<div align="center">

# 🌟 Week 1 — Day 2  
## ⏱️ Timing Libraries · 🏗️ Hierarchical vs Flat Synthesis · 🔁 Flop Coding Styles

</div>

---

## 📖 Table of Contents
- [⏱️ Timing Libraries (.lib)](#⏱️-timing-libraries-lib)  
  - 🔍 Library Structure  
  - 🌡️ PVT Corners  
  - 📂 Example: SKY130 Library  
  - 🏗️ AND Gate Flavors  
  - 📜 Liberty Snippet Comparison  
- [🏗️ Hierarchical vs Flat Synthesis](#🏗️-hierarchical-vs-flat-synthesis)  
  - 📌 Key Concepts  
  - 🧪 Lab Experiments (Part 1 & 2)  
- [🔁 Flop Coding Styles & Optimizations](#🔁-flop-coding-styles--optimizations)  
  - 💡 Why Flops?  
  - 🔎 Coding Styles  
  - 🧪 Synthesis & Simulation Labs (Parts 1–5)  

---

## ⏱️ Timing Libraries (.lib)

Timing libraries (`.lib`) are the **backbone of synthesis**, acting as a *dictionary* for standard cells. They define how each logic gate behaves in terms of **function**, **delay**, **power**, and **area**.

> 💡 **Why it matters**: These libraries guide synthesis tools to optimize designs for speed, power, or area based on project goals.

---

### 🔍 Library Structure
Each **cell** in a `.lib` file includes:
- **Logic Equation**: Defines the Boolean function (e.g., `A1 & A2` for AND gate).  
- **Pin Directions**: Specifies inputs and outputs.  
- **Timing Data**: Includes setup, hold, and propagation delays.  
- **Power Models**: Covers leakage and dynamic power consumption.  
- **Area Usage**: Represents the physical layout size.

---

### 🌡️ PVT Corners
Silicon performance varies with **Process, Voltage, Temperature (PVT)** conditions. Key corners include:

| Corner | Voltage | Temp   | Process     | Behavior                |
|--------|---------|--------|-------------|-------------------------|
| **SS** | 1.60 V  | 125 °C | Slow-Slow   | 🐢 Worst Delay, Low Power |
| **TT** | 1.80 V  | 25 °C  | Typical     | ⚖️ Balanced             |
| **FF** | 1.95 V  | 0 °C   | Fast-Fast   | 🚀 Best Delay, High Power |

> 📌 **Pro Tip**: Always simulate across PVT corners to ensure robust designs!

---

### 📂 Example Library: SKY130
**Filename Breakdown**:  
`sky130_fd_sc_hd__tt_025C_1v80.lib`

| Part              | Meaning                              |
|-------------------|--------------------------------------|
| `sky130_fd_sc_hd` | SkyWater 130nm High-Density Library  |
| `tt`              | Typical Process Corner               |
| `025C`            | Temperature = 25 °C                  |
| `1v80`            | Operating Voltage = 1.80 V           |

---

### 🏗️ AND Gate Flavors
The **2-input AND gate** comes in multiple *drive strengths* to balance **speed**, **power**, and **area**:

| Cell Flavor | Area (µm²) | Speed       | Power Consumption | Delay      |
|-------------|------------|-------------|-------------------|------------|
| **AND2_0**  | 6.25 × 10⁸ | 🐢 Very Slow | 🔋 Very Low       | ⏱️ High     |
| **AND2_2**  | 7.50 × 10⁸ | ⚖️ Medium    | 🔋 Moderate       | ⏱️ Medium   |
| **AND2_4**  | 8.75 × 10⁸ | 🚀 Very Fast | 🔋 High          | ⚡ Very Low |

> 💡 **Design Choice**: Pick a flavor based on **timing closure** and **power budgets**.

---

### 📜 Liberty Snippet Comparison
Below is a comparison of `.lib` snippets for different **AND2 gate flavors** in the **SKY130** library:

| Parameter   | AND2_0 🐢         | AND2_2 ⚖️         | AND2_4 🚀         |
|-------------|-------------------|-------------------|-------------------|
| **Area**    | `6.25`            | `7.50`            | `8.75`            |
| **Function**| `(A1 & A2)`       | `(A1 & A2)`       | `(A1 & A2)`       |
| **Delay**   | ⏱️ High           | ⏱️ Medium         | ⚡ Very Low       |
| **Power**   | 🔋 Very Low       | 🔋 Moderate       | 🔋 High           |
| **Speed**   | 🐢 Very Slow      | ⚖️ Medium         | 🚀 Very Fast      |

**Liberty File Snippets**:

```liberty
cell ("sky130_fd_sc_hd__and2_0") {
  area : 6.25;
  pin(A1) { direction : input; }
  pin(A2) { direction : input; }
  pin(X)  { direction : output; function : "(A1 & A2)"; }
}

cell ("sky130_fd_sc_hd__and2_2") {
  area : 7.50;
  pin(A1) { direction : input; }
  pin(A2) { direction : input; }
  pin(X)  { direction : output; function : "(A1 & A2)"; }
}

cell ("sky130_fd_sc_hd__and2_4") {
  area : 8.75;
  pin(A1) { direction : input; }
  pin(A2) { direction : input; }
  pin(X)  { direction : output; function : "(A1 & A2)"; }
}

## 🏗️ 2. Hierarchical vs Flat Synthesis

In digital design, **synthesis strategy** plays a crucial role in area, timing, and readability of the final gate-level netlist.  
Two popular approaches are **Hierarchical** (modular) and **Flat** (flattened single netlist).  

---

<div align="center">

| 🎯 Approach | 🧩 Concept | 📊 Pros | ⚠️ Cons |
|-------------|------------|---------|---------|
| **Hierarchical** | Break design into **modules** and connect in a top-level | ✔️ Easy debugging <br> ✔️ Reusable IPs <br> ✔️ Faster compile for large designs | ❌ Slightly larger area <br> ❌ Cross-module optimization limited |
| **Flat** | Collapse all modules into a **single netlist** | ✔️ Global optimization <br> ✔️ Better area & timing in some cases | ❌ Harder to debug <br> ❌ No modular reuse |

</div>

---

### 🔹 Hierarchical Example (Modular)

```verilog
// AND Gate
module and_gate(input A, B, output Y);
    assign Y = A & B;
endmodule

// OR Gate
module or_gate(input A, B, output Y);
    assign Y = A | B;
endmodule

// Top Module
module top_hier(input X1, X2, X3, X4,
                output Y_and, Y_or);
    and_gate u1 (.A(X1), .B(X2), .Y(Y_and));
    or_gate  u2 (.A(X3), .B(X4), .Y(Y_or));
endmodule
