## Debugging

To compile a C source code to a binary, use the `gcc -m64 [file.c] -o binary` command. \
\
Once you have the binary, start the debugging process with the command `gdb binary`.

## Exercise 1

Consider the following `hello.c` program:
```C
#include <stdio.h>

int main(int argc, char** argv){
  int n = 8;
  char *random = "Hello";
  
  puts(y);
  return 1;
}
```
- From `gdb`, run the command `disas main` to get the assembly code for the `main()` function.
  - Find the instruction that allocates the variable `n`, notice how `DWORD` is used for integers.
  - Find the instruction that allocates the variable `random`, notice how `QWORD` is used for chars. 
  - The command `x/s [address]` can display the string content of an address, use it to extract the string value `"Hello"`.
    - You can subtract or add addresses together, as well as bytes from addresses.
    - Instead of an address, you can also reference registers, using the "$" symbol and the name (`x/s $esp`)
  - The "bx" format, with `x/bx [address]`, is called "hex dump" output. Output the first 500 bytes from the top of the stack.
  - Find the instruction that calls the function `puts()`, where are the argument and return value stored?
  - Find the instruction that returns the value `1` from the `main()`, what register is used for this?

## Exercise 2

Consider the following `overflow.c` program:

```C
#include <string.h>

int overflow(char* input){
  char buf[25];
  strcpy(buf, input);
  return 1;
}

int main(int argc, char** argv){
  overflow(argv[1]);
  printf("Everything is ok!");
  return 1;
}
```
The program takes an input from the terminal, then calls the `overflow()` function on it, storing the input in the `buf` array.

- Disassemble the `main()` function, how do you know that `argv[1]` is passed to `overflow()`?
- Disassemble the `overflow()` function, examine the call to `strcpy()`, where is `buf` allocated?
- Set a "breakpoint" to the `overflow()` function, with `b overflow`, this will stop the execution once the function is reached
  - You can display all the breakpoints with `i b`
  - You can delete a breakpoint by id with `d [id]`
- Run the binary supplying an input with `r [your_input]`, do you hit the breakpoint?
  - Advance by one instruction with the `nexti` command, then continue the execution with `c`
  - Do the same, but supply an input longer than 25 chars, what happens after `nexti`? Why?

