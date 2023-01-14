## Debugging

To compile a C source code to a binary, use the `gcc -m64 [file.c] -o binary` command.

## Exercise 1

Consider the following `hello.c` program:
```C
#include <stdio.h>

int main(int argc, char** argv){
  int n = 8;
  char *random = "Hello";
  char *random2 = "Hello again";
  
  puts(random);
  
  return 1;
}
```
- Before starting `gdb`, check the binary with the command `file [binary]`, can you see the architecture?
- Check the loaded library functions with the command `ldd [binary]`, what is the address of `libc.so.6`?
- Start debugging with `gdb [binary]`, then run `disas main` to get the assembly code for the `main()` function.
  - Find the allocation of the variable `n`, notice how `DWORD` is used for integers.
  - Find the allocation of the variables `random` and `random2`, notice how `QWORD` is used for chars.
  - The command `x/s [address]` can display the string content from an address, use it to extract the string value `"Hello"`.
    - You can specify the number of strings to display, starting from a specific address, with `x/[number]s`. Use this to print the "random" and "random2" variables in a single command.
    - You can subtract or add bytes from addresses with `+` or `-` operators, `x/s [adr]+100` will inspect the address at 100 bytes more than `[adr]`.
    - Instead of an address, you can also specify registers, using the "$" symbol before the name (`$esp` or `$rax+100`)
    - Instead of strings, the `x/[number]bx` format can be used to print a certain number of bytes starting from an address.
  - Find the instruction that calls the function `puts()`, how are the argument and return value processed?
  - Find the instruction that returns the value `1` from the `main()`, what register is used for this?

## Exercise 2

Consider the following `fault.c` program:

```C
#include <stdio.h>
#include <string.h>

int fault(char* input){
  char buf[25];
  strcpy(buf, input);
  return 1;
}

int main(int argc, char** argv){
  fault(argv[1]);
  printf("Everything is ok!");
  return 1;
}
```
The program takes an input from the terminal, then calls the `overflow()` function on it, storing the input in the `buf` array.

- Disassemble the `main()` function, how do you know that `argv[1]` is passed to `overflow()`?
- Disassemble the `fault()` function, examine the call to `strcpy()`, where is `buf` allocated?
- Set a "breakpoint" to the `strcpy()` function, with `b strcpy`, this will stop the execution once the function is reached
  - You can display all the breakpoints with `i b`
  - You can delete a breakpoint by its "id" number with `d [id]`
- Run the binary with an input shorter than 25 chars, by using `r [your_input]`, did you hit the `strcpy()` function?
    - Output the first 500 bytes from the top of the stack. Using the `x/[number]bx` command.
    - Inspect the registers with `i r`, find the address of the stack top, and of the next instruction that will execute.
  - Advance by one instruction with the `nexti` command, then continue the execution with `c`
    - The `c` command will continue execution until any other breakpoint is hit.
    - The `j *[address]` command will jump to an address of your choice, and then automatically run `c` from that point.
  - Restart the binary and reset the `strcpy()` breakpoint, run the binary with a input of 200 chars. What happens?
  - Inspect the `RBP` register after the crash, what is its value? Why?

