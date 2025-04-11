
# **Running & Debugging with SPIKE**
**Basics:**
- `spike pk program`: Loads and runs your program.
- Instructions go through **Fetch → Decode → Execute**, just like in real hardware.
- Registers and memory behave like a real RISC-V processor.
- **System calls** go through the proxy kernel (`pk`).

**Debug Mode:**
- `spike -d pk program` → Starts the interactive debugger.

**Debugger Commands Cheat-Sheet:**
| Command                | Purpose                              |
|------------------------|--------------------------------------|
| `reg 0`                | Show all registers                   |
| `reg 0 a0`             | Show a specific register (`a0`)      |
| `mem 0 2020`           | View memory at address 0x2020        |
| `pc 0`                 | Show current program counter         |
| `until pc 0 [addr]`    | Run until PC reaches that address    |
| `step` or `step N`     | Execute 1 or N instructions          |
| `disasm 0 [addr] [N]`  | Disassemble N instructions starting from addr |

---

## Sample Programs
### Summing Numbers from 1 to 9 in C
    #include <stdio.h>

    extern int sum1to9_ASS(int x, int y);

    int main() {
        int total = 0;
        int upper_limit = 9;
        total = sum1to9_ASS(0x0, upper_limit + 1);
        printf("Sum of numbers from 1 to %d is %d\n", upper_limit, total);
    return 0;
     }
![WhatsApp Image 2025-04-11 at 12 03 40 AM (10)](https://github.com/user-attachments/assets/ff89165f-cdc5-445c-a0bd-bf62a84b3fa2)

### Assembly Implementation of Summing 1 to 9
    .section .text
    .global load
    .type load, @function

    load:
           add     a4, a0, zero  # Initialize sum register a4 to 0
           add     a2, a0, a1    # Load the loop count into a2
           add     a3, a0, zero  # Initialize intermediate register a3 to 0

    loop:
           add     a4, a3, a4    # Add intermediate sum to total
           addi    a3, a3, 1     # Increment loop counter
           blt     a3, a2, loop  # Repeat if counter is below limit
           add     a0, a4, zero  # Store final result in a0
           ret
![WhatsApp Image 2025-04-11 at 12 03 40 AM (11)](https://github.com/user-attachments/assets/7392e5bc-41dd-4460-9199-040cca220cac)

### View files using Cat Command
    cat 1to9_custom.c
    cat load.S
![WhatsApp Image 2025-04-11 at 12 03 40 AM (13)](https://github.com/user-attachments/assets/441e5136-f7b1-49b5-b624-6b3fa2316513)
### Compile the C code using the RISC-V compiler:
     riscv64-unknown-elf-gcc -O1 -mabi=lp64 -march=rv64i -o 1to9_custom.o 1to9_custom.c load.S
### Display the optimized assembly code for the main function:
    $ riscv64-unknown-elf-objdump -d 1ton_custom.o | less
![WhatsApp Image 2025-04-11 at 12 03 40 AM (13)](https://github.com/user-attachments/assets/3080331f-85c4-4865-86fa-725e1193c85e)
![WhatsApp Image 2025-04-11 at 12 03 40 AM (14)](https://github.com/user-attachments/assets/edf54269-53fe-41ec-94a2-1012ec28e848)

## LAB to Run C-Program on RISC-V CPU
![WhatsApp Image 2025-04-11 at 12 03 40 AM (16)](https://github.com/user-attachments/assets/6f541ef6-f9d7-4ee1-944f-7884a187edd3)
![WhatsApp Image 2025-04-11 at 12 03 40 AM (17)](https://github.com/user-attachments/assets/5a2ee0c9-4de2-4824-aa59-aa3da7235266)
![WhatsApp Image 2025-04-11 at 12 03 40 AM (18)](https://github.com/user-attachments/assets/0e03e4b9-9952-4a41-ba32-8b90e7e98fec)
![WhatsApp Image 2025-04-11 at 12 03 40 AM (19)](https://github.com/user-attachments/assets/361a4783-0fa2-45d7-85f8-3784e4cbdb40)
