## Debugging

To compile a C source code to a binary, use the `gcc -m64 [file.c] -o binary` command. \
\
It is also possible to choose the 32-bit architecture, but these notes are all written for 64-bit. Once you have the binary, start the debugging process with the command `gdb binary`.

## Exercise 1

Consider the following `hello.c` program:
```C
#include <stdio.h>

int main(int argc, char** argv){
  int n = 8;
  char *hello = "Hello";
  
  puts(y);
  return 1;
}
```
- From `gdb`, run the command `disas main` to get the assembly code for the `main()` function.
  - Find the instruction that allocates the integer `n`, notice how `DWORD` is used to reference integers.
  - Find the instruction that allocates the string `hello`, notice how `QWORD` is used and that gdb outputs in white the string address (`# 0x40...`)
  - The command `x/s [address]` can display the string content of an address, use it to extract the string value `"Hello"`.
  - Do the same using `x/bx [address]`, this is called "hex dump" output. Decode the hex output to a string, what do you get?
  - Find the instruction that calls the function `puts()`, where are the argument and return value stored?
  - Find the instruction that returns the value `1` from the `main()`, what register is used for this?
