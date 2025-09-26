# 🌟 Day 3 — Introduction to Optimization  

## 📑 Table of Contents  
- [Introduction to Optimization](#introduction-to-optimization)  
  - [Combinational Logic Optimization](#combinational-logic-optimization)  
  - [Sequential Optimization](#sequential-optimization)  

---

## 📝 Introduction to Optimization  
Optimization in digital design focuses on improving the circuit for **area, power, and performance efficiency**. It can be classified into **Combinational Logic Optimization** and **Sequential Optimization**.  

---

## 🔹 Combinational Logic Optimization  
- **Constant Propagation** – Eliminating redundant constants in logic expressions.  
- **Boolean Logic Optimization** – Simplifying Boolean equations to reduce gate count.  

---

## 🔹 Sequential Optimization  

### Basic  
- **Sequential Constant Propagation** – Removing unnecessary sequential logic based on constant values.  

### Advanced  
- **State Optimization** – Reducing redundant states in FSMs.  
- **Sequential Logic Cloning** – Duplicating registers to improve timing/performance.  
- **Retiming** – Repositioning registers for better performance without changing functionality.  

---

## 📊 Classification Diagram  
![Optimization Classification](path/to/your/image.png)  
*(Add your generated diagram in the repo and link here)*  

---

---

## 🔹 B. Constant Propagation  

Constant Propagation is an optimization technique where **constant values are substituted** into a circuit to simplify logic.  
If an input is fixed to **0 or 1**, unnecessary gates can be eliminated. This reduces **circuit size, transistor usage, and power consumption**.  

---

### 🧮 Example Circuit  

![Constant Propagation Example](images/constant_propagation.png)  


Equation:  

```bash
y = ¯((a · b) + c)
```
### 🔽 Reduction Steps  

Case: a = 0 (constant input) 
```bash 
y = ¯((a · b) + c)
  = ¯((0 · b) + c)
  = ¯(0 + c)
  = ¯c
  = c'
```
So, the **AND + NOR implementation** can be reduced to a **single inverter**.  

---

### ⚡ Transistor Count (Your Example)  
![Reduced Transistor Count](images/constant_propagation_reduced.png)

- **Original circuit (AND + NOR)** → **6 transistors**  
- **Optimized circuit (only inverter for c')** → **2 transistors**  

✅ **Net Reduction = 4 transistors**  

---

---

## 🔹 B. Boolean Logic Optimization

**Boolean Logic Optimization** is a technique to **simplify complex Boolean expressions** and logic circuits, which reduces:

- **Gate count**
- **Transistor usage**
- **Power consumption**
- **Delay**

The goal is to represent the same logic function in a **simpler form** using Boolean algebra or Karnaugh maps.

---

### 🧮 Example Circuit

![Boolean Logic Optimization Example](Images/bool.png)  
Original equation:

```verilog
assign y = a ? (b ? c : (c ? a : 0)) : (!c);
```
We want to simplify this into a **reduced equation**.

---

### 🔽 Reduction Steps

**Step 1:** Analyze the nested ternary operator

```verilog
y = a ? (b ? c : (c ? a : 0)) : !c
```

**Step 2:** Consider cases

- If `a = 0` → `y = !c`  
- If `a = 1` → `y = (b ? c : (c ? 1 : 0))`  

**Step 3:** Simplify the inner expression `(b ? c : (c ? a : 0))`

- When `a = 1` → `(b ? c : (c ? 1 : 0))` → `(b AND c) OR (!b AND c)` → `c`  

**Step 4:** Combine with outer `a`

```verilog
y = a ? c : !c
```

**Step 5:** Recognize **XNOR pattern**

```verilog
y = a XNOR c
```

✅ **Final Reduced Equation:**

y = a xnor c

---

## 🔹 C. Sequential Logic Optimization

**Sequential Logic Optimization** aims to simplify circuits that include **memory elements** (like flip-flops) while preserving functionality.  

### 🔹 Sequential Constant Propagation  

This technique propagates **constant values through sequential elements** (like D flip-flops) to simplify logic. If a flip-flop output is constant, connected logic can often be reduced.

---

### 🧮 Example Circuit
![Sequential Constant Propagation  Example](images/rstdff.png)  
- **Setup:**  
  - DFF with **reset active** (Q is 0 after reset)  
  - Q output of DFF → NAND gate input  
  - Another NAND input → `A`  
  - D input of DFF → grounded (`0`)  

- **Behavior:**  
  - After reset, Q = 0  
  - NAND gate output: `Y = !(Q & A) = !(0 & A) = 1` → **constant 1**  

✅ This shows that **sequential constant propagation** can reduce downstream logic because the output is always constant.

---

Sometimes, **sequential constant propagation cannot be applied** because the flip-flop output changes over time with the clock.  

### 🧮 Example Circuit
![Sequential Constant Propagation cannot be applied Example](images/error.png) 
- **Setup:**  
  - DFF with D input → grounded (`0`)  
  - Q output → NAND gate input  
  - Another NAND input → `A`  
  - Clock drives the DFF 
  
![Sequential Constant Propagation  Example](images/rstdff.png) 
- **Behavior:**  
  - On **positive edge of the clock**, Q toggles according to D input  
  - NAND gate output: `Y = !(Q & A)` → **changes between 1 and 0** over time  
  - Output is **not constant**, so **sequential constant propagation cannot be applied**  

✅ This shows that if a flip-flop output is **time-dependent**, downstream logic **cannot be reduced** using sequential constant propagation.

---

## 🔹 E. Advanced Sequential Optimization Techniques

### 1️⃣ State Optimization

**State Optimization** reduces the number of states in a finite state machine (FSM) without changing its functional behavior.  

- Minimizes the **number of flip-flops** required  
- Reduces **area, power, and complexity**  
- Often uses **state encoding techniques**  

**Example:** Reducing an FSM from 8 states to 5 states while maintaining the same outputs for all input sequences.

---

### 2️⃣ Cloning

**Cloning** duplicates parts of combinational or sequential logic to **reduce critical path delay**.  
![Clonig Example](images/clone.png) 
- Improves **timing and throughput**  
- Reduces **fanout load** on critical signals  
- Useful in **high-speed designs**  

**Example:** Duplicating a complex combinational block feeding multiple flip-flops to avoid timing violations.

---

### 3️⃣ Retiming

**Retiming** repositions flip-flops in a sequential circuit to **improve clock frequency or reduce timing violations**.  

- Moves registers across combinational logic  
- Optimizes **critical path delays**  
- Does not change **functional behavior**  

**Example in your design:**
![Retiming_1 Example](images/r1.png) 
![Retiming_2 Example](images/r2.png) 
- Original design operated at **200 MHz**  
- After **retiming and optimizing logic delays**, the circuit now operates at **250 MHz**  

---

---

## 🧪 Lab – Combinational Logic Optimization  

Before trying different optimization cases, let us first explore the **base Verilog design file (`opt_check.v`)**.  
This will help us understand the unoptimized logic and later compare it with optimized netlists.

---

### 🖥️ Yosys Commands

```bash
# 1. Start Yosys
yosys
```
```bash
# 2. Read Liberty timing file
read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
```

```bash
# 3. Read Verilog design files
read_verilog opt_check.v
```

```bash
# 4. Synthesize the design
synth -top opt_check
```

```bash
# 5. Cleaning the Optimized Design
opt_clean -purge
```
```bash
# 6. Generate gate-level netlist
abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
```

```bash
# 7. Visualize the synthesized design
show
```

### ⚡ A. opt_check1 – Constant Propagation
In this example, logic with constant inputs is simplified by the synthesis tool.

#### Example Verilog Code
verilog
module opt_check1 (input a, output y);
  assign y = a?b:0;
endmodule

---
#### Gvim Design Code Visualization

  <p align="center">
    <img src="check_code.png" alt="Design code" width="1000"/>
  </p>

---
#### Design Visualization

  <p align="center">
    <img src="check.png" alt="Schematic.png" width="600"/>
  </p>

---
