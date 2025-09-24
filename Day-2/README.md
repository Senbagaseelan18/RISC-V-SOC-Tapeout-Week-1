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

# � Week 1 — Day 2

<div align="center">
  <img src="https://img.shields.io/badge/Tool-Yosys-blue" alt="Yosys" />
  <img src="https://img.shields.io/badge/Tech-SKY130-brightgreen" alt="SKY130" />
  <img src="https://img.shields.io/badge/Level-Intro%2FLab-orange" alt="Lab" />
</div>

> A compact, lab-ready guide to timing libraries, PVT corners, hierarchical vs flat synthesis, and flop coding styles.

---

## Quick Lab Card

📌 Goal: Understand `.lib` files, compare hierarchical vs flat synthesis, and run a minimal Yosys synthesis for both flows.

What you need:
- Yosys installed
- A Liberty file (example: `sky130_fd_sc_hd__tt_025C_1v80.lib`)
- Verilog examples (`and_gate.v`, `or_gate.v`, `top_hier.v`, `top_flat.v`)

Quick commands (paste into a single `yosys -p "..."` call or run inside the yosys shell):

```bash
# Hierarchical (preserve modules)
read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog and_gate.v or_gate.v top_hier.v
synth -top top_hier
abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
write_verilog -noattr top_hier_netlist.v

# Flat (global optimization)
read_verilog top_flat.v
synth -flatten -top top_flat
abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
write_verilog -noattr top_flat_netlist.v
```

---

## Table of Contents
1. Timing libraries (.lib)
2. PVT corners — quick intuition
3. SKY130 filename breakdown
4. Drive strengths & Liberty snippet
5. Hierarchical vs Flat synthesis (visual + code)
6. Flop coding styles — best practices
7. Cheatsheet & common pitfalls
8. Next steps & suggested exercises

---

## 1) Timing libraries (.lib) — in one line

Think of a `.lib` as the standard-cell glossary the synthesizer reads: functions, pins, timing arcs, power and area. Without it, the tool can't map RTL to silicon.

### What a cell entry contains
- function (Boolean)
- pins and directions
- timing arcs (e.g., input-to-output delays)
- timing constraints (setup/hold)
- power models and area

---

## 2) PVT corners — quick intuition

| Corner | Voltage | Temp | What to expect |
|--------|--------:|-----:|:---------------|
| SS     | ~1.6 V  | 125°C | slowest devices → use for worst-case timing checks |
| TT     | 1.8 V   | 25°C  | typical / functional testing |
| FF     | ~1.95 V | 0°C   | fastest devices → useful for power/IR checks |

Tip: Always run STA on the pessimistic corner (SS) for timing closure and on TT for functionality.

---

## 3) SKY130 filename explained

`sky130_fd_sc_hd__tt_025C_1v80.lib` → family `sky130_fd_sc_hd`, corner `tt`, 25°C, 1.80 V.

---

## 4) Drive strengths and Liberty snippet

Cells come in flavors (drive strengths) so you can trade area vs speed vs power.

| Cell | Area | Delay | Power |
|------|-----:|------:|:-----:|
| AND2_0 | 6.25  | slow  | low |
| AND2_2 | 7.50  | medium| moderate |
| AND2_4 | 8.75  | fast  | higher |

Simplified Liberty example:

```liberty
cell ("sky130_fd_sc_hd__and2_0") {
  area : 6.25;
  pin (A1) { direction : input; }
  pin (A2) { direction : input; }
  pin (X)  { direction : output; function : "(A1 & A2)"; }
}
```

Pro tip: let synthesis pick drive strengths automatically, but override manually for critical paths.

---

## 5) Hierarchical vs Flat synthesis — visual + code

Why it matters: hierarchy affects optimization scope, debug convenience, and compile time.

- Hierarchical
  - preserves module boundaries
  - faster incremental runs
  - easier to trace signals

- Flat
  - collapses modules for global optimization
  - can yield better area/timing
  - harder to debug

Example (hierarchical):

```verilog
// and_gate.v
module and_gate(input wire A, input wire B, output wire Y);
  assign Y = A & B;
endmodule

// top_hier.v
module top_hier(input wire X1, X2, X3, X4, output wire Y_and, Y_or);
  and_gate u_and (.A(X1), .B(X2), .Y(Y_and));
  // instantiation keeps module boundary
endmodule
```

Flat equivalent:

```verilog
module top_flat(input wire X1, X2, X3, X4, output wire Y_and, Y_or);
  assign Y_and = X1 & X2;
  assign Y_or  = X3 | X4;
endmodule
```

---

## 6) Flop coding styles — short rules

- Use non-blocking assignments (<=) in sequential logic.
- Prefer synchronous resets for timing predictability.
- Keep combinational blocks pure and avoid latches.
- Name clocks, resets and enables clearly.

Good flop pattern:

```verilog
always @(posedge clk or negedge rst_n) begin
  if (!rst_n)
    q <= 0;
  else
    q <= d;
end
```

Bad patterns: mixing blocking (`=`) and non-blocking (`<=`) in the same sequential block; combinational loops.

---

## 7) Cheatsheet & common pitfalls

- If timing fails: check clock constraints, false paths, and make sure the correct `.lib` is loaded.
- Watch out for inferred latches from incomplete assignments in combinational blocks.
- For multi-bit arithmetic, explicitly size operands to avoid unexpected behavioral widening.

Common commands recap:

```bash
# load liberty
read_liberty -lib path/to/sky130_fd_sc_hd__tt_025C_1v80.lib

# synth (hier)
read_verilog and_gate.v or_gate.v top_hier.v
synth -top top_hier

# synth (flat)
read_verilog top_flat.v
synth -flatten -top top_flat

# technology mapping
abc -liberty path/to/sky130_fd_sc_hd__tt_025C_1v80.lib
```

---

## 8) Next steps & exercises (pick one)

1. Run the hierarchical flow, capture `top_hier_netlist.v`, and inspect the module instances.
2. Run the flat flow and compare gate count and timing to the hierarchical netlist.
3. Deliberately break timing on a path and experiment with bumping drive strength for that path.

If you want, I can add:
- downloadable example Verilog files and a `run_yosys.sh` script
- auto-generated PNG diagrams (if Graphviz available)
- a short STA checklist for sign-off

---

Made this more visual, lab-focused and actionable. Tell me which exercise to add next (examples, scripts or diagrams) and I will create the files and commands for you.

