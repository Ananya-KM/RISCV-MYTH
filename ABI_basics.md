# ABI BASICS

- **Application Binary Interface (ABI)** defines the interface between the operating system and application programs, allowing software to interact with the processor hardware.
- It includes **system calls**, which let applications access hardware resources.
- The ABI is crucial from both **processor design** and **programming** perspectives.

---

### **ABI vs Architecture**
- ABI is like a building’s interface (doors, switches), while the **ISA** (Instruction Set Architecture) and **RTL** are the internal structure (wiring, plumbing).
- Users interact through the ABI, abstracting the underlying architecture.

---

### **Software-Hardware Interaction Layers**
- Programs interact with hardware via layered interfaces:
  - **API** (Application Programming Interface): Connects application code to libraries.
  - **ABI**: Connects system software to machine language (ISA).
  - **ISA**: Interface between operating system and processor’s machine instructions.
  - **RTL**: Hardware-level implementation of ISA.

---

### **RISC-V Registers**
- In RISC-V, a 64-bit architecture (XLEN=64) uses **32 general-purpose registers**, each 64 bits wide.
- Registers are used for fast access and data operations, while larger data sets are managed through memory.
- RISC-V register names and roles are standardized by the ABI.

---

### **Memory and Data Access**
- **Byte-addressable memory**: Each memory address refers to one byte.
- A **64-bit value** requires 8 bytes, hence 8 consecutive memory locations.
- Memory in RISC-V follows **little-endian format**: the least significant byte is stored at the lowest address.

---

### **Little Endian vs Big Endian**
- **Little Endian**: LSB stored at the lowest memory address (used in RISC-V).
- **Big Endian**: MSB stored first (used in some other systems).

---

### **Double-Word (64-bit) Memory Access**
- Memory addresses of 64-bit numbers are typically multiples of 8 (e.g., M[0], M[8], M[16]).
- Data from memory can be loaded into registers using load instructions and stored using store instructions.

---

### **Instruction Format in RISC-V**
- RISC-V instructions are **32 bits** wide and follow structured formats based on instruction type:
  - **R-Type**: Operations between registers.
  - **I-Type**: Includes an immediate value.
  - **S-Type**: Stores data to memory.
- Fields include **opcode**, **source/destination registers**, **function codes**, and **offsets/immediates**.

---

### **Common Load/Store Instructions**
- **Load Double Word (ldw)**: Loads 64-bit data from memory into a register.
  - Example: `ldw x0, 16(x23)` loads from memory at address x23 + 16 into register x0.
- **Store Double Word (stw)**: Stores data from a register to memory.
  - Example: `stw x0, 8(x23)` stores data from register x0 to address x23 + 8.

---

### **Addition and Arithmetic**
- **Add instruction**: Performs arithmetic on register values.
  - Example: `add x0, x1, x2` adds contents of x1 and x2, stores in x0.

---

### **Register Allocation and ABI Naming**
- **Registers x0 to x31** have ABI-defined roles:
  - `x0`: Always 0
  - `x1`: Return address (RA)
  - `x2`: Stack pointer (SP)
  - `x3`: Global pointer (GP)
  - `x4`: Thread pointer (TP)
  - `x5-x7`, `x28-x31`: Temporaries
  - `x10-x17`: Function arguments (`a0-a7`)
  - `x18-x27`: Saved registers (`s1-s11`)

