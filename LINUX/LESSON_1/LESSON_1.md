## Tools Installation
Before continuing, please use this one-line command to install the necessary tools for the first lesson:
```
sudo apt install -Y gdb python3-pip && git clone https://github.com/longld/peda.git ~/peda && echo "source ~/peda/peda.py" >> ~/.gdbinit && pip3 install pwntools
```

## Debugging and Assembly

A "binary" is the compiled version of a program written in a programming language.\
\
The compiled code is known as "assembly", it can be thought of as the programming language for your CPU. A line of assembly "code" is called "instruction", and corresponds to a single CPU operation.\
\
When you compile a code, you can choose the "architecture" to be 32-bit or 64-bit. The assembly code you will get is slightly different, this is because the architecture decides two things:
- The RAM memory map.
- The CPU registers and their size.

The RAM is the "passive" component, it's where the current process data is actually being stored as 1s and 0s.\
\
The CPU is the "active" part, it both provides a "map" of this memory space (for 64-bit, consisting of all hex strings from `0x0000000000000000` to `0xffffffffffffffff`), and defines operations on it. Each of these hex strings "points" to a specific region in the RAM, so that the processor knows where to find data.\
\
The memory map is divided into "stack" and "heap".\
\
The stack is the region used to manage functions. When a function is called, local variables are allocated on the stack, get processed, and when the function returns, the return value also is allocated on the stack. After the return, the "stack frame" which was reserved for the function gets freed, and can be re-used.\
Allocation and de-allocation happens with the "push" and "pop" instructions, which mean "add" or "remove and return" 8 bytes from the top of the stack frame.\
\
Contary to 32-bit, not all the (2^64) addresses are utilized for memory mapping. Only "canonical" addresses are used, that is the range `0x0000000000000000` to `0x00007FFFFFFFFFFF` and `0xFFFF800000000000` to `0xFFFFFFFFFFFFFFFF`. Any address outside this range is non-canonical.\
\
Keep in mind that as everything is stored in RAM, the assembly instruction themselves have assigned pointers too, if this was not the case, the CPU wouldn't know what instruction to execute next.\
\
To handle the data received from the RAM, the CPU uses "registers", which are also 64-bit addresses NOT located in RAM, that the CPU uses for specific purposes. The most important ones are:
- RAX: Used to store the return value of functions, and perform temporary operations.
- RSP: Used to store the pointer to the top of the stack.
- RBP: Used to backup the value of ESP before it changes, also known as "base pointer".
- RDI / RSI / RDX / RCX: In order, they store the first 4 arguments of a function.
- RIP: Used to store the pointer to the next assembly instruction. Only accepts a canonical address.

To compile a C source code to binary, use the `gcc -m64 [file.c] -o binary` command. It is also possible to choose the 32-bit architecture, but these notes are all written for 64-bit. Once you have the binary, start the debugging process with the command `gdb binary`.\
\
To complete the lesson, please complete all exercises in the corresponding `.md` file.
