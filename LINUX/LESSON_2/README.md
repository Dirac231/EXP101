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

## Security Measures

There are various protections that make the exploitation of a stack overflow more difficult, i am going to briefly discuss what they do leaving the attack details for later, for now the important thing is to underestand the logic behind the defense and the bypass:

- NX Bit
  - Marks the stack as non-executable. You can overwrite the `RIP`, but if you directly place code on the stack it will not be executed. 
  - NX is not really a security measure. Since you can still overwrite the `RIP`, you have full control over the execution flow of the binary, so you can still execute arbitrary code by re-using the binary instructions themselves. You can use both `ROP` and all `RET2` bypasses.
- ASLR
  - ASLR fully randomizes the stack and heap memory. Reaching the `RIP` will be impossible, because register addresses will be random after every execution. It is a OS security measure rather than a binary one. You can check if it's enabled for your OS by looking if the file `/proc/sys/kernel/randomize_va_space` has a value of `2`.
  - Not all memory is fully randomized, however. The addresses of the actual binary components, for example, are still "weakly" randomized (and only if you compiled with `PIE` enabled in `gcc`!), this means they are all _**at the same offset from a "base" random address**_.
  - This is also true for addresses of library functions, for example the native `LIBC` library where the `gets()` and `puts()` functions live. You can abuse this "weak" randomization to reach dangerous functions and execute arbitrary code, these bypasses are called `RET2LIBC`, `RET2GOT`, and `RET2PLT`.
- CANARY
  - A canary is a random address put on the stack, that gets checked prior to a `ret` instruction. If it gets modified (for example during a overflow) the whole execution of the program stops.
  - On linux systems, canaries always end in a null-byte `\x00`, which makes them more guessable, and sometimes you can leak their values through another vulnerability in the code, normally a `STRING FORMAT` or memory leaks of another kind.
- RELRO
  - There are two versions, the "Full RELRO" will resolve every `LIBC` function address at the beginning of execution, saving those addresses in a read-only table called `GOT`. 
  - The `GOT` actually contains a sub-section called `PLT`. This section contains the code that is responsible to actually find the `LIBC` function address and saving that in the `GOT` table. "Full RELRO" makes `RET2GOT` and `RET2PLT` attacks impossible, as you lose write permission in the `GOT`.
  - The "Partial RELRO" is still the default `gcc` option, and allows both bypasses to be used. `LIBC` function addresses are not resolved all at once at the beginning, but dynamically during execution. `PLT` calls are made the first time a library function is encountered, and the `GOT` has a writable, static address.
