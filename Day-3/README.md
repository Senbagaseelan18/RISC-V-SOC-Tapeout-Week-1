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

## 🔹 B. Constant Propagation  

Constant Propagation is an optimization technique where **constant values are substituted** into a circuit to simplify logic.  
If an input is fixed to **0 or 1**, entire gates may become redundant. This reduces **logic depth, transistor usage, and power consumption**.  

---

### 🧮 Example Circuit  

![Constant Propagation Example](images/constant_propagation.png)  

Equation:  

\[
y = \overline{(a \cdot b) + c}
\]  

---

### 🔽 Reduction Steps  

Case: \(a = 0\) (constant input).  

\[
\begin{aligned}
y &= \overline{(a \cdot b) + c} \\
  &= \overline{(0 \cdot b) + c} \\
  &= \overline{0 + c} \\
  &= \overline{c} \\
  &= c'
\end{aligned}
\]  

Thus the **AND + NOR combination** is replaced with a **single inverter**.  

---

### ⚡ Transistor Count (Static CMOS Assumption)  

- **AND gate (2-input)** → NAND (4 tr) + INV (2 tr) = **6 transistors**  
- **NOR gate (2-input)** → **4 transistors**  
- **Original circuit** (AND + NOR) → **10 transistors**  
- **Optimized circuit** (only inverter for c') → **2 transistors**  

✅ **Net Reduction = 8 transistors**  

---

### 📊 Notes  

- Place your circuit diagram at:  
  `images/constant_propagation.png`  
- The diagram should show:  
  - **Left (Original):** Inputs a, b → AND gate → output + input c → NOR gate → y  
  - **Right (Optimized):** Input c → inverter → y  
  - Annotate transistor counts (10 vs 2)  


