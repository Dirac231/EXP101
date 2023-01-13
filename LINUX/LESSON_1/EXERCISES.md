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
  char *random2 = "Hello again";
  
  puts(y);
  return 1;
}
```
- From `gdb`, run the command `disas main` to get the assembly code for the `main()` function.
  - Find the instruction that allocates the variable `n`, notice how `DWORD` is used for integers.
  - Find the instruction that allocates the variable `random`, notice how `QWORD` is used for chars.
    - Do the same for the string `random2`, where is it allocated?
  - The command `x/s [address]` can display the string content from an address, use it to extract the string value `"Hello"`.
    - You can specify the number of strings to display, starting from a specific address, with `x/[number]s`. Output "random" and "random2" in a single command.
    - You can subtract or add bytes from registers with `+` or `-` operators, like "x/s [adr]+100" will inspect the address at 100 bytes more than [adr].
    - Instead of an address, you can also specify registers, using the "$" symbol before the name (`$esp` or `$rax+100`)
    - Try to access the `$esp-500` value, do you get a value? Why is that?
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
- Run the binary supplying an input with `r [your_input]`, did you hit the breakpoint? At which address?
  - In the "x" command, instead of using "x/s" you can use the "x/bx" format, called "hex dump" format.
    - Output the first 500 hex bytes from the top of the stack. Why can you get the output this time?
  - Advance by one instruction with the `nexti` command, then continue the execution with `c`
  - Do the same, but supply an input longer than 25 chars, what happens after `nexti`? Why?

