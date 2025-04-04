
# Introduction to Logic Gates

This guide is all about **digital logic**, which uses just **1s and 0s** (on and off) to make computers and electronics work.

---

## 🔧 What are Logic Gates?

Logic gates are the **basic building blocks** of all digital circuits.  
They take one or more inputs (1s or 0s) and give an output based on some logic.

Examples:
- **AND** gate: Only gives 1 if *both* inputs are 1  
- **OR** gate: Gives 1 if *any* input is 1  
- **NOT** gate: Flips the input (1 becomes 0, 0 becomes 1)

![Screenshot 2025-04-04 111248](https://github.com/user-attachments/assets/96434e26-dbc1-4d42-abbc-4903d0633bcd)

---

## 🔨 Combinational Logic

These circuits give outputs **only based on current inputs**.  
No memory is used here.

### 🧮 Example: Full Adder

A full adder adds 3 bits together:

- Inputs: A, B, Cin (carry-in)  
- Outputs: S (sum), Cout (carry-out)

![Screenshot 2025-04-04 111303](https://github.com/user-attachments/assets/336a55ae-afa7-47cb-89de-aae447c708d7)
![Screenshot 2025-04-04 111321](https://github.com/user-attachments/assets/14850cd9-2c1c-4f7c-9499-4f5519d31b5e)


---

## ⏳ Sequential Logic

These circuits **remember things** using a clock.

- They use **registers** to store values.
- The output depends on both **current input** and **previous values**.

This is useful when we want to process data over time.

![Screenshot 2025-04-04 111336](https://github.com/user-attachments/assets/4e33ce12-d080-43ae-a67f-0e4b78557cf8)

---

## 🛠️ Pipeline Logic

Pipelining means breaking a task into smaller steps.

Each step runs in a different clock cycle, so the work gets done faster.

> Think of it like an assembly line — each stage does its part and passes it on.
> 

---

## 🧱 Hierarchical Design

We can build small parts (like full adders), and then use them to make bigger things.

> Like using LEGO blocks to build a whole castle!

This helps in organizing big projects better.

![Screenshot 2025-04-04 111350](https://github.com/user-attachments/assets/9df2fe14-009d-46a9-a957-d799a0e53561)

---

## ✏️ TL-Verilog Syntax (Quick Look)
Depending on whether you operate on 1-bit or multi-bit data, syntax may vary. Here’s a quick comparison:
![Screenshot 2025-04-04 111427](https://github.com/user-attachments/assets/55362155-9197-4264-ba38-a81bc08a32bc)



