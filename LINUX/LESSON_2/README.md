## Stack Overflows

At the end of "Exercise 2" in the first lesson, supplying a string of 50 characters makes the binary go in "segmentation fault".\
\
This happens because the `strcpy()` function doesn't check the length of the input by itself, and blindly allocates 50 chars in the `buf` array, which is only 25 chars long. Our string ends up in unintended memory regions, and a "segfault" error is thrown.\
\
As the input string is "overflowing" in the stack, if the string is long enough you could write an arbitrary address in the `RIP` register, and make the binary execute any instruction you want. This is a "stack overflow" vulnerability.\
\
Knowing that the `RBP` is 8 bytes below the `RIP`, the strategy is to first find the string length to reach the `RBP` (also called "offset length"), add 16 bytes to reach the `RIP`, and finally write an arbitrary canonical 48-bit address there.\
\
How do we find the input length to arrive at the `RBP`? The answer is "De Brujin" sequences, a never-repeating pattern of byte chunks that `gdb` can generate for you. The length you choose doesn't matter as long as it overflows the stack.\
\
Once the `RBP` is overwritten with a portion of this string, we will know exactly where that value is in the sequence (as it never repeats more than once), thus we'll get the amount of bytes we need to reach the `RBP` register, and we can perform the attack.

## Security Flags

There are various protections that make the exploitation of a stack overflow more difficult, i am going to briefly discuss what they do and how to bypass them:

- NX Bit
  - Marks the stack as non-executable. You can overwrite the `RIP`, but if you directly place code on the stack it will not be executed. 
  - As you can still overwrite the `RIP`, you have full control over the execution flow of the binary, what if you can generate arbitrary code by re-using the binary instructions themselves? This is called `ROP` bypass.
- ASLR
  - ASLR randomizes the stack and heap memory. Reaching the `RIP` or any other addresses in that memory region will be difficult, because they will be random after every execution. It is a OS security measure, you can check if it's enabled with the command: `cat /proc/sys/kernel/randomize_va_space`, if the output is `2` then ASLR is enabled.
  - When ASLR is enabled, not all the memory is fully randomized, however. The addresses of the actual binary components, for example, are still "relatively" randomized (and only if the `PIE` flag of `gcc` was used at compilation) which means they are all _**at the same offset from a "base" random address**_. Crucially, this is also true for addresses of shared libraries that the code uses, for example `LIBC` where the `gets()` and `puts()` functions live. What if you abuse this "relative" randomization to reach dangerous functions and execute arbitrary code? These bypasses are called `RET2LIBC`, `RET2GOT`, and `RET2PLT`.
- CANARY
  - A canary is a random address put on the stack, that gets checked prior to a `ret` instruction. If it gets tampered/overwritten (for example during a overflow) the whole execution of the program stops.
  - On linux systems, canaries always end in a null-byte `\x00`, which makes them more guessable, and sometimes you can leak their values through another vulnerability in the code, normally a `STRING FORMAT` bypass or memory leaks of another kind.
