# Control Logic
## Register File Read Process:
![Screenshot 2025-04-11 065813](https://github.com/user-attachments/assets/11cb5443-0a54-4172-939a-7abb84dd7ffb)

## 🧠 1. **Purpose of Register File Read**

Once the CPU decodes an instruction, it needs to retrieve the **operands** that the instruction operates on. These operands are usually stored in the **register file**.

- The register file is a small, fast memory area inside the CPU.
- It holds the values of general-purpose registers (in RISC-V, these are x0 to x31).
- The instruction specifies **source register addresses** (rs1 and rs2).
- The CPU reads the values stored in those registers to use them in computations or further operations.

**Key idea:** Reading the correct registers is essential for the instruction to execute correctly.

---

## 🏗️ 2. **Register File Setup**

The register file must be present and active in the CPU design.

- It is usually created as a **reusable module**.
- To use it, it must be included and connected properly.
- Often, a design framework may have it commented out initially — it needs to be enabled.

**Key idea:** The register file must be part of the CPU’s internal components, ready to provide values when asked.

---

## 🔄 3. **Register File Interface**

The register file has a well-defined set of inputs and outputs:

- **Inputs**: Register indices to read/write, write data, control signals like read or write enable.
- **Outputs**: Data read from the specified registers.

Typical RISC-V register file supports:
- **Two reads per clock cycle** (for rs1 and rs2).
- **One write per clock cycle** (for rd).

**Key idea:** This interface allows multiple operations (2 reads, 1 write) in a single cycle, enabling efficient instruction execution.

---

## 🎯 4. **Focus on Read Signals**

The CPU decodes which registers to read by extracting rs1 and rs2 from the instruction.

- These values are used to select which registers to read from.
- Only valid registers should be accessed, based on the instruction type.

**Key idea:** The control logic ensures that the register file reads happen only when needed.

---

## 🪝 5. **Steps to Hook Up Register File (Conceptually)**

- **Activate the Register File**: Ensure it’s included in the design.
- **Connect Reset**: So it initializes properly (e.g., registers start with known values).
- **Connect rs1 and rs2**: These are instruction fields indicating which registers to read.
- **Check Validity**: Ensure reads are enabled only for instructions that need them.
- **Avoid Confusion**: Don’t mix up rs1/rs2 (read) with rd (write destination).

**Key idea:** Correct connectivity and control of signals ensures proper read functionality.

---

## 🔍 6. **Debugging Tip – Register Initialization**

After a reset, the register file may be initialized with known values — e.g., each register contains its own index.

- Example: x5 contains 5 after reset.
- This is done so that in early simulation, you can easily **verify** whether the correct values are being read.

**Key idea:** Makes debugging and signal tracing easier in waveform tools.

---

## 🧪 7. **Simulation**

After setup:
- Run a simulation.
- Confirm that the correct values are being read from the register file when instructions specify them.
- Use waveform viewers to track rs1/rs2 inputs and the corresponding output values.

**Key idea:** Simulation helps validate correct behavior before hardware implementation.

---

## ➡️ 8. **Connecting Register File to ALU**

Once the values are read:
- They are used by the **ALU (Arithmetic Logic Unit)** to perform computations.
- The ALU is responsible for performing arithmetic (add, subtract) and logic (and, or) operations.

**Key idea:** ALU needs actual data (not just instruction fields) to compute results.

---

## 🧮 Arithmetic Logic Unit (ALU)

### 🎯 Purpose:

- Executes computations required by instructions.
- Uses input values from the register file.
- Outputs a **result** to be used or written back.

### 🔀 Behavior:

- Works like a **multiplexer**: It selects the correct operation based on the instruction type.
- For `addi`, it adds a register value and an immediate.
- For `add`, it adds two register values.
- For `blt`, it compares two values and makes a branching decision (no result value).

**Key idea:** ALU performs the "action" part of the instruction execution cycle.

---

## 🧠 Register File Array and Pipelining

### 🧊 Register File as Array:

- Internally, the register file is a **memory array** with 32 locations (in RISC-V).
- Each index corresponds to a register.
- Write logic checks if a register should be updated.
- Read logic selects values to output based on read indices.

**Key idea:** Register file behaves like an indexed memory with read/write capability.

---

### ⏱️ Timing and Pipeline Considerations:

In pipelined CPUs:
- One instruction may write to the register file while another instruction reads from it — **in the same cycle**.
- This can create **hazards** if not properly managed.

**Solution:**
- **Staging** ensures that write and read occur in different stages.
- For example:
  - Stage 1: Execute & write result of instruction A.
  - Stage 2: Read inputs for instruction B.
- This avoids incorrect reads from values that haven’t been written yet.

**Key idea:** Proper pipeline staging prevents read-after-write errors.

---

## 🪜 Pipeline Staging Summary

- Stage 1: Instruction fetch & decode
- Stage 2: Register read
- Stage 3: ALU compute
- Stage 4: Write-back result to register file

**Key principle:** Each stage has a specific role, and the register file must be accessed in a synchronized way with pipeline control.

---


### Register File:
![Screenshot 2025-04-11 065828](https://github.com/user-attachments/assets/fd6941c8-c1e7-4e4a-9523-c1d325569841) </P>
![Screenshot 2025-04-11 065841](https://github.com/user-attachments/assets/fbd72c68-069f-4b2e-b1b7-ae4559d88319)

## 🔁 Branch Instructions in RISC-V CPU

### 1. **Branch vs. Jump**
- **Branch Instructions**: Conditional. The next instruction address depends on the evaluation of a condition between two registers.
- **Jump Instructions**: Unconditional. The next instruction address is always updated, regardless of any condition.

---

### 2. **Types of Branch Instructions**
| Instruction | Condition                                                                 |
|-------------|---------------------------------------------------------------------------|
| `BEQ`       | Branch if Equal (`rs1 == rs2`)                                            |
| `BNE`       | Branch if Not Equal (`rs1 != rs2`)                                        |
| `BLT`       | Branch if Less Than (`rs1 < rs2`) - signed comparison                     |
| `BGE`       | Branch if Greater Than or Equal (`rs1 >= rs2`) - signed comparison        |
| `BLTU`      | Branch if Less Than Unsigned                                              |
| `BGEU`      | Branch if Greater Than or Equal Unsigned                                  |

---

### 3. **Branch Target Calculation**
- Formula:  
  `Branch Target PC = Current PC + Immediate Offset`
- The **immediate** is sign-extended and added to the **current PC**.
- This handles both forward and backward jumps via **two's complement** arithmetic.

---

### 4. **PC Multiplexer (PC Mux) Update**
- **PC Mux** decides which address is selected for the next instruction.
- Input options:
  - `PC + 4`: Default for sequential instructions.
  - `PC + immediate`: Used when a branch is taken.
- **Selection Logic**:
  - If branch is **taken** → choose `PC + immediate`
  - If branch is **not taken** or **not a branch** → choose `PC + 4`

---

### 5. **Pipeline Timing Considerations**
- The **branch condition** is evaluated in a later stage.
- But **next instruction fetch** happens in the earlier stage.
- Solution:
  - Synchronize the **PC Mux control logic** so that if a branch is taken, the pipeline redirects the PC correctly.
  - This often leads to a "stall" or "flush" for incorrect instruction fetch.

---

### 6. **Branch Condition Evaluation**
- Create a **`taken_branch` signal**:
  ```verilog
  taken_branch = is_branch & condition_met;
  ```
- `is_branch`: Decoded from opcode.
- `condition_met`: Depends on comparing `rs1` and `rs2`.

---

### 7. **Signed vs. Unsigned Comparisons**
- **Verilog default**: Unsigned comparisons.
- Use `$signed()` when dealing with signed operations (e.g., `BLT`, `BGE`).
  ```verilog
  ($signed(rs1) < $signed(rs2))
  ```

---

### 8. **Default Behavior**
- If not a branch instruction:
  ```verilog
  taken_branch = 0;
  ```

---

## 🧪 Test Bench for Verification

### ✅ Objective
Automatically verify if a test program (e.g., summing 1 to 9) produces correct results using a **test bench**.

### 🧩 Key Concepts
- **Register x10** holds the sum result.
- Expected value: **45**
- Signals:
  - `pass`: `x10 == 45`
  - `fail`: Timeout or incorrect result

### 🕓 Delay for Visibility
Use `ahead by 5` to delay ending simulation for waveform observation.

### 📝 Implementation
```verilog
pass = (x_reg[10].value == 45);
fail = (cycles > MAX_CYCLES) && !pass;
```

- Simulation **stops on pass/fail** and logs the outcome.
- Helps in **debugging** and ensuring pipeline behavior is correct.

## Lab for Branch instruction:

![Screenshot 2025-04-11 065906](https://github.com/user-attachments/assets/ecead2db-0293-4e42-bd7e-18b0b7ecaa8b)

## Solution
### 1_to_n_sum.v
    |cpu
      @0
         $reset = *reset;
         
         
         $pc[31:0] = (>>1$reset) ? 32'd0 : 
                     (>>1$taken_br) ? (>>1$br_tgt_pc): 
                     (>>1$pc + 32'd4);
         //curr pc = (is prev reset value true) ? if yes, curr pc = 0 : if not, is branch taken? yes, pc = branch target addr of prev instr : no, curr pc = prev pc + 4 (4 locations = 1 instr)
         //i need to consider the prev taken br and prev br target addr, cuz pc operates @0, but all computation is done @1 --> so curr pc always gives next instr addr , hence consider prev instr for branching
      @1
         $imem_rd_en = ! $reset;  //active low reset - it considers the prev reset value
         
         //input the address - its not pc directly since pc is always a multiple of 4
         //$imem_rd_addr[7:0] = $pc; //gives wrong ans - in instr mem - every index is considered 4 bytes (not 1 byte) so divide pc by 4 necessary
         $imem_rd_addr[7:0] = $pc / 4 ; 
         //$imem_rd_addr[7:0] = $pc >> 4 ; //this works too 
         //after observing the waveform: it seems the addr in the mem = 8 only
         //so as pc = 8*4 = 32 --> instr extracted is the first instr cuz : (32/4)mod8 = 0 (mod8 since only 8 instrs)
         
         //get the instruction from the instr mem
         $instr[31:0] = $imem_rd_data[31:0]; 
         
         //opcode in any instr format is 7 bits long but we consider only 5 bits
         //the part of instr that we need to decode the instr format = instr[6:2]
         //instr[1:0] is always 11 for base instr set, hence ignored
         
         $is_i_instr = $instr[6:2] ==? 5'b0000x || //this takes care of 5'b00000 and 5'b00001
                       $instr[6:2] ==? 5'b001x0 || //5'b00100 and 5'b00110
                       $instr[6:2] ==? 5'b11001 ||
                       $instr[6:2] ==? 5'b00100 ; 
         
         $is_r_instr = $instr[6:2] ==? 5'b01011 ||
                       $instr[6:2] ==? 5'b011x0 || //5'b01100 and 5'b01110
                       $instr[6:2] ==? 5'b10100 ;
         
         $is_s_instr = $instr[6:2] ==? 5'b0100x ; //5'b01000 and 5'b01001
         
         $is_b_instr = $instr[6:2] ==? 5'b11000 ;
         
         $is_j_instr = $instr[6:2] ==? 5'b11011 ;
         
         $is_u_instr = $instr[6:2] ==? 5'b0x101 ; //5'b00101 and 5'b01101
         
         //getting the immediate values and sign extending them
         //Eg: {21{$instr[31]} , $instr[30:20]} --> 12 bit imm, msb = instr[31] -> sign extend = {21{msb}}
         //instr[30:20] = give lower 11 (30-20+1) bits
         //r instr doesn't have any imm values
         
         //imm field is not valid for rtype
         $imm_valid = $is_r_instr ? 1'b0 : 1'b1 ; 
         ?$imm_valid
            $imm[31:0] = $is_i_instr ? { {21{$instr[31]}}, $instr[30:20] } : //12 bits for i type - immediate is one of the operands
                         $is_s_instr ? { {21{$instr[31]}}, $instr[30:25], $instr[11:7] } : //12 bits for s type - imm gives offset for calc effective address to store value
                         $is_b_instr ? { {20{$instr[31]}}, $instr[7], $instr[31:25], $instr[11:8], 1'b0 } : //13 bits for b type - imm gives pc relative offset to branch required label/instr
                         $is_u_instr ? { $instr[31:12] , 12'b0 } : //20 bits for u type - imm gives upper 20 bits of a 32 bit value 
                         $is_j_instr ? { {12{$instr[31]}}, $instr[19:12], $instr[20], $instr[30:21], 1'b0 } : //21 bits for j type - imm gives the pc relative addr to jump to 
                         32'b0 ; //if none of the instrs mentioned (if rtype), then imm is 0 - if this condition isn't there - ternary op is not complete - throws error
         
         //extracting other fields of instr
         //risc v is a regular ISA, which means most fields are consistent in their bit positions for all instr types except for immediate field
         //hence other fields of instr don't need to type based conditions
         
         //funct7 is valid only for rtype
         $funct7_valid = $is_r_instr ? 1'b1 : 1'b0 ; // condition ? if true : if false
         ?$funct7_valid
            $funct7[6:0] = $instr[31:25]; 
         
         //this is the only signal that is in my code and not told in the workshop 
         //funct3, rs1, rs2 is invalid for u_type and j type in common, so define a common signal
         $u_or_j = $is_u_instr || $is_j_instr ; 
         
         //funct3 is invalid for u type and j type
         $funct3_valid = $u_or_j ? 1'b0 : 1'b1 ;
         ?$funct3_valid
            $funct3[2:0] = $instr[14:12];
         
         //rs1 is invalid for u type and j type
         $rs1_valid = $u_or_j ? 1'b0 : 1'b1 ;
         ?$rs1_valid
            $rs1[4:0] = $instr[19:15]; //source reg 1 - only 32 reg in total, so 5 bits to represent them
         
         //rs2 is valid for rtype, stype and btype only OR rs2 is invalid for u type, j type and i type - im using this variant since ive define uorj signal already
         //any of the above two is fine
         $rs2_valid = ($u_or_j || $is_i_instr) ? 1'b0 : 1'b1; 
         ?$rs2_valid
            $rs2[4:0] = $instr[24:20]; //source reg 2
         
         //rd is invalid for stype and btype
         $rd_valid = ($is_s_instr || $is_b_instr) ? 1'b0 : 1'b1; 
         ?$rd_valid
            $rd[4:0] = $instr[11:7]; //destination reg
         
         //opcode is valid for all instr types, so no validity check 
         $opcode[6:0] = $instr[6:0]; //opcode required to instruct the alu
         
         //now decode individual instrs - only few instrs from RV32I are considered - others can be added later
         //collect the bits required for decoding - funct7[5], func3, opcode
         $dec_bits[10:0] = { $funct7[5], $funct3, $opcode }; //if index range not specified - means consider all bits of that field
         
         //add, addi (arithmetic instrs)
         $is_add = $dec_bits == 11'b0_000_0110011;
         $is_addi = $dec_bits ==? 11'bx_000_0010011; //==? is verilog operation for comparing against don't cares, it works even when there is not don't care
         //for addi, funct7[5] can be 0 or 1, it doesn't matter - so x (don't care) is used
         
         //branch variants 
         $is_beq = $dec_bits ==? 11'bx_000_1100011;
         $is_bne = $dec_bits ==? 11'bx_001_1100011;
         $is_blt = $dec_bits ==? 11'bx_100_1100011;
         $is_bge = $dec_bits ==? 11'bx_101_1100011;
         $is_bltu = $dec_bits ==? 11'bx_110_1100011;
         $is_bgeu = $dec_bits ==? 11'bx_111_1100011;
         
         //REGISTER FILE interfacing
         //I'm reusing field validity check signals
         
         //INPUT SIGNALS - reg file
         //write signals 
         //NOTE: x0 reg in risc v is hardwired to 0, so writes are ignored and read is always 0
         //wr_en = 0 if rd = x0 - changing wr_en along is not disabling write (reg is still written into when r0 is rd - check rf_wr_data field in waveform)
         $rf_wr_en = ($rd == 5'd0) ? 1'b0 : $rd_valid;  //write into the register file only if rd is a valid field
         $rf_wr_index[4:0] = $rd; //address of the dest reg
         //check the wr_en signal - if its 0, don't write into the register, else, write
         $rf_wr_data[31:0] =  $rf_wr_en ? $result : 32'd0; //this is actually the output of the alu - the result
         
         //read signals
         $rf_rd_en1 = $rs1_valid; //read rs1 from reg file only if rs1 is a valid field in the instr
         $rf_rd_index1[4:0] = $rs1; //address of source reg 1
         $rf_rd_en2 = $rs2_valid; //read rs2 from reg file only if rs2 is a valid field
         $rf_rd_index2[4:0] = $rs2; //address of source reg 2
         
         //OUTPUT SIGNALS - reg file - inputs to the ALU
         $src1_value[31:0] = $rf_rd_data1; //rs1 value
         $src2_value[31:0] = $rf_rd_data2; //rs2 value
         
         //ALU 
         //operations: add, addi (others are added later)
         $result[31:0] = $is_addi ? ($src1_value + $imm) : //rd <-- rs1 + imm
                         $is_add ? ($src1_value + $src2_value) : //rd <-- rs1 + rs2
                         32'bx ; //if none of the above instrs, result is don't care
                         
         //logic to specify if a branch is taken or not
         $taken_br = (! $is_b_instr) ? 1'b0 :
                      $is_beq ? ($src1_value == $src2_value) :
                      $is_bne ? ($src1_value != $src2_value) :
                      $is_blt ? ( ($src1_value < $src2_value) ^ ($src1_value[31] != $src2_value[31]) ) : // for signed comparisons
                      $is_bge ? ( ($src1_value >= $src2_value) ^ ($src1_value[31] != $src2_value[31]) ) : //for signed comparison
                      $is_bltu ? ($src1_value < $src2_value) : //unsigned comparisons only
                      $is_bgeu ? ($src1_value >= $src2_value) : //usigned comparisons
                      1'b0 ; //default condition
    
         //if branch is taken for curr instr, then next pc is changed
         $br_tgt_pc[31:0] = $pc + $imm ; 

### Diagram         
![Screenshot 2025-04-11 071146](https://github.com/user-attachments/assets/2f210c6b-1a81-4328-8a93-6e166467734f)

### Waveform
![Screenshot 2025-04-11 064924](https://github.com/user-attachments/assets/caf14642-7581-4a9a-93ad-b341a05f6762)

