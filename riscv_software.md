## Step 1: Setting Up Ubuntu within VMBox
Install Ubuntu on VMBox.
Launch Ubuntu's terminal.

### Step 2: Install Leafpad
    sudo apt install leafpad
### Navigate to the home directory:
    cd
    
    leafpad filename.c &
    
![WhatsApp Image 2025-04-11 at 12 03 40 AM](https://github.com/user-attachments/assets/0e55bc30-eb2f-4bb9-bc5f-91b226920cd1)


### Step 3: Compile and Run the C Code
#### Compile the C code
      gcc filename.c
#### Run the compiled program:
     ./a.out

![WhatsApp Image 2025-04-11 at 12 03 40 AM (1)](https://github.com/user-attachments/assets/a1c9a2ad-0ed5-44e4-ae5b-1a8b4bf3924f)

### Step 4: Compile C Code with RISC-V Compiler
#### Compile the C code using the RISC-V compiler:
     $ riscv64-unknown-elf-gcc -O1 -mabi=lp64 -march=rv64i -o filename.o filename.c
![WhatsApp Image 2025-04-11 at 12 03 40 AM (3)](https://github.com/user-attachments/assets/3bb5071b-3407-4a31-b99a-9d943f99ca46)

#### List the compiled object file:
     $ ls -ltr filename.o
![WhatsApp Image 2025-04-11 at 12 03 40 AM (4)](https://github.com/user-attachments/assets/896a5f36-df56-4f27-b4cd-24e8211bad9e)

### Step 5: Display Assembly Code
#### Display the optimized assembly code for the main function:
    $ riscv64-unknown-elf-objdump -d filename.o | less
![WhatsApp Image 2025-04-11 at 12 03 40 AM (5)](https://github.com/user-attachments/assets/eac2fee8-a0b5-43cf-aee9-16d8e0e86dd7)

# SPIKE SIMULATION
#### 1. **Compile C file using RISC-V GCC:**

```bash
riscv64-unknown-elf-gcc -O1 -mabi=lp64 -march=rv64i -o sum1ton.o sum1ton.c
```
![WhatsApp Image 2025-04-11 at 12 03 40 AM (3)](https://github.com/user-attachments/assets/34227c10-fe4a-4c2e-8449-a21df721b89d)

#### 2. **Run with Spike using Proxy Kernel:**

```bash
spike pk sum1ton.o
```
![WhatsApp Image 2025-04-11 at 12 03 40 AM (6)](https://github.com/user-attachments/assets/393d306c-abc0-4f08-ac4f-d7790f1639fc)

#### 3. **(Optional) Debug with Spike:**

```bash
spike -d pk sum1ton.o
```
![WhatsApp Image 2025-04-11 at 12 03 40 AM (7)](https://github.com/user-attachments/assets/9ababa36-8139-43a4-aae1-25e741654e05)

