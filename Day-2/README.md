
<style>
/* Page container */
body { font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, 'Helvetica Neue', Arial; color: #0f172a; }
.themed-bg {
  background: linear-gradient(90deg, #f8fafc 0%, #eef2ff 100%);
  border-radius: 16px;
  padding: 28px 20px;
  margin-bottom: 20px;
  box-shadow: 0 6px 24px rgba(14, 165, 233, 0.08);
}
.badges img { margin: 6px 8px; height: 24px; }

/* Callouts */
.callout-info { background: #e0f2fe; border-left: 6px solid #38bdf8; padding: 14px 18px; border-radius: 10px; margin: 14px 0; }
.callout-tip  { background: #f0fdf4; border-left: 6px solid #22c55e; padding: 14px 18px; border-radius: 10px; margin: 14px 0; }
.callout-warn { background: #fffbeb; border-left: 6px solid #f59e0b; padding: 14px 18px; border-radius: 10px; margin: 14px 0; }
.emoji-title { font-size: 1.35rem; font-weight: 600; margin-bottom: 0.15rem; }

/* Section headers */
h1 { font-size: 2.1rem; margin: 0.1rem 0 0.3rem 0; }
h2 { font-size: 1.2rem; color: #0b1220; margin: 0.2rem 0 0.8rem 0; }
h3 { color: #0b1220; }
.section-ribbon { display: inline-block; background: linear-gradient(90deg,#60a5fa,#7c3aed); color: white; padding: 6px 12px; border-radius: 999px; font-weight: 600; margin-bottom: 8px; }

/* Tables */
table { border-collapse: collapse; width: auto; margin: 8px 0 16px 0; }
table th, table td { border: 1px solid #e6eef8; padding: 8px 12px; }
table th { background: #f1f5f9; font-weight: 700; }

/* Code blocks */
pre, code { font-family: ui-monospace, SFMono-Regular, Menlo, Monaco, 'Roboto Mono', 'Courier New', monospace; }
pre { background: #0f172a10; border-radius: 8px; padding: 12px; overflow: auto; }
code.inline { background: #f8fafc; padding: 2px 6px; border-radius: 6px; }

/* Details/summary */
details summary { cursor: pointer; padding: 8px 10px; border-radius: 6px; background: #ffffff; border: 1px solid #eef2ff; }
details[open] summary { box-shadow: inset 0 -1px 0 #eef2ff; }

/* Make markdown anchors look nicer */
a[id] { display: block; position: relative; top: -10px; visibility: hidden; }

/* Responsive improvements */
@media (max-width: 720px) {
  .themed-bg { padding: 18px 14px; }
  .badges img { height: 20px; }
}
</style>

<div class="themed-bg" align="center">
  <h1>🌟 Week 1 — Day 2</h1>
  <h2>Timing Libraries · Hierarchical vs Flat Synthesis · Flop Coding Styles</h2>
  <img src="https://img.shields.io/badge/Tool-Yosys-blue" alt="Yosys" />
  <img src="https://img.shields.io/badge/Tech-SKY130-brightgreen" alt="SKY130" />
  <img src="https://img.shields.io/badge/Level-Intro%2FLab-orange" alt="Lab" />
</div>

<div class="callout-info">
<span class="emoji-title">🚀 At a glance</span><br>
Duration: 1 day (lab + notes) &nbsp;|&nbsp; Focus: <b>.lib</b> (timing libraries), PVT corners, hierarchical vs flat synthesis, and flop coding best practices &nbsp;|&nbsp; Tools: Yosys, Liberty (.lib), optional Graphviz for diagrams
</div>

<div class="callout-tip">
<span class="emoji-title">✨ Why this page?</span><br>
This page turns tedious theory into a short, visual, hands-on lab so you can map RTL to cells, run hierarchical and flat synthesis, and understand how to meet timing.
</div>

## 📚 Table of Contents
1. 🗂️ [Timing libraries (.lib)](#timing-libraries-lib)
2. 🌡️ [PVT corners — quick intuition](#pvt-corners)
3. 🏷️ [SKY130 filename breakdown](#sky130-filename)
4. 🏋️ [Drive strengths & Liberty snippet](#drive-strengths)
5. 🏗️ [Hierarchical vs Flat synthesis — code + tips](#hier-vs-flat)
6. 🔁 [Flop coding styles — concise rules](#flop-styles)
7. 📝 [Cheatsheet & quick commands](#cheatsheet)
8. 🧪 [Quick lab card (run it)](#lab-card)
9. 🖼️ [Images & notes](#images)

---

<a id="timing-libraries-lib"></a>
## 🗂️ Timing libraries (.lib)

A <b>.lib</b> (Liberty) file is the synthesizer's standard-cell dictionary: it describes each cell's Boolean function, pins, timing arcs, power and area. The synthesizer uses this to map RTL to real silicon.

What each cell entry typically contains:
- 🧮 function (Boolean)
- 🔌 pins & directions
- ⏱️ timing arcs (input→output delays)
- ⏳ timing metrics (setup / hold)
- 🔋 power models and area

---

<a id="pvt-corners"></a>
## 🌡️ PVT corners

| Corner | Voltage | Temp | Quick meaning |
|--------|--------:|-----:|:--------------|
| 🐢 SS     | ~1.6 V  | 125 °C | Slow devices — worst-case timing (pessimistic) |
| ⚖️ TT     | 1.8 V   | 25 °C  | Typical / nominal behavior |
| 🚀 FF     | ~1.95 V | 0 °C   | Fast devices — best timing, higher leakage |

<div class="callout-tip">
<b>Tip:</b> Use <b>SS</b> for worst-case STA, <b>TT</b> for functional checks, <b>FF</b> for power/IR margin tests.
</div>

---

<a id="sky130-filename"></a>
## 🏷️ SKY130 filename breakdown

<code>sky130_fd_sc_hd__tt_025C_1v80.lib</code>

- <b>sky130_fd_sc_hd</b> — SkyWater 130 nm, standard-cell (high-density) family<br>
- <b>tt</b> — typical process corner<br>
- <b>025C</b> — temperature = 25 °C<br>
- <b>1v80</b> — operating voltage = 1.80 V

---

<a id="lab-card"></a>
## 🧪 Quick lab card (run this now)

Create a directory <code>lab_day2</code> and copy the example Verilog files and a Liberty file there. Then run the following inside <b>yosys</b> or as a single <code>yosys -p "..."</code> command.

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
- <b>top_hier_netlist.v</b> with instantiated modules<br>
- <b>top_flat_netlist.v</b> with flattened cells

---

<a id="images"></a>
## 🖼️ Images & notes

Drop screenshots into <code>Day-2/Images/</code> and reference them here:

```markdown
![Hierarchical netlist](Images/hier_netlist.png)
![Flat netlist](Images/flat_netlist.png)
```

---

## 🎯 Next steps (pick 1)
1. I can add downloadable Verilog examples + <code>run_yosys.sh</code> (I will create and test the script).<br>
2. I can auto-generate PNG diagrams from the netlists (requires Graphviz locally).<br>
3. I can add a short STA checklist and sample constraint file.<br>

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

