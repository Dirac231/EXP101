## Stack Overflows

At the end of "Exercise 2" in the first lesson, supplying a string of 50 characters makes the binary go in "segmentation fault".\
\
This error is thrown whenever a variable accesses memory which was not intended for that variable. In the exercise case, the `buf[]` array was 25 chars long and allocated on the stack, since the input string is not checked in any way, we can specify an arbitrary input length.\
\
Trying to allocate 50 chars in a 25 chars array makes the string reach unintended memory regions, thus a "segfault" is received. We say that the input string is "overflowing" the stack, as that's the memory region where the error is happening. Think about how dangerous this is for a moment, you can now write any value you want in the stack memory region, including addresses! At the point of the `strcpy()` call, you can still access CPU registers, so what if you overwrite the `RIP` register? What would happen then?\
\
Well, if you think about it, the `RIP` points to the next instruction to be executed, it doesn't know where this instruction is coming from, obviously, so you can write an arbitrary address there, and execute arbitrary code on the operating system.
