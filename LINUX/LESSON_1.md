## Theory

A binary is the compiled version of a program written in a programming language.\
\
The compiled code is known as "assembly", it can be thought of as the programming language for your CPU. A line of assembly "code" is called "instruction".\
\
When you compile a code, you can choose the "architecture" to be 32-bit or 64-bit. The assembly code you will get is slightly different, this is because the architecture decides two things:
- The CPU registers and their size.
- The RAM memory layout.\

For example, compare the following C program to its 64-bit compiled version:
```C
#include <stdio.h>

int main(int argc, char** argv){
    printf("Hello");
    return 1;
}
```

