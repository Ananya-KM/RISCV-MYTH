# RISC-V Microarchitecture Overview

### 1. Program Counter (PC)
- **Function**: Holds the address of the next instruction to execute.
- **Operation**: Typically increments by 4 (since each instruction is 4 bytes) to point to the subsequent instruction.
- **Reset Handling**: On reset, the PC is set to 0 to start execution from the beginning of the instruction memory.
- 
![Screenshot 2025-04-11 011913](https://github.com/user-attachments/assets/9a007cf7-9a19-4bd7-9116-59b94c85e9aa)

**Implementation Snippet**:

```verilog
$reset = *reset;
$pc[31:0] = (>>1$reset) ? 32'd0 : (>>1$pc + 32'd4);
```

*Explanation*: If the reset signal is active, the PC is set to 0. Otherwise, it increments by 4.

### 2. Instruction Memory
- **Purpose**: Stores the program's instructions.
- **Access**: The PC provides the address to fetch the current instruction.

### 3. Instruction Decode Logic
- **Role**: Breaks down the fetched instruction into its components:
  - **Opcode**: Type of operation (e.g., add, load).
  - **Source Registers**: Operands for the operation.
  - **Destination Register**: Where the result is stored.
  - **Immediate Values**: Constants embedded in the instruction.

### 4. Register File
- **Contains**: A set of general-purpose registers.
- **Operations**:
  - **Read**: Fetches operand values for the ALU.
  - **Write**: Stores results from the ALU or memory operations.

### 5. Arithmetic Logic Unit (ALU)
- **Function**: Performs arithmetic (e.g., addition, subtraction) and logical (e.g., AND, OR) operations.
- **Inputs**: Typically two operands from the register file.
- **Output**: Result written back to the register file.
- 
![Screenshot 2025-04-11 011928](https://github.com/user-attachments/assets/97bca39b-e98f-472c-ad28-c77f4d81cfdf)

#### Starting Point Code:
- The GitHub repo gives you a basic starting point for designing a RISC-V CPU using **TL-Verilog**.
- The code already includes some macro definitions and a program setup using **Makerchip’s module interface**.
- 
![Screenshot 2025-04-11 011939](https://github.com/user-attachments/assets/4b89bc32-f09f-4581-b01c-6c555878baa1)

#### 🔹 RISC-V Assembler
- The assembler in this project uses **TL-Verilog and m4 macros**.
- It’s not 100% RISC-V standard, but it’s close enough for our use during the workshop.

#### 🔹 Preloaded Test Program
- A test program is already included—it adds numbers from **1 to 9**.
- The pipeline is partly set up, including a **reset signal** in stage 0.
- There’s space in the code to add your own logic.
  
  ![Screenshot 2025-04-11 011949](https://github.com/user-attachments/assets/79e69d21-8e1b-4f72-ae9a-bda447e6b479)


#### 🔹 Pipeline Setup
- Basic components like:
  - **Instruction Memory** (preloaded with test instructions)
  - **Register File** (read/write registers)
  - **Data Memory**  
  are already included and ready to use.

#### 🔹 Platform Communication
- The design automatically checks for **pass/fail** after **40 clock cycles**.
- Macros for **logging and feedback** help you track what's happening.

#### 🔹 Memory and Arrays
- Instruction memory, register file, and data memory are built using simple **array-like structures**.
- These are all **custom components** designed for this workshop.

#### 🔹 Visualization Tool
- There’s a built-in visual tool (like the one used for the calculator example) to **see your CPU in action**.
- Super helpful for **debugging and learning**.

#### 🔹 Help is Available
- If you get stuck, check the **Help** section for **reference solutions**.

### 6. Branching and Control Flow
- **Branch Instructions**: Alter the PC based on conditions (e.g., if a register value is zero).
- **Branch Target Calculation**: New PC = Current PC + Offset (immediate value).

### 7. Memory Access (Load and Store)
- **Load**: Transfers data from memory to a register.
- **Store**: Writes data from a register to memory.
- **Address Calculation**: Base address from a register + Immediate offset.

### 8. Pipeline and Cycle Timing
- **Single-Cycle Model**: Assumes all operations complete in one clock cycle (simplified for educational purposes).
- **Memory Access Assumption**: Instantaneous, with no delays.
- **PC Update**: After each instruction, the PC is updated to point to the next instruction or branch target.

### 9. Visualization and Debugging
- **Makerchip Visualization**: Provides a graphical representation of the CPU's operation.
- **Waveforms**: Useful for debugging signal behaviors and ensuring correct implementation.

![Screenshot 2025-04-11 012007](https://github.com/user-attachments/assets/8efb4a2e-5a6d-430e-8970-a3027af5f1d8)
### Regsiter File Updates
The register file shows updated values as instructions are processed, and users can observe how values are written

![Screenshot 2025-04-11 012017](https://github.com/user-attachments/assets/5d6e1008-44e0-4b46-8279-cc5a9fcad634)

#### 🔹 Visualization Behavior
- Early on, visuals might not show much.
- As you build logic, you’ll see components like **Program Counter (PC)** and **instructions** updating.
- Helps you understand the **pipeline flow** and **CPU behavior**.

#### 🔹 Waveform Debugging
- If the visual diagram doesn’t load (due to high load), use the **waveform view**.
- It shows all important signals like:
  - PC
  - Register values
  - Instruction stages
- Great for **step-by-step debugging**.

#### 🔹 Instruction Memory
- Contains the CPU’s program.
- Shows both **binary** and **disassembled** instructions.
- A moving arrow represents the **PC**, stepping through instructions.

#### 🔹 Program Counter (PC) & Branching
- PC usually increments to fetch the next instruction.
- On branch instructions, it may briefly go to the wrong instruction.
- The pipeline **corrects itself** and jumps to the right instruction, showing **branch handling**.

#### 🔹 Decode Logic & Bad Paths
- Binary instructions are **decoded** so you can see what action they perform.
- If the CPU follows a **wrong path**, it’s shown in **gray**, then corrected as needed.

#### 🔹 Register File Updates
- As instructions run, the **register values** are updated live.
- For example, if an `ADD` is executed, the result is immediately visible.

#### 🔹 Next PC Logic
- Controls where the next instruction comes from.
- By default, PC moves to the next instruction unless a branch changes it.
- 
![Screenshot 2025-04-11 012029](https://github.com/user-attachments/assets/91d14c86-a99c-470a-a3fc-e2db437d836a)

- Each RISC-V instruction is 4 bytes, so the **Program Counter (PC)** increments by 4 after each instruction.
- On **reset**, the PC must be set to **0** to start fetching from the first instruction.
- If reset isn't handled properly, the PC might skip the first instruction — this is avoided by checking the **previous reset** state.
- The logic:  
  ```
  $pc = (>>1$reset) ? 0 : (>>1$pc + 4);
  ```
  ![Screenshot 2025-04-07 103737](https://github.com/user-attachments/assets/1e2e1296-722c-4c15-826b-81fce2426146)

- After reset, PC starts at 0 and then increases by 4 every cycle.
- This logic should allow future support for **branch and jump instructions**.
- Use **waveforms** to verify:
  - PC starts at 0 after reset
  - Increments by 4 correctly
  - Can be extended later for branches/jumps

For more detailed information and resources, refer to the [RISC-V MYTH Workshop repository](https://github.com/stevehoover/RISC-V_MYTH_Workshop). 

## LABS
![Screenshot 2025-04-07 103737](https://github.com/user-attachments/assets/934458b2-8f6b-4433-ac52-604d8fda6cca)
![Screenshot 2025-04-07 104530](https://github.com/user-attachments/assets/a79c2977-c3bf-4eea-88d2-9d8c831ab1f9)
![Screenshot 2025-04-11 013732](https://github.com/user-attachments/assets/9b56d661-fe25-4a4d-9480-391a842d3352)


