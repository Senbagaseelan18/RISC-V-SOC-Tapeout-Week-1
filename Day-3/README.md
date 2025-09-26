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


