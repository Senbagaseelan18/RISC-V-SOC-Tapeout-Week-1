# 🌟 Week 1 — Day 2

<div align="center">
  <h1>Timing Libraries · Hierarchical vs Flat Synthesis · Flop Coding Styles</h1>
</div>

---

<!-- Hero / summary -->
## � At a glance

- Duration: 1 day (lab + notes)
- Focus: `.lib` (timing libraries), PVT corners, hierarchical vs flat synthesis, and flop coding best practices
- Tools: Yosys, Liberty (.lib), optional Graphviz for diagrams

---

## ✨ Why this page? (one-sentence)

This page turns tedious theory into a short, visual, hands-on lab so you can map RTL to cells, run hierarchical and flat synthesis, and understand how to meet timing.

---

## � Table of Contents
1. [Timing libraries (.lib)](#timing-libraries-lib)
2. [PVT corners — quick intuition](#pvt-corners)
3. [SKY130 filename breakdown](#sky130-filename)
4. [Drive strengths & Liberty snippet](#drive-strengths)
5. [Hierarchical vs Flat synthesis — code + tips](#hier-vs-flat)
6. [Flop coding styles — concise rules](#flop-styles)
7. [Cheatsheet & quick commands](#cheatsheet)
8. [Quick lab card (run it)](#lab-card)
9. [Images & notes](#images)

---

## Timing libraries (.lib)

A `.lib` (Liberty) file is the synthesizer's standard-cell dictionary: it describes each cell's Boolean function, pins, timing arcs, power and area. The synthesizer uses this to map RTL to real silicon.

What each cell entry typically contains:
- function (Boolean)
- pins & directions
- timing arcs (input→output delays)
- timing metrics (setup / hold)
- power models and area

---

## PVT corners

| Corner | Voltage | Temp | Quick meaning |
|--------|--------:|-----:|:--------------|
| SS     | ~1.6 V  | 125 °C | Slow devices — worst-case timing (pessimistic) |
| TT     | 1.8 V   | 25 °C  | Typical / nominal behavior |
| FF     | ~1.95 V | 0 °C   | Fast devices — best timing, higher leakage |

Tip: Use SS for worst-case STA, TT for functional checks, FF for power/IR margin tests.

---

## SKY130 filename breakdown

`sky130_fd_sc_hd__tt_025C_1v80.lib`

- `sky130_fd_sc_hd` — SkyWater 130 nm, standard-cell (high-density) family
- `tt` — typical process corner
- `025C` — temperature = 25 °C
- `1v80` — operating voltage = 1.80 V

---

## Drive strengths & Liberty snippet

Cells are available in multiple drive strengths to trade area, delay and power.

| Cell | Area (µm²) | Delay | Power |
|------:|-----------:|------:|:-----:|
| AND2_0 | 6.25 | slow   | low |
| AND2_2 | 7.50 | medium | moderate |
| AND2_4 | 8.75 | fast   | higher |

Simplified Liberty example (shows function + area):

```liberty
cell ("sky130_fd_sc_hd__and2_0") {
  area : 6.25;
  pin (A1) { direction : input; }
  pin (A2) { direction : input; }
  pin (X)  { direction : output; function : "(A1 & A2)"; }
}
```

Design tip: let the tool pick drive strengths by default, then selectively upsize cells on timing-critical paths.

---

## Hierarchical vs Flat synthesis — code + tips

Why it matters: hierarchy controls optimization scope, debug friendliness and compile times.

- Hierarchical (recommended during development)
  - Pros: easier debugging, faster incremental runs, modular IP reuse
  - Cons: limited cross-module optimization

- Flat (use for final optimization)
  - Pros: global optimizations, often better area/timing
  - Cons: harder to debug, longer run-time on large designs

Example — hierarchical (files: `and_gate.v`, `or_gate.v`, `top_hier.v`):

```verilog
// and_gate.v
module and_gate(input wire A, input wire B, output wire Y);
  assign Y = A & B;
endmodule

// top_hier.v (instantiates modules)
module top_hier(input wire X1, X2, X3, X4, output wire Y_and, Y_or);
  and_gate u_and (.A(X1), .B(X2), .Y(Y_and));
  // other instances...
endmodule
```

Flat equivalent (all collapsed):

```verilog
module top_flat(input wire X1, X2, X3, X4, output wire Y_and, Y_or);
  assign Y_and = X1 & X2;
  assign Y_or  = X3 | X4;
endmodule
```

Quick tip: keep hierarchy in early iterations for debugging; flatten for last-mile timing closure.

---

## Flop coding styles — concise rules

- Use non-blocking assignments (<=) in sequential always blocks.
- Use synchronous resets where timing predictability is required.
- Avoid mixing blocking and non-blocking assignments in the same always block.
- Initialize registers in reset paths, not with implicit inference.

Good pattern:

```verilog
always @(posedge clk or negedge rst_n) begin
  if (!rst_n)
    q <= 0;
  else
    q <= d;
end
```

Bad pattern examples and explanations are left for exercises.

---

## Cheatsheet — common pitfalls & fixes

- STA fails? Check the loaded `.lib` file, clock constraints, and if false paths are set.
- Inferred latch? Look for combinational always blocks missing assignments on some branches.
- Unexpected width expansion? Explicitly size operands in arithmetic operations.

Common Yosys commands recap:

```bash
# load liberty
read_liberty -lib path/to/sky130_fd_sc_hd__tt_025C_1v80.lib

# hierarchical synth
read_verilog and_gate.v or_gate.v top_hier.v
synth -top top_hier

# flat synth
read_verilog top_flat.v
synth -flatten -top top_flat

# technology mapping
abc -liberty path/to/sky130_fd_sc_hd__tt_025C_1v80.lib
```

---

## Quick lab card (run this now)

Create a directory `lab_day2` and copy the example Verilog files and a Liberty file there. Then run the following inside `yosys` or as a single `yosys -p "..."` command.

```bash
# Hierarchical flow
read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog and_gate.v or_gate.v top_hier.v
synth -top top_hier
abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
write_verilog -noattr top_hier_netlist.v

# Flat flow
read_verilog top_flat.v
synth -flatten -top top_flat
abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
write_verilog -noattr top_flat_netlist.v
```

Expected results:
- `top_hier_netlist.v` with instantiated modules
- `top_flat_netlist.v` with flattened cells

---

## Images & notes

Drop screenshots into `Day-2/Images/` and reference them here:

```markdown
![Hierarchical netlist](Images/hier_netlist.png)
![Flat netlist](Images/flat_netlist.png)
```

---

## Next steps (pick 1)
1. I can add downloadable Verilog examples + `run_yosys.sh` (I will create and test the script).
2. I can auto-generate PNG diagrams from the netlists (requires Graphviz locally).
3. I can add a short STA checklist and sample constraint file.

Tell me which and I'll create the files and test them in this workspace.


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

