<div align="center">

# 🌟 Week 1 — Day 2  
## Timing Libraries · Hierarchical vs Flat Synthesis · Flop Coding Styles

</div>

---

## 📖 Table of Contents
1. ⏱️ [Introduction to Timing Libraries (.lib)](#-1-introduction-to-timing-libraries-lib)  
   - 🔬 Library Structure  
   - 🌡️ PVT Corners  
   - 📂 Example: `sky130_fd_sc_hd__tt_025C_1v80.lib`  
   - 🏗️ AND Gate Flavors  
2. 🏗️ [Hierarchical vs Flat Synthesis](#-2-hierarchical-vs-flat-synthesis)  
   - 📌 Concept  
   - 🧪 Lab Experiments (Part 1 & 2)  
3. 🔁 [Flop Coding Styles & Optimizations](#-3-flop-coding-styles--optimizations)  
   - 💡 Why Flops?  
   - 🔎 Different Coding Styles  
   - 🧪 Synthesis & Simulation Labs (Parts 1–5)  

---

# ⏱️ 1. Introduction to Timing Libraries (.lib)

A **timing library (`.lib`)** is the *dictionary of standard cells* for synthesis.  
It tells the tool **how each logic gate behaves** in terms of **function, delay, power, and area**.  

---

### 🔬 Library Structure
Inside a `.lib`, each **cell** is described with:
- **Logic equation** (Boolean function)  
- **Pins and directions** (input/output)  
- **Timing data** (setup, hold, propagation delay)  
- **Power models** (leakage & dynamic switching)  
- **Area usage** (layout size)  

---

### 🌡️ PVT Corners
The behavior of silicon changes with **Process, Voltage, Temperature (PVT)**:

| Corner | Voltage | Temp | Process | Behavior |
|--------|---------|------|---------|----------|
| SS | 1.60 V | 125 °C | Slow-Slow | Worst Delay, Low Power |
| TT | 1.80 V | 25 °C  | Typical   | Balanced |
| FF | 1.95 V | 0 °C   | Fast-Fast | Best Delay, High Power |

---

### 📂 Example Library: SKY130
**Filename:**  
`sky130_fd_sc_hd__tt_025C_1v80.lib`  

| Part | Meaning |
|------|---------|
| `sky130_fd_sc_hd` | SkyWater 130nm High-Density Cell Library |
| `tt` | Typical process corner |
| `025C` | Temperature = 25 °C |
| `1v80` | Operating Voltage = 1.80 V |

---

### 🏗️ Example: AND Gate Flavors
The same **2-input AND gate** is available in multiple drive strengths for performance tuning:  

| Cell Flavor | Area (µm²)   | Speed      | Power Consumption | Delay |
|-------------|--------------|------------|-------------------|-------|
| **AND2_0**  | 6.25 × 10⁸   | Very Slow  | Very Low          | High  |
| **AND2_2**  | 7.50 × 10⁸   | Medium     | Moderate          | Medium|
| **AND2_4**  | 8.75 × 10⁸   | Very Fast  | High              | Very Low |

💡 Designers select a flavor depending on **timing closure and power budgets**.

---

### 📂 Liberty Snippet Example

```liberty
cell ("sky130_fd_sc_hd__a2111o_1") {
  area : 6.25;
  pin(A1) { direction : input; }
  pin(A2) { direction : input; }
  pin(B1) { direction : input; }
  pin(C1) { direction : input; }
  pin(D1) { direction : input; }
  pin(X)  { direction : output;
            function : "((A1 & A2) | B1 | C1 | D1)"; }
}
