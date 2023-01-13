## Stack Overflows

At the end of "Exercise 2" in the first lesson, supplying a string of 50 characters makes the binary go in "segmentation fault". This error appears whenever you access a region of memory which was not allocated for you. In the exercise case, the `buf[]` array was 25 chars long and allocated on the stack, since the input string is not checked in any way, we can specify an arbitrary length, and trying to allocate 50 chars in a 25 chars array makes your input string reach unintended memory regions, thus a "segfault" is received.
