## Stack Overflows

At the end of "Exercise 2" in the first lesson, supplying a string of 50 characters makes the binary go in "segmentation fault".\
\
This happens because the `strcpy()` function doesn't check the length of the input by itself, and blindly allocates 50 chars in the `buf` array, which is only 25 chars long. Our string ends up in unintended memory regions, and a "segfault" error is thrown.\
\
We say that the input string is "overflowing" the stack as that's the memory region where the error is happening.\
\
Think about how dangerous this is, you can now write any value you want in the stack region, including addresses! At the point of the `strcpy()` call, you can still access CPU registers, so what if you overwrite the `RIP` register? What would happen then?\
\
Well, the `RIP` points to the next instruction to be executed, so if you can write an arbitrary address there, you can make the binary execute arbitrary code on the system!\
\
This scenario is known as "stack overflow", to prove that it's possible, you only need to do two things:
- Overwrite the `RIP` with an arbitrary address.
- Prove that you can execute arbitrary code, by choosing the right address.

If you remember, in the `RIP`, only "canonical" 48-bit addresses are accepted, but this is not true for the `RBP`.\
\
In the memory map, the `RBP` is actually immediately below the `RIP`, so overwriting the `RBP` would be a great starting point. Once the input is long enough to reach the beginning of the `RBP`, you first write 8 bytes of junk in it, and then you are right at the beginning of the `RIP`, where only canonical addresses are accepted.\
\
But how do you find the right input length to arrive at the `RBP`? The answer is "De Brujin" sequences, a never-repeating pattern of byte chunks. The length you choose doesn't matter as long as it overflows the stack, because once the `RBP` is overwritten with a portion of this string, we will know exactly where that value is as it never repeats more than once, thus we'll get the amount of bytes we need to reach the `RBP` register, and we win.

## Security Flags

There are various protections that make the exploitation of a stack overflow more difficult.
