## Stack Overflows

At the end of "Exercise 2" in the first lesson, supplying a string of 50 characters makes the binary go in "segmentation fault".\
\
This error is thrown whenever a variable accesses memory which was not intended for that variable. In that case, the `buf[]` array was 25 chars long and allocated on the stack, since the length of the input is not checked anywhere, we can specify an arbitrary input length.\
\
A programmer might think that `strcpy()` would check the length of the input string, but he would be wrong, `strcpy()` is considered unsafe for this very reason (had he read the docs, he would have used `strncpy()` instead).\
\
`strcpy()` blindly allocates 50 chars in a 25-long array, a "segfault" is received, and we say that the input string is "overflowing" the stack as that's the memory region where the error is happening.\
\
Think about how dangerous this is for a moment, you can now write any value you want in the stack memory region, including addresses! At the point of the `strcpy()` call, you can still access CPU registers, so what if you overwrite the `RIP` register? What would happen then?\
\
Well, if you think about it, the `RIP` points to the next instruction to be executed, so if you can write an arbitrary address there, you can basically make the binary execute arbitrary code on the system!. This scenario is known as "stack overflow", to prove that it's possible, you only need to do two things:
- Overwrite the `RIP` with an arbitrary address.
- Prove that you can execute arbitrary code, by choosing the right address.
