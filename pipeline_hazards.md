# Solutions to Pipeline Hazards:
### 1. **Objective: Transition to Back-to-Back Instruction Execution**
- Improve CPU performance by executing instructions without waiting cycles (currently 3-cycle cadence).
- Two key hazards arise: **register file dependency** and **branch target dependency**.
- Focus is initially on **register file dependency** (Read-After-Write hazard).

---

### 2. **Register File Dependency (RAW Hazard)**
- Happens when one instruction writes to a register and the next instruction tries to read from it before the write completes.
- Leads to incorrect values being read if not handled.

---

### 3. **Solution: Register Bypass**
- Introduces a bypass path from the ALU output of the previous instruction to the input of the current instruction.
- Allows immediate use of results without waiting for them to be written and read from the register file.
- Uses a **multiplexer** to choose between bypassed value and register file value.

---

### 4. **Bypass Logic Implementation**
- Read values from the register file are delayed by two cycles to match timing.
- Multiplexers select:
  - Bypass value if the destination register of the previous instruction matches the source register of the current instruction.
  - Else, regular register file value.

---

### 5. **Maintaining Compatibility with 3-Cycle Cadence**
- The bypass logic is inactive during current 3-cycle operation.
- Ensures that introducing this logic doesn't affect existing simulation results.

---

### 6. **Simulation and Debugging**
- Validate correctness through simulation.
- Ensure the CPU behavior is preserved and bypass logic operates correctly when active.

---

### 7. **Branch Target Handling**
- On taking a branch, the CPU must redirect the PC correctly and invalidate speculative instructions.
- Due to three-cycle latency in decoding, reading, and computing the target, a **two-cycle penalty** is unavoidable.
- The PC redirection path remains a three-cycle loop.

---

### 8. **Branch Handling Implementation**
- **Valid signal logic** is updated to allow valid instructions only when prior branches are not taken.
- PC loop is updated to a 1-cycle loop for normal execution, retaining a 3-cycle loop for branch redirection.

---

### 9. **Final Checks**
- Simulate with the near one-instruction-per-cycle model.
- Validate the bypass and branch handling logic.

---

### 10. **Decode Logic Completion**
- Decode logic interprets machine instructions into control signals.
- Implement remaining instructions of **RV32I base set**, except for:
  - `fence`, `ecall`, and `ebreak`.
- Introduce `is_load` signal to simplify decoding of all load instructions.

---

### 11. **Bogus Use Technique**
- Used to initialize unused control signals to avoid compiler warnings.
- Later removed when the signals are connected to execution logic.

---

### 12. **Completing the ALU**
- ALU handles arithmetic and logic operations.
- Uses Verilog operators for straightforward instruction implementation (add, sub, and, or, etc.).
- Special instructions (`SLT`, `SLTI`, `SLTU`) require comparison logic.
- Use an intermediate signal for unsigned comparisons to simplify expressions.

## LAB Solution
![Screenshot 2025-04-11 073139](https://github.com/user-attachments/assets/4a1e1197-7735-48ca-83fb-ac278b2964fe)
![Screenshot 2025-04-11 073200](https://github.com/user-attachments/assets/f11873b5-9ff1-4009-9216-d799e695fdc1)
