## Tools Installation
Before continuing, please use this one-line command to install the necessary tools for the first lesson:
```
sudo apt install -Y gdb python3-pip && git clone https://github.com/longld/peda.git ~/peda && echo "source ~/peda/peda.py" >> ~/.gdbinit && pip3 install pwntools
```

## Theory & Introduction

A "binary" is the compiled version of a code, written in a programming language.\
\
The code is translated via the "compiler" in a sequence of CPU instructions known as "assembly", each instruction corresponding to an elementary CPU operation.\
\
When you compile a code, you can choose the target "architecture" to be 32-bit or 64-bit. The assembly code you get will be slightly different, this is because the architecture decides two things:
- The RAM memory map.
- The CPU registers and their size.

The RAM is the "passive" component, it's where a process data is physically stored. After a binary gets executed, a "memory map" of the RAM is generated, for 64-bit, it consists of all hex strings from `0x0000000000000000` to `0xffffffffffffffff` also called "pointers". To each of these pointers corresponds a value stored in the physical RAM, the CPU uses this "map" to read/write data from the RAM.\
\
As everything is stored in RAM, every assembly instruction of the binary also has a dedicated pointer, if this was not the case, the CPU wouldn't know what instruction to execute next.\
\
The memory map is divided into "stack" and "heap".\
\
The stack is the region used to manage functions. When a function is called, local variables and function arguments are allocated on a "stack frame", get processed by the CPU, and when the function returns, the return value also is allocated on the stack and can be processed. After the return, the "stack frame" which was reserved for the function gets freed, and can be re-used. The execution then continues from the return address.\
\
Allocation and de-allocation from the stack happen with the "push" and "pop" instructions, which mean "add" or "remove and return" 8 bytes from the top of the stack frame. As these are the only operations you can do on the stack, it's commonly referred as "static" memory, because you have no control over where the allocation happens.\
\
The "heap" is the region used for every other kind of memory management, it's dynamic, meaning that you can allocate, extend, shrink, de-allocate memory for multiple kinds of data, and static/global variables are here.\
\
Contary to 32-bit, not all the (2^64) addresses are utilized for memory mapping. Only "canonical" addresses are used, that is the range `0x0000000000000000` to `0x00007FFFFFFFFFFF` and `0xFFFF800000000000` to `0xFFFFFFFFFFFFFFFF`. Any address outside this range is non-canonical.\
\

\
To handle the data received from the RAM, the CPU uses "registers", which are also 64-bit addresses NOT located in RAM, that the CPU uses for specific purposes. The most important ones are:
- RAX: Used to store the return value of functions, and perform temporary operations.
- RSP: Used to store the pointer to the top of the stack.
- RBP: Used to backup the value of ESP before it changes, also known as "base pointer".
- RDI / RSI / RDX / RCX: In order, they store the first 4 arguments of a function.
- RIP: Used to store the pointer to the next assembly instruction. Only accepts a canonical address.
\
\
To complete the lesson, please complete all exercises in the corresponding `.md` file.
