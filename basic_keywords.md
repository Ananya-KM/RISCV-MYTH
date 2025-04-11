# RISCV BASIC KEYWORDS
### **Section Overview**

This section introduces the concept of **Instruction Set Architecture (ISA)** — the fundamental language that computers understand to perform operations. ISA defines how software communicates with the hardware.

---

### **Key Concepts**

---

#### **C Program to Hardware Execution**

A high-level program written in **C language** is first converted into **assembly language** using a compiler. The assembler then transforms the assembly instructions into **machine code**, which is binary (1s and 0s). This binary code is executed directly by the **hardware (CPU)** embedded in devices like laptops, producing the final output.

---

#### **Program Flow**

The flow of a program from high-level code to hardware execution involves multiple stages:
```text
C Program → Assembly Language → Machine Code → Execution on Chip → Output
```
This sequence allows human-readable code to be translated into **low-level binary instructions** that are executed by physical components of the computer.

---

#### **Role of Hardware Description Language (HDL)**

HDL is a specialized language used to describe and design **hardware architecture**. It enables developers to define how hardware behaves logically. Through HDL, high-level architectural functions are implemented using **logic gates** and **Register Transfer Level (RTL)** constructs. Tasks like arithmetic operations or swapping numbers are implemented via HDL, which is later mapped to physical components in hardware.

---

### **Real-World Implementation**

In practice, HDL designs are **synthesized into physical hardware layouts** like circuits and chips. When a user runs a program, it interacts with the hardware designed using HDL. The chip then executes the corresponding machine instructions and provides the required output.

---

### **System Software, ISA, and Application Interaction**

---

#### **Application and System Software Overview**

**Application software** includes user-facing programs that run on laptops and mobile devices. These rely on **system software** (e.g., OS, compiler, assembler) to bridge the gap between high-level application logic and hardware-level execution. System software manages how instructions are translated and passed to the processor.

---

#### **Role of the Operating System**

The **Operating System (OS)** is responsible for resource allocation, memory management, and task scheduling. It also plays a critical role in converting application code into a format that hardware can understand by coordinating with the compiler and assembler.

---

#### **Compiler and Instruction Set Architecture (ISA)**

The **compiler** translates high-level programming languages like C into **ISA-level instructions**, which are tailored to specific hardware architectures such as RISC-V or x86. The ISA defines the structure and syntax of the instructions used by a particular processor.

---

#### **Assembler and Machine Language**

The **assembler** takes the compiler's ISA instructions and converts them into **binary machine code**. This machine code is the actual set of signals (0s and 1s) that control the hardware elements like **logic gates** and **flip-flops**.

---

#### **Example of Stopwatch App**

A C program that implements a **stopwatch function** is compiled into ISA instructions. These are then assembled into binary instructions, which interact with the chip’s hardware to execute the stopwatch's functionality.

---

### **ISA as an Interface**

The **Instruction Set Architecture (ISA)** serves as the crucial **interface** between software and hardware. It abstracts the hardware's internal complexity and offers a standardized instruction format that can be used across different software programs to execute tasks on a particular hardware setup.

---

### **High-Level Design to Physical Design**

The ISA is defined using **HDL**, which is used to describe the logic and behavior of the hardware. HDL is then synthesized into physical hardware elements such as **flip-flops**, **gates**, and **combinational circuits**. This process transitions from abstract logic to **real, fabricated silicon chips**.

---

### **Three Stages of Implementation**

The path from a high-level program to physical execution involves:
1. **ISA and Architecture** – specifying the expected hardware functionality.
2. **HDL Implementation** – designing the hardware behavior in HDL.
3. **Physical Design** – implementing the HDL design into **physical electronic components**.

---

## 📘 Course Content

---

### **Basic Operations**

The course begins with foundational arithmetic operations such as **addition**, **multiplication**, and **division**. These are essential for more complex applications like **scientific calculators**. The compiler translates these operations into machine instructions that the processor executes.

---

### **Instruction Sets**

Different processors use different instruction sets:
- **ARM-based systems** use **ARM 64-bit instructions**.
- **RISC-based systems** follow **RISC-specific instructions**.

ARM 64 instructions are used for processing 64-bit data and include operations specific to **integer arithmetic**.

---

### **Instruction Extensions**

Instruction sets can be extended to include more complex operations like **multiplication** and **division**. In ARM systems, these are known as **ARM 64 MCP (Multiplication and Control Program)** instructions, which extend the base capabilities of the processor.

---

### **Floating-Point Instructions**

Floating-point operations are handled through **floating-point extensions**, which include:
- **Single-precision** and **double-precision** instructions.
- Instructions like **DFAW** and **F** indicate floating-point arithmetic operations in ARM 64 architecture.

---

### **Application Binary Interface (ABI)**

The **ABI** defines a standard for how application programs interact with system resources and the processor. It ensures consistency in calling functions, passing data, and accessing hardware functionalities across different applications and system platforms.

---

### **Memory Allocation and Stack**

This section covers how memory is managed during execution. Concepts like the **stack** and **heap** are explored, with emphasis on how variables are stored, accessed, and transferred during program flow, especially in **function calls** and **instruction execution**.

---

### **Signed vs. Unsigned Data**

Processors support both **signed** and **unsigned** integer data types. Understanding how these are represented in binary is essential for correct operation of instructions, especially in arithmetic and comparison operations.

---

### **Compiler to Machine Language Flow**

The end-to-end process of how code runs on hardware:
```text
Source Code (C) → Compiler → ISA Instructions → Assembler → Machine Code (Binary)
```
This transformation chain ensures that user-written programs can be executed efficiently on silicon hardware by breaking down complex logic into binary instructions.

---

