
# **CPU Fetch & Decode – Summary**

#### 1. **CPU Framework & Build Process**
- You're building a CPU **step-by-step**, starting with basic control like the PC (Program Counter), and gradually adding functional blocks like fetch, decode, ALU, etc.

#### 2. **Component Implementation**
- **A. Next PC Logic**:  
  - PC increments by 4 (each instruction is 32 bits).
  - Handles reset carefully to ensure **first instruction** is fetched from PC = 0.
  - Uses **previous reset** value to avoid skipping.

- **B. Instruction Fetch**:  
  - Use the current PC to fetch instruction from **instruction memory**.
  - Instruction memory is already instantiated; you need to **uncomment** and **connect signals**.

- **C. Decode** (coming next):  
  - Fetched instruction is analyzed to decide what operation the CPU will perform.

#### 3. **Instruction Memory Signals to Connect**
- `imem_en`: Enable signal.
- `imem_addr`: Instruction address = `PC >> 2` (because PC is byte-addressed).
- `imem_read_data`: Instruction data output, assign it to an `instruction` signal.

#### 4. **Testing and Simulation**
- Recompile after connecting signals.
- Use **waveforms and logs** to verify:
  - PC starts at 0 after reset.
  - PC increments by 4.
  - Fetched instruction signal is active.

#### 5. **Visualization**
- Uncomment the **CPU visualization** line in the code.
- You’ll see PC and instruction values visually updating as simulation runs.

#### 6. **Next Step (Decode Logic)**
- The decode logic will interpret the instruction and prep it for execution in the ALU or other parts of the CPU.

## Labs Fetch Logic

![Screenshot 2025-04-11 025141](https://github.com/user-attachments/assets/279f6387-1ad8-4848-ba54-94c535915d6a)

### ✅ **Summary of Key Steps (Instruction Fetch)**

1. **Uncomment Components**  
   - Enable **instruction memory** and **CPU visualization** in the code.

2. **Initial Compilation**  
   - Compile the code to identify errors related to missing or unconnected signals.

3. **Connect Signals to Instruction Memory**  
   - `imem_en`: Enable signal.  
   - `imem_addr`: Set to `PC >> 2` (because each instruction is 4 bytes, and PC is byte-addressed).  
   - `imem_read_data`: Connect to `instruction` signal to carry the fetched instruction.

4. **Test and Simulate**  
   - Run the simulation and check the waveform/log:
     - PC should start at 0 after reset.
     - PC should increment by 4 per instruction.
     - Instruction fetch should occur correctly.

5. **Next Step – Decode Logic**  
   - Begin implementing decode logic to interpret and process fetched RISC-V instructions.

## Decode Logic

![Screenshot 2025-04-11 025154](https://github.com/user-attachments/assets/fbed7f47-5b7f-415c-b6f8-55824d79dbb6)


### ✅ **Summary of Decode Logic (RISC-V Instruction Types)**

1. **Goal**  
   - Decode the fetched instruction to identify its **type** (e.g., R-type, I-type, S-type) based on the **RISC-V ISA** format.

2. **Opcode Extraction**  
   - RISC-V opcode is in bits **[6:0]**, but you focus on bits **[6:2]** (since bits [1:0] are always `11` for base ISA).

3. **Instruction Type Detection**  
   - Define signals like:
     - `is_I_type`, `is_R_type`, `is_S_type`, etc.
   - Use the `==?` operator to allow **don’t care bits** in comparisons.

   **Example**:  
   ```verilog
   is_I_type = (opcode[6:2] == 5'b00000?) || (opcode[6:2] == 5'b00100?);
   ```

4. **Don't Care Bits (`?`)**  
   - Let you match multiple instructions that share the same core bits but differ in unused bits.

5. **Handle All Types**  
   - Implement decoding for all types you may encounter later, even if your current program doesn't use all of them.

6. **Simulation and Debugging**  
   - Simulate and check waveforms for:
     - `opcode[6:2]`
     - Decoded type signals (`is_I_type`, etc.)
   - Ensure only the correct type signal is asserted for each instruction.

## Decoding the immediate fields for different instruction types in the RISC-V ISA:

### ✅ **Summary: Immediate Decode Logic (RISC-V)**

---

#### **1. Goal**
- Extract and **sign-extend** immediate values from instructions for types that need them (I, S, B, U, J).
- Construct a **32-bit immediate** based on instruction format.

---

#### **2. I-Type Instructions**
- **Immediate bits**: `[31:20]`  
- **Logic**:
```verilog
imm_I = { {21{instruction[31]}}, instruction[30:20] };
```

---

#### **3. S-Type Instructions**
- **Immediate bits**: `[31:25]` (upper), `[11:7]` (lower)  
- **Logic**:
```verilog
imm_S = { {21{instruction[31]}}, instruction[30:25], instruction[11:7] };
```

---

#### **4. B-Type Instructions** (Branch)
- **Immediate bits**: `[31] [7] [30:25] [11:8]`, aligned by appending `0`  
- **Logic**:
```verilog
imm_B = { {20{instruction[31]}}, instruction[7], instruction[30:25], instruction[11:8], 1'b0 };
```

---

#### **5. U-Type Instructions**
- **Immediate bits**: `[31:12]`, lower 12 bits are zeroed  
- **Logic**:
```verilog
imm_U = { instruction[31:12], 12'b0 };
```

---

#### **6. J-Type Instructions** (Jump)
- **Immediate bits**: `[31] [19:12] [20] [30:21]`, append `0`  
- **Logic**:
```verilog
imm_J = { {12{instruction[31]}}, instruction[19:12], instruction[20], instruction[30:21], 1'b0 };
```

---

#### **7. Simulation Checks**
- Test all immediate types via waveform viewer:
  - Inspect `imm_I`, `imm_S`, etc.
  - Ensure correct **sign-extension**.
  - Verify for both positive and negative values.

## Extracting other fields of the instruction in RISC-V ISA
Great! Let’s walk through the theory behind extracting and decoding RISC-V instruction fields, while integrating the logic you'd implement in SystemVerilog. We'll go step-by-step:

---

### 🔍 1. **Overview of Field Extraction in RISC-V ISA**

Every RISC-V instruction is a 32-bit binary word. Various parts of this 32-bit word encode specific information:
- Which operation is being performed
- Which registers are being used
- Any constant/immediate values involved

To decode this instruction and perform the right operation in the CPU, we need to **extract these fields**:
- **rs1** and **rs2** (Source registers)
- **rd** (Destination register)
- **funct3** and **funct7** (Help differentiate instructions within the same opcode)
- **opcode** (Base operation category)

---

### 📍 2. **Fixed Field Positions**

The beautiful thing about RISC-V is that many fields (except immediate) always occur at the same positions in the instruction. This means you can extract them using **bit slicing** without needing to decode the instruction type first.

| Field   | Bit Range | Meaning                  |
|---------|-----------|--------------------------|
| `rd`    | 11–7      | Destination Register     |
| `rs1`   | 19–15     | Source Register 1        |
| `rs2`   | 24–20     | Source Register 2        |
| `funct3`| 14–12     | 3-bit sub-opcode         |
| `funct7`| 31–25     | 7-bit sub-opcode (mainly for R-type) |
| `opcode`| 6–0       | Main operation type      |

So for example, if your 32-bit instruction is in a variable called `instruction`, you can do this in SystemVerilog:
```systemverilog
wire [4:0] rs1 = instruction[19:15];
wire [4:0] rs2 = instruction[24:20];
wire [4:0] rd  = instruction[11:7];
wire [2:0] funct3 = instruction[14:12];
wire [6:0] funct7 = instruction[31:25];
wire [6:0] opcode = instruction[6:0];
```

---

### ✅ 3. **Conditional Validity (Why it’s Important)**

Not all instructions use all fields.

![Screenshot 2025-04-11 025212](https://github.com/user-attachments/assets/d8c76a14-d0a8-44b6-97c0-d169d49dda92)

To avoid invalid values, use conditional logic. For example:
```systemverilog
wire rs2_valid = is_r_type || is_s_type || is_b_type;
wire [4:0] rs2 = rs2_valid ? instruction[24:20] : 5'b0;
```

Repeat this pattern for each field based on the types that use it.

---

### 🧠 4. **Instruction Decoding: Matching Patterns**

After extracting fields, you need to **identify the actual instruction**. This is where the combination of `opcode`, `funct3`, and `funct7` helps.

To simplify pattern matching:
```systemverilog
wire [14:0] decode_bits = {funct7, funct3, opcode};
```

Then, you compare this with known instruction patterns.

#### Examples:

- **ADD (R-type):**
  ```systemverilog
  wire is_add = (decode_bits == 15'b0000000_000_0110011);
  ```
- **ADDI (I-type):**
  ```systemverilog
  wire is_addi = (decode_bits == 15'bxxxxxxxx_000_0010011);  // funct7 not relevant
  ```
- **BEQ (B-type):**
  ```systemverilog
  wire is_beq = (decode_bits == 15'bxxxxxxxx_000_1100011);
  ```
- **BLT (B-type):**
  ```systemverilog
  wire is_blt = (decode_bits == 15'bxxxxxxxx_100_1100011);
  ```

Notice how `x` is used to denote **don’t care** bits, which is especially useful for the `funct7` in non-R-type instructions.

---

### 🛠 5. **SystemVerilog’s `==?` Operator**

SystemVerilog introduces `==?`, which allows comparison with “don’t care” (`x`) bits:

```systemverilog
wire is_beq = (decode_bits ==? 15'bxxxxxxxx_000_1100011);
```

This is super handy for compact and readable instruction decoding logic.

---

### 🧹 6. **Cleaning Up with `bogus_use`**

Sometimes, your tool (e.g., SandPiper) may complain that you're declaring wires like `is_add` but not using them right away. To silence such warnings:

```systemverilog
`bogus_use(is_add, is_addi, is_beq, is_blt);
```

This macro tells the tool that “yes, I know these are declared but not used (yet). Don’t warn me.”

---

### 📈 7. **Simulation & Verification**

After implementing:
- Run your design through simulation (like on Makerchip or another tool).
- In the **waveform**, verify:
  - Fields like `rs1`, `rs2`, `rd`, `funct3`, `funct7` are being extracted correctly.
  - Instructions are properly decoded (e.g., `is_add` goes high when ADD is seen).
  - Fields show as zero when they’re not valid for that instruction type.

## Labs Decode logic

![Screenshot 2025-04-11 025234](https://github.com/user-attachments/assets/ca7f03c5-1ad2-415b-b343-9480df2414bf) </p>
![Screenshot 2025-04-11 025257](https://github.com/user-attachments/assets/f3b705e1-35f4-49fb-ab83-84313487148f) </p>
![Screenshot 2025-04-11 025331](https://github.com/user-attachments/assets/6634a2ee-840b-490c-8a09-9b0912f79595)

## Solution
![Screenshot 2025-04-11 064839](https://github.com/user-attachments/assets/5f3543fa-ccf2-469b-ae9b-eb124351da72) </p>
![Screenshot 2025-04-11 064938](https://github.com/user-attachments/assets/7f9afba2-94e1-4866-84cb-040d45055514)

