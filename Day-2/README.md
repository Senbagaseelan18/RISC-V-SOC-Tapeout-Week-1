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
   - 📜 Liberty Snippet Comparison  
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

### 📂 Liberty Snippet Comparison – AND Gate Flavors

Below is how the `.lib` describes different **drive strengths** of the same `AND2` gate in **SKY130**:  

| Parameter | AND2_0 🐢 | AND2_2 ⚖️ | AND2_4 🚀 |
|-----------|-----------|-----------|-----------|
| **Area**  | `6.25`    | `7.50`    | `8.75`    |
| **Function** | `(A1 & A2)` | `(A1 & A2)` | `(A1 & A2)` |
| **Delay** | High ⏱️ | Medium ⏱️ | Very Low ⚡ |
| **Power** | Very Low 🔋 | Moderate 🔋 | High 🔋 |
| **Speed** | Very Slow 🐢 | Medium ⚖️ | Very Fast 🚀 |

---

### 📜 Example Liberty Snippets

```liberty
cell ("sky130_fd_sc_hd__and2_0") {
  area : 6.25;
  pin(A1) { direction : input; }
  pin(A2) { direction : input; }
  pin(X)  { direction : output;
            function : "(A1 & A2)"; }
}

cell ("sky130_fd_sc_hd__and2_2") {
  area : 7.50;
  pin(A1) { direction : input; }
  pin(A2) { direction : input; }
  pin(X)  { direction : output;
            function : "(A1 & A2)"; }
}

cell ("sky130_fd_sc_hd__and2_4") {
  area : 8.75;
  pin(A1) { direction : input; }
  pin(A2) { direction : input; }
  pin(X)  { direction : output;
            function : "(A1 & A2)"; }
}
```
### ✅ Key Takeaways

- `.lib` = the **blueprint of standard cells** used in synthesis  
- Defines **delay, power, and functionality** at specific PVT corners  
- Same gate comes in **different drive strengths (flavors)** for trade-offs  
- Example: `sky130_fd_sc_hd__tt_025C_1v80.lib` → typical corner for SKY130 flow  

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

Let’s start with two simple gates, **AND** and **OR**, kept as independent modules, and then instantiate them in a top-level module.

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

<p align="center"> <img src="path_to_your_image/hier_netlist.png" width="600" alt="Hierarchical Netlist"/> </p>

📌 In this approach:  
- Each gate remains **visible** in the final netlist.  
- Debugging is easier as **boundaries** are maintained.  

<p align="center">
  <img src="path_to_your_image/hier_blocks.png" width="600" alt="Hierarchical Block Diagram"/>
</p>
```
---

### 🔹 Yosys Flow for Hierarchical Netlist

```bash
# Start yosys
yosys

# Load Liberty
read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib

# Load Verilog files
read_verilog top_hier.v

# Run synthesis
synth -top top_hier

# Map cells using Liberty
abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib

# Show hierarchy preserved
show top_hier

# Write netlist with hierarchy
write_verilog -noattr top_hier_netlist.v
```
<p align="center"> <img src="path_to_your_image/hier_netlist.png" width="600" alt="Hierarchical Netlist"/> </p>

🔹 Yosys Flow for Hierarchical Netlist
# Start yosys
yosys

# Load Liberty
read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib

# Load Verilog files
read_verilog top_hier.v

# Run synthesis
synth -top top_hier

# Map cells using Liberty
abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib

# Show hierarchy preserved
show top_hier

# Write netlist with hierarchy
write_verilog -noattr top_hier_netlist.v

<p align="center"> <img src="path_to_your_image/hier_netlist.png" width="600" alt="Hierarchical Netlist"/> </p>
🔹 Flat Example (Collapsed)
module top_flat(input X1, X2, X3, X4,
                output Y_and, Y_or);
    assign Y_and = X1 & X2;
    assign Y_or  = X3 | X4;
endmodule


📌 In this approach:

The design is optimized globally.

But module boundaries disappear, making debug and reuse harder.

<p align="center"> <img src="path_to_your_image/flat_blocks.png" width="600" alt="Flat Block Diagram"/> </p>
🔍 Netlist Comparison
<div align="center">
Feature	Hierarchical Netlist	Flat Netlist
Readability	Clear module boundaries	One large block
Debugging	Easy to trace signals	Harder to isolate signals
Reuse	IP blocks can be reused	No modular reuse
Optimization	Limited to module scope	Global cross-optimization
Compile Time	Faster for large designs	Slower for huge designs
</div>
🖼️ Visualization in CMOS
<p align="center"> <img src="path_to_your_image/cmos_compare.png" width="600" alt="CMOS Visualization"/> </p>

🔑 Note: Stacked PMOS devices are inefficient due to poor mobility, hence synthesis tools often restructure logic to optimize for this.

🚀 Key Takeaways

Hierarchical synthesis → modular, reusable, easier debug

Flat synthesis → globally optimized, smaller area but harder debug

Choice depends on design size, reuse needs, and optimization goals
