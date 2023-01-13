## Stack Overflows

At the end of "Exercise 2" in the first lesson, supplying a string of 50 characters makes the binary go in "segmentation fault".\
\
This error is thrown whenever a variable accesses memory which was not intended for that variable. In the exercise case, the `buf[]` array was 25 chars long and allocated on the stack, since the input string is not checked in any way, we can specify an arbitrary input length.\
\
Trying to allocate 50 chars in a 25 chars array makes the string reach unintended memory regions, thus a "segfault" is received.
