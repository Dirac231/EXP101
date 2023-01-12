## Theory

A binary is the compiled version of a program written in a programming language.\
\
The compiled code is known as "assembly", it can be thought of as the programming language for your CPU. A line of assembly "code" is called "instruction".\
\
When you compile a code, you can choose the "architecture" to be 32-bit or 64-bit. For the rest of the notes, we will always use 32-bit architectures, because they are still actively supported, and easier to exercise on. The assembly code you will get is slightly different, this is because the architecture decides two things:
- The RAM memory map.
- The CPU registers and their size.



The RAM is the "passive" component, it's where the current process data is actually being stored as 1s and 0s.\
\
The CPU is the "active" part, it basically provides a "representation" of this memory (for 32-bit, the collection of all hex strings from `0x00000000` to `0xffffffff`), divides it into "stack" and "heap", and defines operations on it.\
\
To read and write data from this "map", the CPU uses "registers", which are 4-byte addresses NOT located in RAM, that the CPU uses for specific purposes. The most important ones are:
- EAX: Used to store the return value of functions, and perform operations on variables.
- ESP: Used to store the pointer to the top of the "stack".
- EBP: Used to store the pointer to the "bottom" of the stack.
- EDI / ESI / EDX / ECS: In order, they store the first 4 arguments of a function.
- EIP: Used to store the pointer to the next assembly instruction.

For example, let's compile the following C program:

```C
#include <stdio.h>

int main(int argc, char** argv){
    printf(argv[1]);
    return 1;
}
```

