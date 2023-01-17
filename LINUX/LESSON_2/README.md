## Stack Overflows

At the end of "Exercise 2" in the first lesson, supplying a string of 200 characters makes the binary go in "segmentation fault".\
\
This happens because the `strcpy()` function doesn't check the length of the input by itself, and blindly allocates 200 chars in the `buf` array, which is only 25 chars long. Our string ends up in unintended memory regions, and a "segfault" error is thrown.\
\
If the input string is long enough, as you've seen, the `RBP` register gets overwritten with it. Since the beginning of the `RBP` is always 16 bytes before the `RIP` address, once we overwrite it with 8 bytes of junk data, we are at the right position in memory to write a canonical 48-bit address in the `RIP`.\
\
As the `RIP` holds the value of the next instruction to be executed, being able to do this means to execute arbitrary code on the system. This is called a "stack overflow" vulnerability. To exploit it, we only need to find the right string length (called "offset") to reach the beginning of the `RBP`.\
\
To find this value, we will overwrite the `RBP` with a non-repeating string of several chunks that `gdb` can generate for us.\
\
Once the `RBP` is overwritten with a portion of this string, we know for sure that this specific portion is unique in the original string, so we can get its exact position in it. This position expressed in amount of bytes is exactly the offset value.

## Security Measures

There are various protections that make the exploitation of a stack overflow more difficult, i am going to briefly discuss what they do leaving the attack details for later, for now the important thing is to underestand the logic behind the defense and the bypass:

- NX Bit
  - Marks the stack as non-executable. You can overwrite the `RIP`, but if you directly place instructions on the stack they will not get executed. 
  - NX is not a security measure, because overwriting the `RIP` still grants you control over the execution flow of the binary. Instead of placing extra code on the stack, you can simply re-use native binary functions. This idea is called `ROP` bypass, it's a larger class of bypasses that include all the `RET2` bypasses.
- CANARY
  - A canary is a random address put on the stack, that gets checked prior to any function return in the code. If it gets modified (for example during a overflow) the whole execution of the program stops.
  - The only way to bypass a canary, is to leak their values through another vulnerability in the code, like a `format string` memory leak, which we are going to talk about extensively.
- ASLR
  - ASLR fully randomizes the stack and heap memory. Reaching the `RIP` will be impossible, because register addresses will be random after every execution. It is actually a OS security measure, as such, you can first try to exploit the binary without it, and then enable it again, to see if a bypass is possible. By default, `gdb` will debug without ASLR, to re-enable it run: `set disable-randomization off`.
  - Not all memory is fully randomized by ASLR. The binary segments outside the `SO` section are still "weakly" randomized (and only if the binary has the `PIE` flag enabled!), this means they will be all _**at the same offset from a "base" random address**_ which is very different from being truly random. Once you leak this "base" random address (like with canaries) and calculate a single offset value, you are back in the `ROP` and `RET2` bypasses territory.
- RELRO
  - There are two versions, "Full" and "Partial" (which is the default in the latest `gcc`)
  - "Full RELRO" will resolve every library function address at the beginning of execution, and makes the `GOT` table read-only. This makes `RET2GOT` and `RET2PLT` attacks impossible, as you lose write permission in both regions.
  - "Partial RELRO" is not a problem and allows both bypasses to be used. Library function addresses are resolved dynamically and `PLT` calls are made the first time a function is encountered. Both `GOT` and `PLT` sections remain writable.

Exercises are in the corresponding `.md` file.
