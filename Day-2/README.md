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
# 🌟 Week 1 — Day 2

Timing libraries · Hierarchical vs Flat Synthesis · Flop coding styles

---

## 📚 What this page contains

- A compact, practical guide to timing libraries (.lib) and PVT corners
- Clear comparison and examples for hierarchical vs flat synthesis
- Flop coding styles and synthesis-friendly RTL tips (lab-ready)
- Ready-to-run Yosys snippets and image placeholders you can replace with screenshots

---

## Table of contents
1. Introduction to timing libraries (.lib)
2. PVT corners explained
3. Example: SKY130 library filename breakdown
4. Drive strengths (gate flavors) and liberty snippets
5. Hierarchical vs flat synthesis (with examples)
6. Yosys flows (hierarchical and flat)
7. Flop coding styles & synthesis tips
8. How to reproduce the labs (quick commands)

---

## 1) Introduction — what is a timing library (.lib)?

A timing library (a `.lib` or Liberty file) is the synthesis tool's dictionary of standard cells. It describes each cell's:

- Boolean function
- Pin directions and timing arcs
- Timing (propagation, setup, hold)
- Power (leakage, dynamic switching)
- Physical area

The synthesizer uses `.lib` data to map RTL to technology cells and to estimate timing and power.

---

## 2) PVT corners (Process · Voltage · Temperature)

Semiconductor characteristics depend on process, supply voltage and temperature. Common corners:

| Corner | Voltage | Temperature | Process | Typical implication |
|--------|---------|-------------|---------|---------------------|
| SS     | ~1.60 V | 125 °C      | Slow-Slow | Worst timing, often used for timing pessimism |
| TT     | 1.80 V  | 25 °C       | Typical   | Nominal behavior (functional validation) |
| FF     | ~1.95 V | 0 °C        | Fast-Fast | Best timing, worst-case power/IR drop checks |

Use the appropriate corner when doing static timing analysis and power estimation.

---

## 3) Example: SKY130 library filename

`sky130_fd_sc_hd__tt_025C_1v80.lib`

Meaning:
- `sky130_fd_sc_hd` — SkyWater 130 nm, standard-cell high-density family
- `tt` — typical process corner
- `025C` — 25 °C
- `1v80` — 1.80 V nominal supply

---

## 4) Drive strengths (gate flavors)

Cells are provided in different drive strengths so the tool (or designer) can trade area, delay and power.

| Cell | Area (µm²) | Relative delay | Power |
|------|------------:|---------------:|:-----|
| AND2_0 | 6.25  | slow  | low  |
| AND2_2 | 7.50  | medium| moderate |
| AND2_4 | 8.75  | fast  | higher |

Liberty snippets (simplified) — all three implement the same Boolean function:

```liberty
cell ("sky130_fd_sc_hd__and2_0") {
  area : 6.25;
  pin (A1) { direction : input; }
  pin (A2) { direction : input; }
  pin (X)  { direction : output; function : "(A1 & A2)"; }
}
```

Repeat for `and2_2` and `and2_4` with their respective area numbers.

Key takeaway: choose drive strength to meet timing while minimizing power/area.

---

## 5) Hierarchical vs Flat synthesis

Two common synthesis strategies:

- Hierarchical (module boundaries preserved)
  - Pros: easier to debug, faster incremental runs, IP reuse
  - Cons: limited cross-module optimizations, sometimes larger area

- Flat (all modules collapsed)
  - Pros: global optimization across the whole design, often better area/timing
  - Cons: harder to debug, longer synthesis times for very large designs

When to pick which: use hierarchical flows for large projects and IP reuse; use flat for final optimization passes when timing closure requires it.

### Example (Verilog)

Hierarchical version (modules instantiated):

```verilog
// and_gate.v
module and_gate(input wire A, input wire B, output wire Y);
  assign Y = A & B;
endmodule

// or_gate.v
module or_gate(input wire A, input wire B, output wire Y);
  assign Y = A | B;
endmodule

// top_hier.v
module top_hier(input wire X1, X2, X3, X4, output wire Y_and, Y_or);
  and_gate u_and (.A(X1), .B(X2), .Y(Y_and));
  or_gate  u_or  (.A(X3), .B(X4), .Y(Y_or));
endmodule
```

Flat version (same functionality, collapsed):

```verilog
module top_flat(input wire X1, X2, X3, X4, output wire Y_and, Y_or);
  assign Y_and = X1 & X2;
  assign Y_or  = X3 | X4;
endmodule
```

---

## 6) Yosys flows — quick commands

Below are minimal Yosys sequences for hierarchical and flat flows. Replace the liberty path with your local library path.

Hierarchical synthesis (preserve instantiation hierarchy):

```bash
# start yosys and run these commands inside the yosys shell
read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog and_gate.v or_gate.v top_hier.v
synth -top top_hier
abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show -format png -prefix hier_top
write_verilog -noattr top_hier_netlist.v
```

Flat synthesis (flatten and optimize globally):

```bash
read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog top_flat.v
synth -flatten -top top_flat
abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show -format png -prefix flat_top
write_verilog -noattr top_flat_netlist.v
```

Notes:
- `show` will create PNGs if Graphviz is installed. Replace or remove if not desired.

---

## 7) Flop coding styles & synthesis-friendly RTL tips

- Always use non-blocking assignments for sequential logic (inside clocked always blocks) to avoid race conditions.
- Prefer synchronous reset when timing is tight, asynchronous reset where required by the IP, but document clearly.
- Avoid writing arithmetic with mixed bit-widths without explicit casting—synthesis may infer extra logic.
- Keep combinational logic simple and use pipeline registers to meet timing.

Example (recommended flop style):

```verilog
always @(posedge clk or negedge rst_n) begin
  if (!rst_n)
    q <= 0;
  else
    q <= d; // non-blocking
end
```

---

## 8) How to reproduce the labs (quick guide)

1. Install Yosys (and Graphviz if you want diagrams).
2. Put your `.v` files and the correct `.lib` in a folder.
3. Run the commands from section 6 inside the `yosys` shell.

Example quick terminal run (outside yosys):

```bash
# open yosys then paste the commands from section 6, or run a script:
yosys -p "read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib; read_verilog and_gate.v or_gate.v top_hier.v; synth -top top_hier; abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib; write_verilog -noattr top_hier_netlist.v"
```

---

## Images and screenshots
Replace the placeholders below with your screenshots taken during the lab. Suggested files to add to `Day-2/Images/`:

- `hier_netlist.png` — hierarchical netlist screenshot
- `flat_netlist.png` — flat netlist screenshot
- `yosys_flow.png` — yosys terminal output or flow diagram

Embed images in this README using standard Markdown, e.g.:

```markdown
![Hierarchical netlist](Images/hier_netlist.png)
```

---

## Final notes

This file is intentionally concise and lab-focused. If you'd like, I can:

- Add a complete lab step-by-step with downloadable Verilog and scripts
- Generate PNG diagrams from the examples and embed them here
- Add a short checklist for timing sign-off (STA steps)

Tell me which of the above you'd like next and I'll implement it.
