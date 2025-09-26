<div align="center" style="margin-bottom:20px;">
  <h1 style="font-size:32px; color:#4B0082; font-weight:bold; text-shadow: 2px 2px 4px #aaa;">
    🌟 Day 4 — GLS, Blocking vs Non-Blocking & Simulation Mismatch
  </h1>
  <hr style="width:200px; border:2px solid #FF69B4; margin-top:-10px;">
  <p style="font-size:16px; color:#555;">
    Dive into <b>Gate-Level Simulation (GLS)</b>, understand <b>blocking vs non-blocking assignments</b>, and explore <b>synthesis-simulation mismatches</b> with practical examples in Verilog. ⚡
  </p>
</div>

---

## 📑 Table of Contents

| No. | Main Topic | Subtopics |
|-----|------------|-----------|
| 1 | [GLS Concept and Flow using Icarus Verilog](#1-gls-concept-and-flow-using-icarus-verilog) | Why GLS, GLS with Verilog, simulation flow |
| 2 | [Synthesis-Simulation Mismatch](#2-synthesis-simulation-mismatch) | Missing sensitivity list, Blocking vs Non-Blocking, Non-standard Verilog coding |

---

## 1. GLS Concept and Flow using Icarus Verilog

### 🔹 What is GLS?
- **Gate-Level Simulation (GLS)** is the process of simulating the **post-synthesis netlist** instead of RTL code.  
- It verifies whether the synthesized netlist still behaves the same as the RTL design.  

### 🔹 Why GLS?
- To ensure **functional equivalence** between RTL and gate-level netlist.  
- To catch issues like **uninitialized signals**, **timing behavior**, or **synthesis mismatches** that don’t appear in RTL simulation.  

### 🔹 GLS using Verilog
- With tools like **Icarus Verilog (iverilog)**, we can simulate the synthesized **netlist (.v file)** along with the testbench.  
- Flow:  
  1. Write RTL design and testbench.  
  2. Synthesize design using Yosys → get **netlist.v**.  
  3. Simulate netlist with **iverilog** + testbench.  
  4. Compare results with RTL simulation.  

📌 **Visualization:**  
<p align="center"> 
  <img src="https://github.com/your-repo-name/path-to-images/gls_verilog.png?raw=true" alt="GLS using Verilog" width="600"/> 
</p>

---

## 2. Synthesis-Simulation Mismatch

A **synthesis-simulation mismatch** occurs when the behavior of the design in simulation does not match the behavior of the synthesized hardware.  

### Common Causes:
- **Missing sensitivity list** → Incomplete combinational logic triggers.  
- **Blocking vs Non-blocking assignment** → Misuse inside sequential logic.  
- **Non-standard Verilog coding** → Constructs not supported or misinterpreted by synthesis tools.  

---

### 2.1 Missing Sensitivity List  

If sensitivity list is incomplete, simulation may not update outputs properly even though synthesis will infer correct logic.  

#### ⚠️ Example with Incorrect Sensitivity List
```verilog
// 2:1 MUX with incomplete sensitivity list
module mux_incomplete (input a, input b, input sel, output reg y);
  always @(sel) begin
    if (sel)
      y = a;
    else
      y = b;
  end
endmodule
```

#### 🔴 Problem:
Here, always @(sel) only triggers when sel changes, but ignores changes in a and b. Simulation mismatch occurs because in real hardware, output depends on all inputs.

#### ✅ Corrected Code using (*)

// 2:1 MUX with complete sensitivity list
```verilog
module mux_correct (input a, input b, input sel, output reg y);
  always @(*) begin
    if (sel)
      y = a;
    else
      y = b;
  end
endmodule
```
✔️ Using @(*) ensures that all inputs are automatically included in the sensitivity list, avoiding mismatches.

### 2.2 Blocking vs Non-Blocking Assignment

#### 🔹 Blocking (`=`)
- Executes immediately, in sequential order.  
- Suitable for **combinational logic**.  
- Misuse in sequential (`always @(posedge clk)`) can cause mismatches.  

#### 🔹 Non-Blocking (`<=`)
- Executes in parallel, values update at the end of the time step.  
- Suitable for **sequential logic**.  

---

#### ⚠️ Caveat: Blocking vs Non-Blocking in Sequential Logic  

Blocking assignments (`=`) can cause **simulation vs synthesis mismatch** because:  

- In **RTL simulation**, statements execute **line by line** → later logic may use an "old" value.  
- In **synthesis**, hardware updates **in parallel** → mismatch occurs.  

---

#### 🔴 Problematic Blocking Example  

```verilog
module blocking_caveat (input clk, input a, input b, output reg y);
  reg temp;
  always @(posedge clk) begin
    y = temp & b;   // uses OLD value of temp
    temp = a;       // temp updated afterwards
  end
endmodule
```
Here, y is calculated using the old value of temp during simulation.

But in actual hardware, temp and y would update together, causing mismatch.

### ✅ Corrected Using Non-Blocking

```verilog
module nonblocking_fix (input clk, input a, input b, output reg y);
  reg temp;
  always @(posedge clk) begin
    temp <= a;           // scheduled update
    y <= temp & b;       // now uses updated value correctly
  end
endmodule
```

✔️ Using non-blocking (<=) ensures hardware behavior matches simulation.

