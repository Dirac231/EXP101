## Exercise 1

Consider the following `try.c` program, that just declares an empty `main()`:
```C
#include <stdio.h>

int main(int argc, char** argv){
  int x = 8;
  char *y = "Hello";
  
  puts(y);
  return 1;
}
```
- Compile the source code for 32-bit, and open both binaries with `gdb`
- Run the command `disas main` to get the assembly code for the `main()` function.
  - What are the instructions to define the integer `x`? What about the string `y`?
  - Where is the function `puts()` called? Where is the argument stored? What about the return value?
  - Where is the `return 1` instruction?
