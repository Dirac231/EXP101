## Tools Installation
Before continuing, please use this one-line command to install the necessary tools for the first lesson:
```
sudo apt install -Y gdb python3-pip && git clone https://github.com/longld/peda.git ~/peda && echo "source ~/peda/peda.py" >> ~/.gdbinit && pip3 install pwntools
```

## Debugging and Assembly

A "binary" is the compiled version of a program written in a programming language.\
\
The compiled code is known as "assembly", it can be thought of as the programming language for your CPU. A line of assembly "code" is called "instruction".\
\
When you compile a code, you can choose the "architecture" to be 32-bit or 64-bit. For the rest of the notes, we will always use 32-bit architectures, because they are still actively supported, and easier to exercise on. The assembly code you will get is slightly different, this is because the architecture decides two things:
- The RAM memory map.
- The CPU registers and their size.

The RAM is the "passive" component, it's where the current process data is actually being stored as 1s and 0s.\
\
The CPU is the "active" part, it basically provides a "representation" of this memory (for 32-bit, the collection of all hex strings from `0x00000000` to `0xffffffff`), divides it into "stack" and "heap", and defines operations on it. Each of these hex strings "points" to a specific region in the RAM, so that the processor knows where to find data.\
\
Keep in mind that as everything is stored in RAM, the compiled assembly instruction themselves have assigned pointers too.\
\
To read and write data from this "map", the CPU uses "registers", which are also 32-bit addresses NOT located in RAM, that the CPU uses for specific purposes. The most important ones are:
- EAX: Used to store the return value of functions, and perform operations on variables.
- ESP: Used to store the pointer to the top of the "stack".
- EBP: Used to store the pointer to the "bottom" of the stack.
- EDI / ESI / EDX / ECS: In order, they store the first 4 arguments of a function.
- EIP: Used to store the pointer to the next assembly instruction.

To compile a C source code to a 32-bit linux binary, use the `gcc -m32 [file.c] -o binary` command. Then, start the debugging process with the command `gdb binary`.\
\
To complete the lesson, please complete all exercises in the corresponding `.md` file.
