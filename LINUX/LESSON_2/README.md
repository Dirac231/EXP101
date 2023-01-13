## Stack Overflows

At the end of "Exercise 2" in the first lesson, supplying a string of 50 characters makes the binary go in "segmentation fault".\
\
This happens because the `strcpy()` function doesn't check the length of the input by itself, and blindly allocates 50 chars in the `buf` array, which is only 25 chars long. Our string ends up in unintended memory regions, and a "segfault" error is thrown.\
\
The string is "overflowing" in the stack. Using this string, you could write an arbitrary address in the `RIP` register, and make the binary execute arbitrary code.
\
\
This scenario is known as "stack overflow", to prove that it's possible, you only need two things:
- Overwrite the `RIP` with an arbitrary address.
- Prove that you can execute arbitrary code.

If you remember, in the `RIP`, only "canonical" 48-bit addresses are accepted, but this is not true for the `RBP`. We can use this information to first overwrite the `RBP`, and since in memory this register is immediately (8 bytes) below the `RIP`, we can then inject a canonical address there.\
\
How do we find the input length to arrive at the `RBP`? The answer is "De Brujin" sequences, a never-repeating pattern of byte chunks that `gdb` can generate for you. The length you choose doesn't matter as long as it overflows the stack, because once the `RBP` is overwritten with a portion of this string, we will know exactly where that value is in the sequence (as it never repeats more than once), thus we'll get the amount of bytes we need to reach the `RBP` register, and we win.

## Security Flags

There are various protections that make the exploitation of a stack overflow more difficult:

- NX Bit
  - Marks the stack as non-executable. You can overwrite the `RIP`, but the code you place on the stack will not get executed by the binary.
- ASLR & PIE
  - ASLR Randomizes the stack and heap memory. Reaching the `RIP` or any other addresses there will be difficult, because they will change after every execution. It depends on the OS, you can check if it's enabled with the command: `cat /proc/sys/kernel/randomize_va_space`, if the output is `2` then ASLR is enabled on your OS.
  - PIE is an additional security measure that only works if ASLR is on. It randomizes addresses found in the binary region. These addresses are outside the stack, and are used to call standard libraries, like `LIBC`, that contains the `gets()` and `puts()`  functions, for example.
