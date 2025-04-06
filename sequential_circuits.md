
# Sequential Logic and D Flip-Flop 

---

## Sequential Logic

- Uses a clock to control timing.
- Makes output predictable.
- State updates happen on clock edge.

![Screenshot 2025-04-05 115206](https://github.com/user-attachments/assets/94199aaf-cdfc-402a-bc65-2d35ce07fe83)

---

## D Flip-Flop

- Basic memory element.
- Stores 1 bit (0 or 1).
- Captures input on clock’s rising edge.
- Holds value until next clock.


---

## Reset

- Sets circuit to known state.
- Flip-flops can reset to 0 or 1.
- Needed for initialization.

---

## Sequential Circuits as State Machines

- Combines current input + stored state.
- Clock updates the state.
- Output changes based on new state.

![Screenshot 2025-04-05 115222](https://github.com/user-attachments/assets/ac237adb-acd9-42aa-b373-917af42398fb)

---

## Fibonacci Circuit

- Uses two flip-flops.
- Adds two previous values.
- Reset loads initial ones.
- Updates sequence on each clock.
![Screenshot 2025-04-05 115238](https://github.com/user-attachments/assets/4d749461-f37c-4ada-941c-6084715144aa)

## Fibonacci Series Circuit Example:
![Screenshot 2025-04-05 115254](https://github.com/user-attachments/assets/4cc59763-96a3-499c-b3b5-f799c043d3ee)

## Reset in Fibonacci Circuit:
![Screenshot 2025-04-05 115312](https://github.com/user-attachments/assets/a6109320-2946-4bb6-ab7d-c15c4f212d65)

---

## TL-Verilog (Fibonacci)

```tlv
$reset = *reset;
$fib = $reset ? 32'd1 : >>1$fib + >>2$fib;
```
![Screenshot 2025-04-04 084930](https://github.com/user-attachments/assets/d9d44ddd-0ae8-430d-8f6a-f7b57f6f6fba) </p>
![Screenshot 2025-04-04 085134](https://github.com/user-attachments/assets/b178b7e6-bb96-46b0-a041-a0144f9d3e5b)

---
## LABS
## Free-Running Counter

```tlv
$reset = *reset;
$count = $reset ? 32'd0 : >>1$count + 1;
```
![Screenshot 2025-04-05 115312](https://github.com/user-attachments/assets/6a12e2e3-e6b2-4992-892e-e1806315eb08) </P>
![Screenshot 2025-04-04 085445](https://github.com/user-attachments/assets/743c2d5e-38ae-4134-91c2-8d1c6cac4220) </P>
![Screenshot 2025-04-04 085509](https://github.com/user-attachments/assets/3029c966-ee81-4132-b35a-f18f55ca583b)

---

## Verilog Value Format

- `<bits>'<base><value>`
- `8'd15` → 8-bit decimal 15
- `4'b1010` → 4-bit binary
- `'0` → zero (auto width)
- `'x` → unknown (not supported in Verilator)
- 
![Screenshot 2025-04-05 115349](https://github.com/user-attachments/assets/4edb5fb2-7ad7-4c21-8c7b-40d59094d1a0)

---

## Bit Widths

- Default: 32 bits if not specified.
- Auto-truncation may happen.
- Be cautious with mismatched widths.

---

## Calculator (With Flip-Flop)

- Stores previous result.
- Performs add/sub/mul/div.
- Uses selector input.
- 
![Screenshot 2025-04-05 115415](https://github.com/user-attachments/assets/39c656ad-251b-493a-82cd-ae9a188f2425)

---

## Reset in Calculator

- Clears previous value.
- Works like a "clear" button.

---

## LAB EXERCISE

     \m5_TLV_version 1d: tl-x.org
    \m5
  
     // =================================================
     // Welcome!  New to Makerchip? Try the "Learn" menu.
     // =================================================
  
       //use(m5-1.0)   /// uncomment to use M5 macro library.
     \SV
       // Macro providing required top-level module definition, random
        // stimulus support, and Verilator config.
       m5_makerchip_module   // (Expanded in Nav-TLV pane.)
     \TLV
       $reset = *reset;
  
  
     //...
     // Assert these to end simulation (before Makerchip cycle limit).
     //Sequential Calculator
     $in1[31:0] = >>1$op; //Memory Element that stores the previous result
     $in2[31:0] = $rand2[3:0];
     $sel[1:0] = $rand3[1:0];
     $sum[31:0] = $in1 + $in2;
     $diff[31:0] = $in1 - $in2;
     $prod[31:0] = $in1 * $in2;
     $quot[31:0] = $in1 / $in2;
     $temp[31:0] = (($sel == 2'b00) ? $sum : ($sel == 2'b01) ? $diff : ($sel == 2'b10) ? $prod : $quot);
     $op[31:0] = $reset ? 32'b0 : $temp;
     *passed = *cyc_cnt > 40;
     *failed = 1'b0;
     \SV
      endmodule



## OUTPUT

![Screenshot 2025-04-04 090044](https://github.com/user-attachments/assets/63544beb-f6be-4778-8b82-ba96dbea2fa4) </p>
![Screenshot 2025-04-04 085901](https://github.com/user-attachments/assets/79884cd1-28ee-43c0-989d-65b4a82953a1)


