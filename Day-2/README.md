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
```

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
```

📌 In this approach:  
- Each gate remains **visible** in the final netlist.  
- Debugging is easier as **boundaries** are maintained.  

<p align="center">
  <img src="Images/block.png" width="1000" alt="Hierarchical Block Diagram"/>
</p>

---
### 🔹 Yosys Flow for Hierarchical Netlist

## ▶️ Start Yosys
```bash
yosys
```
## 📂 Load Liberty
```bash
read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
```
## 📂 Load Verilog Files
```bash
read_verilog multiple_modules.v
```
## ⚙️ Run Synthesis
```bash
read_verilog multiple_modules.v
```
## 🔗 Map Cells using Liberty
```bash
abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
```
## 👁️ Show Hierarchy Preserved
```bash
show top_module
```

<p align="center">
  <img src="Images/multimodule.png" width="1000" alt="Hierarchical Schematic"/>
  <br>
  <em> Hierarchical Schematic (Yosys show output) </em>
</p>


## 📝 Write Netlist with Hierarchy
```bash
write_verilog -noattr multiple_modules-hier.v
```
## 👓 Open Netlist in GVim
```bash
!gvim multiple_modules-hier.v
```

<p align="center">
  <img src="Images/code.png" width="1000" alt="Hierarchical Netlist"/>
  <br>
  <em>Hierarchical Netlist (GVim output)</em>
</p>

### ⚙️ CMOS Visualization

- **Stacked PMOS** → Not preferred, as PMOS has **poor carrier mobility**, making circuits slower.  
- **Stacked NMOS** → Generally better since NMOS has **higher mobility**, but too many in series increases resistance.  

---

<p align="center">
  <img src="Day2/Images/cmos.png" width="600" alt="CMOS Visualization"/>
  <br>
  <em>CMOS Transistor-Level Representation</em>
</p>

### 🏗️ C. Flattened Synthesis

In **flattened synthesis**, all modules in a design are merged into a **single flat netlist**, removing the original hierarchy.  
This enables **global optimizations** across the design but makes debugging more challenging.

---

### 🔹 Key Points
- Command `yosys > flatten` collapses the hierarchy.  
- Sub-modules are no longer preserved after flattening.  
- The design is represented as **one unified block**.  
- Useful for **maximizing performance**, but hierarchy visibility is lost.  

---

### ✍️ Writing a Flat Netlist

## 1. Flatten the hierarchy
```bash
flatten
```
## 2. Export netlist
```bash
write_verilog -noattr good_mux_netlist_flat.v
```

## 3. Open with editor
```bash
!gvim good_mux_netlist_flat.v
```
<p align="center"> <img src="Images/flat_file.png" alt="Flattened Netlist File" width="1000"/> </p>

### 📊 Visualizing the Flat Netlist

To see the flat representation after synthesis:

```bash
yosys> flatten
```
```bash
yosys> show
```
<p align="center"> <img src="Images/flat_syn.png" alt="Flattened Schematic" width="1000"/> </p>

## 🔍 Hierarchical vs Flattened Synthesis

| **Aspect**            | **Hierarchical 🧩**             | **Flattened 🏗️**                |
|------------------------|---------------------------------|---------------------------------|
| **Hierarchy**          | Preserved                      | Collapsed                       |
| **Optimization Scope** | Module-level                   | Global                          |
| **Runtime**            | ⚡ Faster (scales for SoCs)     | 🐢 Slower                       |
| **Debugging**          | 🛠️ Easier                      | 🔍 Harder                       |
| **Netlist Style**      | Modular blocks                 | Single block                    |
| **Best For**           | ✅ Debug, modular development   | 🚀 Max performance optimization |

---

## 🌟 Key Takeaway

- Use **hierarchical synthesis** when you want clarity and modularity.  
- Use **flattened synthesis** when you need **maximum optimization** at the cost of runtime and debug simplicity.  

### 🧩 D. Submodule-Level Synthesis

In **submodule-level synthesis**, a single submodule of the design is synthesized independently, while the rest of the hierarchy remains untouched.  
This method is highly effective for **isolated testing, optimization, and debugging** before integrating into the top-level design.

---

### 🔹 Key Highlights
- 🎯 Focuses on **one submodule** at a time.  
- 🧩 Preserves hierarchy of other modules.  
- 📦 Ideal when **multiple instances** of the same module exist.  
- ⚡ Enables a **divide-and-conquer** approach for large designs.  
- ✅ Each submodule can be **optimized, verified, and reused** efficiently.  
- 🚫 Netlist generation at submodule stage is optional (not typically needed for final hardware).  

---

### 🖥️ Yosys Workflow

## 1. Launch Yosys
```bash
yosys
```

# 2. Load standard cell library
```bash
read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
```

# 3. Load Verilog source files
```bash
read_verilog multiple_modules.v
```

# 4. Run synthesis only for selected submodule
```bash
synth -top sub_module1
```

# 5. Visualize synthesized submodule
```bash
show
```
<p align="center"> <img src="Images/submodule_netlist.png" alt="Submodule Netlist Visualization" width="1000"/> </p>

### 🌟 Why Use Submodule Synthesis?

- ✅ Speeds up **debugging** by isolating issues.  
- ✅ Saves time when working on **large SoCs**.  
- ✅ Allows **targeted optimization** of complex blocks.  
- ✅ Supports **incremental verification** at block level.  

