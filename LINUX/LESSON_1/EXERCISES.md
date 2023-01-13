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
    - You can specify the number of strings to display, starting from a specific address, with `x/[number]s`. Use this to print the "random" and "random2" variables in a single command.
    - You can subtract or add bytes from addresses with `+` or `-` operators, `x/s [adr]+100` will inspect the address at 100 bytes more than `[adr]`.
    - Instead of an address, you can also specify registers, using the "$" symbol before the name (`$esp` or `$rax+100`)
    - Instead of strings, the `x/[number]bx` format can be used to print a certain number of bytes starting from an address.
    - Access the 500 bytes from the top of the stack, do you get a output? Why is that?
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
  - You can delete a breakpoint by its "id" number with `d [id]`
- You can run the binary with an input using `r [your_input]`, did you hit the breakpoint? At which address?
    - Output the first 500 bytes from the top of the stack. Can you get the output this time? Why?
    - Inspect a register of your choice with `i r [register]`, with this you could find the address of the next instruction that will execute, how?.
    - You can inspect all registers at once with `i r`, get the values of `rbp` and `rsp` in a single shot.
  - Advance by one instruction with the `nexti` command, then continue the execution with `c`
    - The `c` command will continue execution until any other breakpoint is hit.
    - The `j *[address]` command will directly jump to an address of your choice, and then automatically run `c` from that point.
  - Delete all breakpoints, and re-run the binary with an input of 50 chars, what happens after `nexti`? Why?

