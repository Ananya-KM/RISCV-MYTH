
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

---

## Free-Running Counter

```tlv
$reset = *reset;
$count = $reset ? 32'd0 : >>1$count + 1;
```

---

## Verilog Value Format

- `<bits>'<base><value>`
- `8'd15` → 8-bit decimal 15
- `4'b1010` → 4-bit binary
- `'0` → zero (auto width)
- `'x` → unknown (not supported in Verilator)

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

---

## Reset in Calculator

- Clears previous value.
- Works like a "clear" button.

---

## TL-Verilog Code

```tlv
\m5_TLV_version 1d: tl-x.org
\m5
\SV
  m5_makerchip_module
\TLV
  $reset = *reset;

  $in1[31:0] = >>1$op;
  $in2[31:0] = $rand2[3:0];
  $sel[1:0] = $rand3[1:0];

  $sum[31:0]  = $in1 + $in2;
  $diff[31:0] = $in1 - $in2;
  $prod[31:0] = $in1 * $in2;
  $quot[31:0] = $in1 / $in2;

  $temp[31:0] = ($sel == 2'b00) ? $sum :
                ($sel == 2'b01) ? $diff :
                ($sel == 2'b10) ? $prod : $quot;

  $op[31:0] = $reset ? 32'b0 : $temp;

  *passed = *cyc_cnt > 40;
  *failed = 1'b0;
\SV
  endmodule
```

---
