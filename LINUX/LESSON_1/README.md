## Tools Installation
Before continuing, please use this one-line command to install the necessary tools (`gef`,`pip3` and `pwntools`):
```
sudo apt install -y gdb python3 git python3-pip && wget -O ~/.gdbinit-gef.py -q https://gef.blah.cat/py && echo source ~/.gdbinit-gef.py >> ~/.gdbinit && pip3 install pwntools
```

## Theory & Introduction

A "binary" is the compiled version of a code, written in a programming language.\
\
The code is translated via the "compiler" in a sequence of CPU instructions known as "assembly", each instruction corresponding to an elementary CPU operation.\
\
When you compile a code, you can choose the target "architecture" to be 32-bit or 64-bit. The assembly code you get will be slightly different, this is because the architecture decides two things:
- The RAM memory map.
- The CPU registers and their size.

The RAM is the "passive" component, it's the actual piece of hardware where a process data is stored in 0s and 1s. After a binary gets executed, a "memory map" of the RAM is generated (also called "virtual address space"), for 64-bit, it consists of all hex strings from `0x0000000000000000` to `0xffffffffffffffff` called "pointers". To each of these pointers corresponds a value stored in the physical RAM, the CPU uses this "map" to read/write data from the RAM.\
\
As everything is stored in RAM, every assembly instruction of the binary also has a dedicated pointer, if this was not the case, the CPU wouldn't know what instruction to execute next.\
\
The memory map is divided into "stack" and "heap".\
\
The stack is the region used to manage functions. When a function is called, local variables and function arguments are allocated on a "stack frame", get processed by the CPU. After the function returns, the "stack frame" gets freed and can be re-used. The execution then continues from the return address.\
\
Allocation and de-allocation from the stack happen with the "push" and "pop" instructions, which mean "add" or "remove and return" 8 bytes from the top of the stack frame. As these are the only operations you can do on the stack, it's commonly referred as "static" memory, because you have no control over where the allocation happens.\
\
The "heap" is the region used for every other kind of memory management, it's dynamic, meaning that you can allocate, extend, shrink, de-allocate memory for multiple kinds of data, and static/global variables are here.\
\
Contary to 32-bit, not all the (2^64) addresses are utilized for memory mapping. Only "canonical" addresses are used, that is the range `0x0000000000000000` to `0x00007FFFFFFFFFFF` and `0xFFFF800000000000` to `0xFFFFFFFFFFFFFFFF`. Any address outside this range is non-canonical, notice how the canonical ranges are 48-bits long.\
\
To handle the data from the RAM, the CPU uses "registers", which are "hardware" 64-bit addresses (therefore NOT located in RAM), that the CPU uses for specific purposes. The most important ones are:
- RAX: Used to store the return value of functions, and perform temporary operations.
- RSP: Used to store the pointer to the top of the stack.
- RBP: Used to backup the value of ESP before it changes, also known as "base pointer".
- RDI / RSI / RDX / RCX: In order, they store the first 4 arguments of a function.
- RIP: Used to store the pointer to the next assembly instruction. Only accepts a canonical address.

## Assembly Instructions

In the following, the word "object" will be used as an alias for "address", "value" or "register". Each assembly instruction can take at most 2 arguments, the most important ones are:
- `mov    [object_1] [object_2]`
  - In `gbd` notation, moves the **value** contained in "object_2" into "object_1"
- `lea    [object_1] [object_2]`
  - In `gdb` notation, moves the **address** of "object_2" into "object_1", usually in `gdb`, square brackets around "object_2" will indicate that addresses are used.
- `pop    [object]`
  - Removes 8 bytes from the stack top address (kept in the `rsp`) and moves those bytes in `[object]`
- `push   [object]`
  - Pushes `[object]` to the top of the stack (now `rsp` will point to this object address)
- `jmp    [address]`
  - Jumps to a specific address and continues execution from there.
- `call   [function]`
  - Calls a function, first processes the arguments (using `rax` with `rdi`, `rsi`, ...), then pushes the `return` address, then moves the function address into the `rip`. Space for local variables is reserved by "subtracting" the `rsp`, for example `subl esp, 0x8` allocates 8 bytes.
- `ret`    
  - Does a `pop rip` followed by a `jmp [return_address]`, at the end of a function call.

Exercises are in the corresponding `.md` file.
