## Tools Installation
Before continuing, please use this one-line command to install the necessary tools for the first lesson:
```
sudo apt install -Y gdb python3-pip && git clone https://github.com/longld/peda.git ~/peda && echo "source ~/peda/peda.py" >> ~/.gdbinit && pip3 install pwntools
```

## Theory & Introduction

A "binary" is the compiled version of a program written in a programming language.\
\
The compiled code is known as "assembly", it can be thought of as the programming language for your CPU. A line of assembly "code" is called "instruction", and corresponds to a single CPU operation.\
\
When you compile a code, you can choose the "architecture" to be 32-bit or 64-bit. The assembly code you will get is slightly different, this is because the architecture decides two things:
- The RAM memory map.
- The CPU registers and their size.

The RAM is the "passive" component, it's where the current process data is actually being stored as 1s and 0s. After a program gets compiled and executed, a "memory map" of the RAM is generated, for 64-bit, it consists of all hex strings from `0x0000000000000000` to `0xffffffffffffffff` also called "pointers". This is essential because the CPU needs to know where data is actually stored to perform operations, to each of these pointers corresponds a value stored in the actual RAM.\
\
The memory map is divided into "stack" and "heap".\
\
The stack is the region used to manage functions. When a function is called, local variables are allocated on the stack, get processed, and when the function returns, the return value also is allocated on the stack. After the return, the "stack frame" which was reserved for the function gets freed, and can be re-used.\
\
Allocation and de-allocation happens with the "push" and "pop" instructions, which mean "add" or "remove and return" 8 bytes from the top of the stack frame. As these are the only operations you can do on the stack, it's commonly referred as "static" memory, because you have no control over where the allocation happens.\
\
The "heap" is the region used for every other kind of memory management, it's dynamic, meaning that you can allocate, extend, shrink, de-allocate memory for multiple kinds of data, and static/global variables are here.\
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
\
To complete the lesson, please complete all exercises in the corresponding `.md` file.
