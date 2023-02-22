
## Exercise 1 - NX Bypass

- Get the binary by running `wget https://gr4n173.github.io/ret2libc/public/files/climb`
- Disable ASLR by running `echo 0 | sudo tee /proc/sys/kernel/randomize_va_space`
- Enter in a `gdb` session for the binary, run the `checksec` command to check the security measures
  - Is the stack executable? Which binary segments are randomized? What about canaries?
- Generate a pattern of 300 bytes with `pattern create 300`, we will use this to overwrite the `RBP`
  - Find the vulnerable function, set a breakpoint to it, then continue execution. Paste the pattern on the standard input.
  - Inspect the `RBP` register after the crash, get the offset value with `pattern offset [rbp_value]`
  - Add 8 bytes to this value, a string like `'A'*[offset+8] + [address]` will now reach the `RIP` and overwrite it a chosen address.
  - Since the stack is not executable, we will reach the function `system()` in `LIBC` and pass `"/bin/sh"` to it.
    - Get the address of `system()` in `LIBC` by setting a breakpoint at `main()`, running the binary, then `p system`
    - Search in the `libc` memory for the `"/bin/sh"` string, with the command `find [system_address],+99999999,"/bin/sh"`
    - Outside `gdb` use `ropper -f [binary] --search "pop rdi; ret"` to search the address corresponding to a `pop rdi` instruction
    - Use ropper to also find a single `ret;` instruction. append this address to the exploit string, this is known as "stack alignment"
    - append the `pop rdi` address to the exploit string, then append the `/bin/sh` string address. `/bin/sh` is now in the `rdi` register, ready to be passed as argument.
    - Append the `system()` address to the exploit string, the exploit is complete and `system("/bin/sh")` will be executed by the binary.
  - Make the binary execute a shell by supplying the full input string to it.
