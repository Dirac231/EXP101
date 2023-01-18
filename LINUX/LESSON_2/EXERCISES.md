
Due to "stack alignment" issues, when creating `ROP` code, it's worth to first inject the `main()` address in the `rip` followed by other instructions.

## Exercise 1 - NX + ASLR

https://jlajara.gitlab.io/Privesc_Ret2libc_ASLR_64

- Enter in a `gdb` session for the binary, run the `checksec` command to check the security measures
  - Is the stack executable? Are the binary segments randomized? What about canaries?
- Generate a pattern of 300 bytes with `pattern create 300`, we will use this to overwrite the `RBP`
  - Find the vulnerable function, set a breakpoint to it, then continue execution. Paste the pattern on the standard input.
  - Inspect the `RBP` register after the crash, get the offset value with `pattern offset [rbp_value]`
  - Add 8 bytes to the offset value, build a long enough string to overwrite the `RIP` register with a canonical address.
    - You can use `\x41\x41\x41\x41\x41\x41\x00\x00` as a test.
  - After controlling the `RIP`, since the stack is not executable, we have to reach the function `system()` and pass `"/bin/sh"` to it.
    - Get the address of `system()` in `LIBC` by setting a breakpoint at `main()`, running the binary, then `p system`
    - Search in the `libc` memory for the `"/bin/sh"` string, with the command `find [system_address],+99999999,"/bin/sh"`
    - Outside `gdb` use `ropper -f [binary] --search "pop rdi; ret"` to search the address corresponding to a `pop rdi` instruction
    - Supply to the RIP this address, then append the `/bin/sh` string address. This will copy `/bin/sh` into the `rdi` register
    - As `rdi` stores the first argument of the next function, if you append the `system()` address next, you will execute `system("/bin/sh")`, getting code execution.
  - Make the binary execute a shell by supplying the full input string to it.
