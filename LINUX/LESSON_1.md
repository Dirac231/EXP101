## Theory

A binary is the compiled version of a program written in a programming language.\
\
The compiled code is known as "assembly", it can be thought of as the programming language for your CPU. A line of assembly "code" is called "instruction".\
\
When you compile a code, you can choose the "architecture" to be 32-bit or 64-bit. The assembly code you will get is slightly different, this is because the architecture decides two things:
- The RAM memory map.
- The CPU registers and their size.

The RAM is the "passive" component, it's where data is actually being stored as 1s and 0s. The CPU is the "active" part, it basically provides a "map" of this memory (for 32-bit, this will be the collection of all hex strings from `0x00000000` to `0xffffffff`), and a set of operations defined on it.
For example, let's compile the following C program:

```C
#include <stdio.h>

int main(int argc, char** argv){
    printf(argv[1]);
    return 1;
}
```

