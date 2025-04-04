# MAKERCHIP PLATFORM
---

## 🎛️ Understanding the Multiplexer (MUX)

## 🔍 What is a Multiplexer?

A **Multiplexer** (or **MUX**) is a **combinational circuit** that selects one of several inputs and sends it to the output.  
Think of it like a **digital switch** controlled by select lines.

---

## 🛠️ 2-to-1 Multiplexer (Basic Example)

### 🔌 Inputs:
- `x1`, `x2`: Two input signals (1-bit each)  
- `s`: Select signal (1 bit)

### 📤 Output:
- `f`: The selected output, based on `s`

### 🧮 Boolean Expression:
If `s = 0`, then `f = x2`  
If `s = 1`, then `f = x1`

### Boolean Expression 

![Screenshot 2025-04-04 113753](https://github.com/user-attachments/assets/a6779bd9-8a12-4f48-9b95-94b73778b26a) </p>
![Screenshot 2025-04-04 113827](https://github.com/user-attachments/assets/eed70035-2375-4a1d-9771-9a2885b54f69)
---

### 💻 Verilog Code using Ternary Operator:
```verilog
assign f = s ? x1 : x2;
```

This means:
- If `s` is 1 → output is `x1`
- If `s` is 0 → output is `x2`

![Screenshot 2025-04-04 113849](https://github.com/user-attachments/assets/31a5deb7-4f6a-4606-a6a3-571369d8d1e6)


## 🔢 4-to-1 Multiplexer (Larger MUX)

We can expand a MUX to have more inputs.

### 🔌 Inputs:
- `a, b, c, d`: Four 1-bit input signals  
- `sel[1:0]`: 2-bit select signal

### 📤 Output:
- `f`: One of the inputs (`a`, `b`, `c`, `d`), chosen based on `sel`

### ✅ Why Ternary Chains are Useful:
- Makes code short and simple
- Easy to read and understand
- Hardware-friendly for synthesis

---

## 🧑‍💻 Makerchip Platform: TL-Verilog IDE

**Makerchip** is an online tool for writing and simulating **TL-Verilog** code — a modern and flexible way to design hardware.

### ✨ Features of Makerchip:
- ✅ **Code Editor**: Write TL-Verilog code right in your browser
- ✅ **Live Simulation**: Run your design and see the results
- ✅ **Circuit Diagrams**: Automatically draw circuits from your code
- ✅ **Waveform Viewer**: Watch how signals change over time
- ✅ **Debugging**: Trace and fix issues easily
- ✅ **Project Sharing**: Save and share projects with links
- ✅ **Built-in Help**: Examples and documentation included

🖼️ *(Add screenshots: example circuits, waveform views, etc.)*

---

## 🧭 Getting Started

### Visit: [Makerchip IDE](https://makerchip.com)
  ![WhatsApp Image 2025-04-04 at 10 30 17 AM](https://github.com/user-attachments/assets/cd5e3af0-a75f-40c6-b3cb-2484d594eec1)

### Try examples from the sidebar
  ![WhatsApp Image 2025-04-04 at 10 30 17 AM (1)](https://github.com/user-attachments/assets/599f29d5-ad00-40e9-8b59-ae0838216ae1) </p>
  ![WhatsApp Image 2025-04-04 at 10 30 17 AM (2)](https://github.com/user-attachments/assets/87e627fa-ef23-48d8-8420-4c8349f59cad)

### Use TL-Verilog to describe your MUX
  ![WhatsApp Image 2025-04-04 at 10 35 22 AM](https://github.com/user-attachments/assets/60fa1955-75e6-40ae-af21-6d2a90a68f58)

### Run and simulate it live!
  ![Screenshot 2025-04-04 114616](https://github.com/user-attachments/assets/ecaf0349-74f3-4dea-aee1-c5956e7bbed2) </p>
  ![WhatsApp Image 2025-04-04 at 10 35 23 AM](https://github.com/user-attachments/assets/32af5bb5-9840-4e15-a2f4-07eda96b8c1e)

---

