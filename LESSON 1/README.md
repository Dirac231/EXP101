## Tools Installation
Before continuing, please use this one-line command to install the necessary tools (`gcc`,`gef`,`pip3`,`ropper` and `pwntools`):
```
sudo apt install -y build-essential gcc gdb python3 git python3-pip && wget -O ~/.gdbinit-gef.py -q https://gef.blah.cat/py && echo source ~/.gdbinit-gef.py >> ~/.gdbinit && pip3 install pwntools ropper
```

## Binaries & Memory

A "binary" is the compiled version of a code, written in a programming language. Some languages are "interpreted", meaning that the code gets compiled line-by-line during execution, so that you often don't have a binary to execute. We are interested in the other category, that consists of "compiled" languages, like C for example, in which compilation produces an executable file.\
\
We will always either take a C program or a direct binary to train exploitation techniques later on.\
\
The C code is translated via the "compiler" in a sequence of CPU instructions known as "assembly" code, each instruction corresponding to an elementary CPU operation.\
\
When you compile a code, you can choose the target "architecture" to be 32-bit or 64-bit. This decides two things:
- The RAM memory map.
- The CPU registers, their size, and the assembly language used to manipulate them.

The RAM is the actual piece of hardware where a process data is stored in 0s and 1s. After a binary gets executed, a "memory map" of the RAM is generated (also called "virtual address space").\
\
For 64-bit, it consists of all hex strings from `0x0000000000000000` to `0xFFFFFFFFFFFFFFFF` called "pointers". To each of these pointers corresponds a value stored in the physical RAM, and the CPU uses this "map" to read/write data from the RAM, using "registers" as input/output channels.\
\
Contrary to 32-bit, not all the (2^64) addresses are utilized for memory mapping. Only "canonical" addresses are used, that is the range `0x0000000000000000` to `0x00007FFFFFFFFFFF` and `0xFFFF800000000000` to `0xFFFFFFFFFFFFFFFF`. Any address outside this range is "non-canonical" and therefore irrelevant for our purposes, notice how they have 48 bits of entropy.

## Stack, Heap, Segments
The memory map can be divided into three important areas:
- Stack
- Heap
- Segments

The `stack` is the region used to manage functions. When a function is called, a memory region called "stack frame" is reserved for it, containing the following:
- Function address
- Argument values
- Local variables
- Return value and address

Certain CPU registers are reserved especially to store values/pointers of stack frame objects, this allows us to easily identify function calls in the assembly code. Once a stack frame is created, it can't be extended or reduced anymore, this is why the stack is referred to as "static" memory.\
\
The `heap` is the region used for every other kind of memory management, it's dynamic, meaning that you can allocate, extend, shrink, de-allocate memory, it contains the following:
- Static variables
- Global variables
- Memory chunks (e.g. created with `malloc`)

The `segments` are specific sub-regions of the memory. For exploitation purposes, the important ones are those responsible for loading library functions in the code:
- SO Files
- PLT Section
- GOT Table

The `SO` files contain the code for library functions, like `gets()`. An example file is `/lib/i386-linux-gnu/libc-2.27.so` found in most linux systems.\
\
When a call to a library function is executed, a piece of code contained in the `PLT` section looks for its address in the `.so` files, this address gets saved in the `GOT` table, which is part of the binary memory, so that it knows where to find the function. This whole process can occur at runtime, or at compilation time, with some security implications.

## CPU Registers, Assembly

To perform read/write operations on the RAM memory map, the CPU uses "registers", which are also represented by 64-bit addresses, altough not part of the RAM. \
\
The important ones are:
- `RSP`: Stores the pointer to the top address of a stack frame.
- `RBP`: Backups the pointer contained in `ESP`.
- `RDI` / `RSI` / `RDX` / `RCX`: In order, they store the first 4 argument values of a function.
- `RAX`: Stores the return value of a function, and temporary addresses.
- `RIP`: Stores the return address of a function.

When the code is compiled, assembly instructions are generated, that operate between the registers and the RAM map. Here are the most important ones, written following the `gdb` convention:
- `mov [address_1] [address_2]`
  - Moves the **value** contained in `[address_2]` into `[address_1]`
- `lea [address_1] [value_2]`
  - Moves the **address** of `[value_2]` into `[address_1]`. In `gdb`, square brackets will indicate that the address is being taken instead of the value.
- `push [address]`
  - Pushes `[address]` to the top of the stack frame (`rsp` will therefore point to this address)
- `pop [register]`
  - Stores the top 8 bytes of the stack frame into `[register]`
- `jmp [address]`
  - Continues execution starting from `[address]`.
- `call [function address]`
  - Calls a function with the argument values present in `rdi`, `rsi`,`rdx`,`rcx`.
  - Pushes the return address, moves the function address into the `rip`.
- `subl esp [value]`
  - Allocates `[value]` bytes in the stack for a local variable, depending on its size. 
- `ret`    
  - Does a `pop rip` followed by a `jmp [return_address]`, at the end of a function call.

It is strongly recommended to complete the exercises in `EXERCISES.md` before moving on.
