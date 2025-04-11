
# 🔧 Final Phase of RISC-V CPU Design 

---

### 🧩 1. **Load and Store Instructions Overview**

- **Load Types in RISC-V:**  
  RISC-V supports multiple data widths (byte, half-word, word) with both signed and unsigned variants.  
  **This implementation supports only full-word loads/stores**, simplifying memory operations.

- **Sign Extension vs Zero Extension:**  
  Signed loads extend the sign bit, while unsigned loads fill upper bits with 0s.  
  Not implemented in this design since only full-word loads are used.

---

### 🧠 2. **Decoding Logic**

- **is_load Signal:**  
  All types of loads (LB, LH, LW, etc.) are decoded into a single `is_load` signal based on the opcode.  
  This simplifies decoding and removes the need to differentiate between load sizes or sign types.

- **is_s_type Signal:**  
  Used to identify store instructions (like SW). Helps control memory write operations.

---

### 🧮 3. **Address Computation**

- **For Loads and Stores:**  
  Use the ALU to compute the effective address:  
  `Address = rs1 + immediate`  
  Same logic used for `addi` instruction, so reuse existing infrastructure.

- **Memory is Word-Aligned:**  
  Memory only supports 16 entries (32-bit each).  
  Use `address[5:2]` to index memory (ignore lower 2 bits of the byte address).

---

### ⏳ 4. **Timing and Load Hazards**

- **2-Cycle Load Delay:**  
  There’s a **two-cycle delay** from when a load instruction is issued to when data becomes available.  
  This creates a hazard since the CPU can’t use the result immediately.

- **Solution:**  
  Invalidate the next **two instructions** after a load.  
  This is known as **load shadowing**, similar to branch delay handling.

- **PC Redirection:**  
  Redirect the PC to skip the invalid instructions and replay them after the load has completed.

---

### 🧠 5. **Bypassing Logic**

- **Register File Bypass:**  
  Ensure that once the load data is available (after 2 cycles), it can be bypassed to subsequent instructions without waiting for it to be written to the register file.

- **This allows:**  
  - Immediate use of the loaded data.  
  - Prevention of data hazards by forwarding the result.

---

### 🧪 6. **Implementation Plan**

#### ✅ Phase 1: Handle Load Hazards & PC Redirection
- Invalidate 2 cycles after load using the valid signal.
- Redirect PC to retry instructions.

#### ✅ Phase 2: Load Data Writeback
- Write data to `rd` **2 cycles after** the instruction.
- Use a mux to select:
  - ALU result for regular instructions.
  - Load data for invalid instructions in the load shadow.

#### ✅ Phase 3: Register File Index Handling
- Register write-back should target `rd` from **2 cycles ago**.
- Use a delayed version of `rd` to select the correct register.

#### ✅ Phase 4: Data Memory Integration
- Instantiate 16-entry memory macro:
  - Each entry: 32 bits.
  - Support 1 read or 1 write per cycle.
- Hook up signals:
  - Address from ALU.
  - Write enable for store.
  - Read enable for load.
  - Write data from `rs2`.

---

### 🧪 7. **Testing Load & Store Instructions**

#### 🧬 Test Program:

```assembly
addi r10, x0, 10     # r10 = 10 (some computed value)
sw r10, 4(x0)        # Store r10 at address 4
lw r15, 4(x0)        # Load r15 from address 4
```

#### ✅ Modify Test Logic:
- Instead of checking `r10`, check if **`r15 == 10`** for passing condition.
- This confirms:
  - Store worked correctly.
  - Load happened with the correct delay.
  - Loop terminated properly.

---

### 🪂 8. **Jump Instructions (JAL, JALR)**

#### 🚀 JAL (Jump and Link)

- **Target:** `PC + immediate`
- **Behavior:** Unconditional jump.
- Set `rd = return address` (next instruction).
- Redirect PC after jump.
- Invalidate next 2 instructions (same as load/branch).

#### 🌀 JALR (Jump and Link Register)

- **Target:** `rs1 + immediate`
- Requires bypassing for `rs1` to compute the target.
- Use a new or shared adder to compute address.
- Clear lowest bit of the result (bit 0 = 0).

---

### 🔁 9. **Implementation Steps for Jumps**

1. **Define Jump Signals:**
   - `is_jump = is_jal | is_jalr`

2. **Target Calculation:**
   - JAL: use existing PC + imm adder.
   - JALR: use new adder or add mux to select input.

3. **Redirect PC and Invalidate:**
   - As with branches and loads.

4. **Write Return Address:**
   - `rd = PC + 4`, written back to register file.

5. **Testing:**
   - Add a jump instruction to skip a label.
   - Confirm return address is stored.
   - Check if PC redirected correctly.

---
## FINAL OUTCOME
### CODE
       |cpu
      @0
         $reset = *reset;
         
         //use a valid signal that becomes high every 3 clock cycles, 
         //we already have 2 stages of pipeline, just add one more, and check that value
         //this is a better approach than implementing a counter, cuz 1. pipeline already there, 
         //make use of it, 2. every 3 cycles, 3 is odd, so harder to implement a counter (even number is easier)
         //valid signal must be initialised using start signal
         //start = 1, right after reset is asserted, and = 0 o/w
         //this start signal seems a bit diff from what is shown in the vid, in that - it is 1 throughout the period reset = 1 
         
         $start = $reset ? 1'b0 : (>>1$reset) ? 1'b1 : 1'b0 ; //if curr reset = 1, start = 0, else, prev reset = 1, start = 1, else start = 0
         
         //remove this valid signal @0, cuz now 1 instr needs to be supported every cycle
         //$valid = $reset ? 1'b0 :
         //         $start ? 1'b1 :
         //         (>>3$valid) ; //valid value 3 cycles before current cycle
         ////how to use this? 
         ////1. avoid writing into reg file when invalid (valid = 0)
         ////2. update pc every 3 cycles
         
         
         //curr pc = if reset, =0, else if 3 cycles ago, valid branch taken, = branch target addr calc 3 cycles ago, else curr pc = 3 cycles ago pc + 4
         //this is done because the cpu executes 1 instr every 3 cycles, so prev instr requires pc value 3 cycles ago
         //now, support inc of pc every cycle
         //it is assumed that branch is taken 3 cycles ago only - cuz it will be computed @3
         $pc[31:0] = (>>1$reset) ? 32'd0 : 
                     (>>3$taken_br) ? (>>3$br_tgt_pc): 
                     (>>1$pc + 32'd4);
      
      @1
         //INSTRUCTION FETCH - START
         $imem_rd_en = ! $reset;  //active low reset 
         $imem_rd_addr[7:0] = $pc / 4 ; 
         //get the instruction from the instr mem
         $instr[31:0] = $imem_rd_data[31:0]; 
         //INSTRUCTION FETCH - END

         //INSTRUCTION DECODE - START
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

         //imm field is not valid for rtype
         $imm_valid = $is_r_instr ? 1'b0 : 1'b1 ; 
         ?$imm_valid
            $imm[31:0] = $is_i_instr ? { {21{$instr[31]}}, $instr[30:20] } : //12 bits for i type 
                        $is_s_instr ? { {21{$instr[31]}}, $instr[30:25], $instr[11:7] } : //12 bits for s type 
                        $is_b_instr ? { {20{$instr[31]}}, $instr[7], $instr[31:25], $instr[11:8], 1'b0 } : //13 bits for b type 
                        $is_u_instr ? { $instr[31:12] , 12'b0 } : //20 bits for u type 
                        $is_j_instr ? { {12{$instr[31]}}, $instr[19:12], $instr[20], $instr[30:21], 1'b0 } : //21 bits for j type 
                        32'b0 ; 


            //funct7 is valid only for rtype
         $funct7_valid = $is_r_instr ? 1'b1 : 1'b0 ; // condition ? if true : if false
         ?$funct7_valid
            $funct7[6:0] = $instr[31:25]; 

         $u_or_j = $is_u_instr || $is_j_instr ; 

         //funct3 is invalid for u type and j type
         $funct3_valid = $u_or_j ? 1'b0 : 1'b1 ;
         ?$funct3_valid
            $funct3[2:0] = $instr[14:12];

         //rs1 is invalid for u type and j type
         $rs1_valid = $u_or_j ? 1'b0 : 1'b1 ;
         ?$rs1_valid
            $rs1[4:0] = $instr[19:15]; 

         $rs2_valid = ($u_or_j || $is_i_instr) ? 1'b0 : 1'b1; 
         ?$rs2_valid
            $rs2[4:0] = $instr[24:20]; //source reg 2

         //rd is invalid for stype and btype
         $rd_valid = ($is_s_instr || $is_b_instr) ? 1'b0 : 1'b1; 
         ?$rd_valid
            $rd[4:0] = $instr[11:7]; //destination reg

         $opcode[6:0] = $instr[6:0]; 

         $dec_bits[10:0] = { $funct7[5], $funct3, $opcode }; 

         //arithmetic instrs, shift and logical 
         $is_add = $dec_bits == 11'b0_000_0110011; 
         $is_sub = $dec_bits ==? 11'b1_000_0110011;
         $is_sll = $dec_bits ==? 11'b0_001_0110011;
         $is_slt = $dec_bits ==? 11'b0_010_0110011;
         $is_sltu = $dec_bits ==? 11'b0_011_0110011;
         $is_xor = $dec_bits ==? 11'b0_100_0110011;
         $is_srl = $dec_bits ==? 11'b0_101_0110011;
         $is_sra = $dec_bits ==? 11'b1_101_0110011;
         $is_or = $dec_bits ==? 11'b0_110_0110011;
         $is_and = $dec_bits ==? 11'b0_111_0110011;

         //branch variants 
         $is_beq = $dec_bits ==? 11'bx_000_1100011;
         $is_bne = $dec_bits ==? 11'bx_001_1100011;
         $is_blt = $dec_bits ==? 11'bx_100_1100011;
         $is_bge = $dec_bits ==? 11'bx_101_1100011;
         $is_bltu = $dec_bits ==? 11'bx_110_1100011;
         $is_bgeu = $dec_bits ==? 11'bx_111_1100011;
         
         //u type instrs
         $is_lui = $dec_bits ==? 11'bx_xxx_0110111;
         $is_auipc = $dec_bits ==? 11'bx_xxx_0010111;
         
         //jump instrs - j type/i type
         $is_jal = $dec_bits ==? 11'bx_xxx_1101111;
         $is_jalr = $dec_bits ==? 11'bx_xxx_1100111;
         
         //load - all load variants treated the same, so check opcode only
         $is_load = $opcode ==? 7'b0000011; 
         
         //stores - stype instructions
         $is_sb = $dec_bits ==? 11'bx_000_0100011;
         $is_sh = $dec_bits ==? 11'bx_001_0100011;
         $is_sw = $dec_bits ==? 11'bx_010_0100011;
         
         //set less than varaints
         $is_slti = $dec_bits ==? 11'bx_010_0010011;
         $is_sltiu = $dec_bits ==? 11'bx_011_0010011; 
         
         //logical and shift instructions - itype
         $is_addi = $dec_bits ==? 11'bx_000_0010011;
         $is_xori = $dec_bits ==? 11'bx_100_0010011;
         $is_ori = $dec_bits ==? 11'bx_110_0010011;
         $is_andi = $dec_bits ==? 11'bx_111_0010011;
         $is_slli = $dec_bits ==? 11'b0_001_0010011;
         $is_srli = $dec_bits ==? 11'b0_101_0010011;
         $is_srai = $dec_bits ==? 11'b1_101_0010011;
         
         
         

            //INSTRUCTION DECODE - END
      @2
         //REGISTER FILE interfacing
         //INPUT SIGNALS - reg file

         //$rf_wr_data[31:0] = $rf_wr_en ? (>>1$result) : 32'd0; //this can't be done @2, it has to be done after alu calc the result, so @3
         //if this stays here, no error, but you'll never get the answer - it never passes

         //read signals
         $rf_rd_en1 = $rs1_valid; 
         $rf_rd_index1[4:0] = $rs1; //address of source reg 1
         $rf_rd_en2 = $rs2_valid; 
         $rf_rd_index2[4:0] = $rs2; //address of source reg 2


         //OUTPUT SIGNALS - reg file - inputs to the ALU
         //adding reg file bypass to handle read after write hazard
         //if previously rd was written into reg file AND previous instr rd == rs1 or rs2, then select previous result (2 cycles ago) as src1 or src2 for this instr
         //why 2 cycles ago? refer to the diagram to understand (video)

         $select1 = >>2$rf_wr_en && (>>1$rd == $rs1); 
         $select2 = >>2$rf_wr_en && (>>1$rd == $rs2);
         $src1_value[31:0] = $select1 ? (>>1$result) : $rf_rd_data1; //rs1 value
         $src2_value[31:0] = $select2 ? (>>1$result) : $rf_rd_data2; //rs2 value
         
         $br_tgt_pc[31:0] = $pc + $imm ; 
         //REGISTER FILE - END
      @3
         //ALU - START
         //operations: add, addi (others are added later)
         
         $sltu_res[31:0] = $src1_value < $src2_value ;
         $sltiu_res[31:0] = $src1_value < $imm ; 
         $srai_res[31:0] = { {32{$src1_value[31]}}, $src1_value } >> $imm[4:0]; 
         $sra_res[31:0] = { {32{$src1_value[31]}}, $src1_value } >> $src2_value[4:0]; 
         $slt_res[31:0] = ($src1_value[31] == $src2_value[31]) ? $sltu_res : {31'b0, $src1_value[31]};
         $slti_res[31:0] = ($src1_value[31] == $imm[31]) ? $sltiu_res : {31'b0, $src1_value[31]};
         
         $result[31:0] = $is_addi ? ($src1_value + $imm) : //rd <-- rs1 + imm
                         $is_add ? ($src1_value + $src2_value) : //rd <-- rs1 + rs2
                         $is_andi ? ($src1_value & $imm) :
                         $is_ori ? ($src1_value | $imm) :
                         $is_xori ? ($src1_value ^ $imm) :
                         $is_slli ? ($src1_value << $imm[5:0]) : //why 5:0 ?
                         $is_srli ? ($src1_value >> $imm[5:0]) :
                         $is_and ? ($src1_value & $src2_value) :
                         $is_or ? ($src1_value | $src2_value) :
                         $is_xor ? ($src1_value ^ $src2_value) :
                         $is_sub ? ($src1_value - $src2_value) :
                         $is_sll ? ($src1_value << $src2_value[4:0]) :
                         $is_srl ? ($src1_value >> $src2_value[4:0]) :
                         $is_sltu ? $sltu_res :
                         $is_sltiu ? $sltiu_res :
                         $is_lui ? {$imm[31:12], 12'b0} :
                         $is_auipc ? ($pc + $imm) :
                         ($is_jal || $is_jalr) ? ($pc + 32'd4) :
                         $is_srai ? $srai_res :
                         $is_sra ? $sra_res :
                         $is_slt ? $slt_res :
                         $is_slti ? $slti_res :
                         32'bx ; //if none of the above instrs, result is don't care
         //ALU - END
         
         $valid =  ! (>>1$taken_br || >>2$taken_br);
         //write signals 
         //include the valid signal in the wr_en signal - disable write when valid = 0
         $rf_wr_en = ($rd == 5'd0 || (! $valid)) ? 1'b0 : $rd_valid;  
         $rf_wr_index[4:0] = $rd; //address of the dest reg
         //valid signal shifted here
         //given prev two instructions are not branch, valid signal will be high
         
         $rf_wr_data[31:0] = $rf_wr_en ? ($result) : 32'd0; //write result into reg file write after calc it
         
         //logic to specify if a branch is taken or not
         $taken_br = (! $is_b_instr) ? 1'b0 :
                     $is_beq ? ($src1_value == $src2_value) :
                     $is_bne ? ($src1_value != $src2_value) :
                     $is_blt ? ( ($src1_value < $src2_value) ^ ($src1_value[31] != $src2_value[31]) ) : // for signed comparisons
                     $is_bge ? ( ($src1_value >= $src2_value) ^ ($src1_value[31] != $src2_value[31]) ) : //for signed comparison
                     $is_bltu ? ($src1_value < $src2_value) : //unsigned comparisons only
                     $is_bgeu ? ($src1_value >= $src2_value) : //usigned comparisons
                     1'b0 ; //default condition
         //to accomodate valid signal - removed now
         //$valid_taken_br = ($valid) && ($taken_br);
         // Assert these to end simulation (before Makerchip cycle limit).
         //*passed = *cyc_cnt > 40;
        //how to tell makerchip that the simulation has passed? 1. manually check the waveforms, 2. do the following (a simple check)
       *passed = |cpu/xreg[10]>>5$value == (1+2+3+4+5+6+7+8+9) ; //monitors xreg[10], when the value reaches the RHS at say nth cycle, it tells it on (n+5)th cycle
        //tell - means, passed msg appears in LOG
       *failed = 1'b0;
   
      // Macro instantiations for:
      //  o instruction memory
      //  o register file
      //  o data memory
      //  o CPU visualization

# Diagram
![Screenshot 2025-04-11 073213](https://github.com/user-attachments/assets/c0dd20ea-96f5-40c7-b088-f984e27a7bb4)
# waveform
![Screenshot 2025-04-11 073222](https://github.com/user-attachments/assets/2697b980-ca06-4f7d-8b94-5473ae646608)
