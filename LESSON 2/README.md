## Stack Overflows

At the end of "Exercise 2" in the first lesson, supplying a string of 200 characters makes the binary go in "segmentation fault".\
\
This happens because the `strcpy()` function doesn't check the length of the input by itself, and blindly allocates 200 chars in the `buf` array, which is only 25 chars long. Our string ends up in unintended memory regions, and a "segfault" error is thrown.\
\
If the input string is long enough, as you've seen, the `RBP` register gets overwritten with it. Since the beginning of the `RBP` is always 8 bytes before the `RIP` address, if we overwrite those 8 bytes with junk data, we are at the right position in memory to write a canonical 48-bit address in the `RIP`.\
\
As the `RIP` holds the value of the next instruction to be executed, being able to do this means to execute arbitrary code on the system. This is called a "stack overflow" vulnerability. To exploit it, we only need to find the right string length (called "offset") to reach the beginning of the `RIP`.\
\
To find this value, we will first overwrite the `RBP` with a non-repeating string of several chunks that `gdb` can generate for us.\
\
Once the `RBP` is overwritten with a portion of this string, we know that this specific portion is unique in the original string, so we can get its exact position in it. We will take this position in bytes, add 8 to it, and that's going to be the offset value.

## Security Measures

There are various protections that make the exploitation of a stack overflow more difficult, i am going to briefly discuss what they do leaving the attack details for later exercises:

- NX Bit
  - When set to `1`, the stack becomes a non-executable region. Meaning that even if you overwrite the `RIP` register, extra code after that register will not get executed. 
  - NX is not really a security measure, overwriting the `RIP` still grants you control over the execution flow of the binary. You can then piece together existing instructions of the binary to achieve code execution. This idea of re-using and chaining together native code pieces is called `ROP` bypass.
- CANARY
  - A canary is a random address put on the stack, that gets checked prior to any function return in the code. If it gets modified (for example during a overflow) the whole execution of the program stops.
  - The only way to bypass a canary, is to leak their values through another vulnerability in the code, like a `format string` memory leak, which we are going to talk about later.
- ASLR + PIE
  - ASLR is a OS-level protection that randomizes the stack and heap memory. Even tough most systems have this enabled, the strength of ASLR critically depends on the `PIE` flag, which is still decided at compilation time. The standard way of testing ASLR is to disable it first, test the binary normally, and then re-enable it to test if the binary is exploitable also with ASLR enabled.
  - If the `PIE` flag is disabled, ASLR is mostly useless. This is because the `GOT` and `PLT` binary segments are not randomized. As we'll see, you can abuse library functions present in these segments to execute code, using a single `puts()` or `printf()` call plus a `ROP` bypass.
  - If the `PIE` flag is enabled, the binary segments are still only "weakly" randomized, meaning that library functions will be _**at the same offset from a "base" random address**_ which is very different from having a truly random memory. If you manage to leak this "base" random address (like you do with canaries), then you can access library functions like before, and execute code via `ROP`.
- Full RELRO
  - This flag makes the `GOT` and `PLT` segments read-only, making attacks that leverage these regions impossible.

Exercises are in the corresponding `.md` file.
