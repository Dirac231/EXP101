## Stack Overflows

At the end of "Exercise 2" in the first lesson, supplying a string of 200 characters makes the binary go in "segmentation fault".\
\
This happens because the `strcpy()` function doesn't check the length of the input by itself, and blindly allocates 200 chars in the `buf` array, which is only 25 chars long. Our string ends up in unintended memory regions, and a "segfault" error is thrown.\
\
If the input string is long enough, as you've seen, the `RBP` register gets overwritten with it. Since the beginning of the `RBP` is always 16 bytes before the `RIP` address, once we overwrite it with 8 bytes of junk data, we are at the right position in memory to write a canonical 48-bit address in the `RIP`.\
\
As the `RIP` holds the value of the next instruction to be executed, being able to do this means to execute arbitrary code on the system. This is called a "stack overflow" vulnerability. To exploit it, we only need to find the right string length (called "offset") to reach the beginning of the `RBP`.
\
To find this value, we will overwrite the `RBP` with a non-repeating string of several chunks that `gdb` can generate for us.\
\
Once the `RBP` is overwritten with a portion of this string, we know for sure that this specific portion is unique in the original string, so we can get its exact position in it. This position expressed in amount of bytes is exactly the offset value.

## Security Measures

There are various protections that make the exploitation of a stack overflow more difficult, i am going to briefly discuss what they do leaving the attack details for later, for now the important thing is to underestand the logic behind the defense and the bypass:

- NX Bit
  - Marks the stack as non-executable. You can overwrite the `RIP`, but if you directly place code on the stack it will not be executed. 
  - NX is not really a security measure. Since you can still overwrite the `RIP`, you have full control over the execution flow of the binary, so you can still execute arbitrary code by re-using native binary functions. 
  - For reference, this idea of re-using binary functions is called `ROP` bypass, it's a larger class of bypasses that include all the `RET2` bypasses.
- CANARY
  - A canary is a random address put on the stack, that gets checked prior to a `ret` instruction. If it gets modified (for example during a overflow) the whole execution of the program stops.
  - The only way to bypass a canary, is to leak their values through another vulnerability in the code, like a `STRING FORMAT` bypass which we are going to talk about later on.
- ASLR
  - ASLR fully randomizes the stack and heap memory. Reaching the `RIP` will be impossible, because register addresses will be random after every execution. It is actually a OS security measure, as such, you can first try to exploit the binary without it, and then re-enable it to try and bypass ASLR too. 
  - By default, `gdb` will make you debug every binary without ASLR. For the extra bypass challenge, you can enable ASLR with `set disable-randomization off`.
  - Not all memory is fully randomized by ASLR. The addresses of binary components and library functions are still "weakly" randomized, at worst they are all _**at the same offset from a "base" random address**_. You can abuse this "weak" randomization to reach dangerous functions and execute arbitrary code, these bypasses are called `RET2LIBC`, `RET2GOT`, and `RET2PLT`.
- RELRO
  - There are two versions, "Full" and "Partial" (which is the default in the latest `gcc`)
  - "Full RELRO" will resolve every library function address at the beginning of execution, and makes the `GOT` table read-only. This makes `RET2GOT` and `RET2PLT` attacks impossible, as you lose write permission in both regions.
  - "Partial RELRO" is not a problem and allows both bypasses to be used. Library function addresses are resolved dynamically and `PLT` calls are made the first time a function is encountered. Both `GOT` and `PLT` sections remain writable.

Exercises are in the corresponding `.md` file.
