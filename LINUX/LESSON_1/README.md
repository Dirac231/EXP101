## Tools Installation
Before continuing, please use this one-line command to install the necessary tools (`gef`,`pip3` and `pwntools`):
```
sudo apt install -y gdb python3 git python3-pip && wget -O ~/.gdbinit-gef.py -q https://gef.blah.cat/py && echo source ~/.gdbinit-gef.py >> ~/.gdbinit && pip3 install pwntools
```

## Binaries & Memory

A "binary" is the compiled version of a code, written in a programming language.\
\
The code is translated via the "compiler" in a sequence of CPU instructions known as "assembly", each instruction corresponding to an elementary CPU operation.\
\
When you compile a code, you can choose the target "architecture" to be 32-bit or 64-bit. We will work always with 64-bit, the architecture decides two things:
- The RAM memory map.
- The CPU registers and their size.

The RAM is the actual piece of hardware where a process data is stored in 0s and 1s. After a binary gets executed, a "memory map" of the RAM is generated (also called "virtual address space"). For 64-bit, it consists of all hex strings from `0x0000000000000000` to `0xFFFFFFFFFFFFFFFF` called "pointers". To each of these pointers corresponds a value stored in the physical RAM, and the CPU uses this "map" to read/write data from the RAM.\
\
Contrary to 32-bit, not all the (2^64) addresses are utilized for memory mapping. Only "canonical" addresses are used, that is the range `0x0000000000000000` to `0x00007FFFFFFFFFFF` and `0xFFFF800000000000` to `0xFFFFFFFFFFFFFFFF`. Any address outside this range is non-canonical, notice how they are 48-bits long.\
\
As everything is stored in RAM, an assembly instruction itself also has a dedicated pointer, if this was not the case, the CPU wouldn't know what instruction to execute next.\
\
The memory map is divided into address chunks called "stack" and "heap". These areas are further divided into sub-regions called "binary segments", this concept is important because the binary segments get treated differently by some security measures, and are the starting point for some of the advanced attacks.\
\
The stack is the region used to manage functions. Local variables, arguments, return values, return addresses are all stored in a "stack frame" when a function is called. The CPU can only perform two operations on the stack, the "push" and "pop" instructions, which mean "add" or "remove" 8 bytes from the top address. For this reason, the stack is called "static" memory, because you have no control over where the allocation happens.\
\
The "heap" is the region used for every other kind of memory management, it's dynamic, meaning that you can allocate, extend, shrink, de-allocate memory for multiple kinds of data, and static/global variables are here.\
\
The "binary segments" are the sub-regions of the stack and heap containing the variables and functions used by the binary. The important ones are the `GOT` table, the `PLT`, and the `SO` local files. The `SO` files contain the code for the extra library functions, like the native functions `gets()` or `puts()`. One example is `/lib/i386-linux-gnu/libc-2.27.so` found in your linux system.\
\
Even if you don't declare them, the binary always knows where native functions are, thanks to the "linker", a small piece of code found in the `PLT` section that looks for these functions in the `SO` files. Once found, the corresponding address is saved in the `GOT` table. When the binary needs to call `gets()`, it searches the `GOT` table to find the correct address.\
\
After this whole process, the `SO` file is part of the binary memory, saved in the `GOT` section, this is why we use the word "linker".

## Registers & Assembly

To handle the memory map, the CPU uses "registers", which are "hardware" 64-bit addresses (therefore NOT located in RAM), that the CPU uses for specific purposes, for example to copy values from the memory map, and perform operations on it.\
\
The important ones are:
- RAX: Used to store the return value of functions, and perform temporary operations.
- RSP: Used to store the pointer to the top of the stack.
- RBP: Used to backup the value of ESP before it changes, also known as "base pointer".
- RDI / RSI / RDX / RCX: In order, they store the first 4 arguments of a function.
- RIP: Used to store the pointer to the next assembly instruction. Only accepts a canonical address.

The code is compiled into assembly instructions, each taking at most 2 arguments, that operate between these registers and the RAM addresses. The most important instructions are:
- `mov    [address_1] [address_2]`
  - In `gbd` notation, moves the **value** contained in `[address_2]` into `[address_1]`
- `lea    [address_1] [value_2]`
  - In `gdb` notation, moves the **address** of `[value_2]` into `[address_1]`. In `gdb`, square brackets will indicate that the address is being taken instead of the value.
- `pop    [address]`
  - Removes 8 bytes from the stack top address (kept in the `rsp`) and moves those bytes in `[address]`
- `push   [address]`
  - Pushes `[address]` to the top of the stack (`rsp` will therefore point to this address)
- `jmp    [address]`
  - Jumps to a specific address and continues execution from there.
- `call   [function address]`
  - Calls a function. Before this line, assembly first processes the arguments (using `rax` with `rdi`, `rsi`, ...), pushes the `return` address, and moves the function address into the `rip`. Local variables are allocated by "subtracting" the `rsp`, for example `subl esp, 0x8` allocates 8 bytes.
- `ret`    
  - Does a `pop rip` followed by a `jmp [return_address]`, at the end of a function call.

It is strongly recommended to complete the exercises in `EXERCISES.md` before moving on.
