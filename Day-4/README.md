# 📑 Day 4 — GLS, Blocking vs Non-Blocking, and Synth-Sim Mismatch

<div align="center">
  <table cellpadding="10">
    <tr>
      <td align="center">
        <a href="#1️⃣-gls-concept-and-flow">
          <img src="https://img.shields.io/badge/1-GLS%20Concept-blueviolet?style=for-the-badge&logo=verilog" />
        </a>
        <br>GLS Concept & Flow
      </td>
      <td align="center">
        <a href="#2️⃣-synthesis-vs-simulation-mismatch">
          <img src="https://img.shields.io/badge/2-Synth--Sim%20Mismatch-orange?style=for-the-badge&logo=code" />
        </a>
        <br>Synth-Sim Mismatch
      </td>
      <td align="center">
        <a href="#3️⃣-gls-and-netlist-labs">
          <img src="https://img.shields.io/badge/3-GLS%20%26%20Labs-green?style=for-the-badge&logo=verilog" />
        </a>
        <br>GLS & Netlist Labs
      </td>
      <td align="center">
        <a href="#4️⃣-blocking-vs-non-blocking-labs">
          <img src="https://img.shields.io/badge/4-Blocking%20Statements-red?style=for-the-badge&logo=code" />
        </a>
        <br>Blocking vs Non-Blocking
      </td>
      <td align="center">
        <a href="#5️⃣-key-takeaways">
          <img src="https://img.shields.io/badge/5-Summary%20Insights-yellow?style=for-the-badge&logo=markdown" />
        </a>
        <br>Key Takeaways
      </td>
    </tr>
  </table>

  <p style="margin-top:15px; font-size:16px;">
    🎯 <b>Focus Areas:</b> Understand <b>Gate-Level Simulation</b>,  
    explore <b>synthesis vs simulation mismatches</b>,  
    run <b>GLS labs with netlists</b>, and learn the impact of  
    <b>blocking vs non-blocking assignments</b> in Verilog.
  </p>
</div>

---

## 🔹 Table of Contents

1. [GLS Concept and Flow Using Icarus Verilog](#gls-concept-and-flow-using-icarus-verilog)  
   - Why GLS?  
   - GLS with Verilog  

2. [Synthesis vs Simulation Mismatch](#synthesis-vs-simulation-mismatch)  
   - Missing Sensitivity List  
   - Blocking vs Non-Blocking Assignments  
   - Non-Standard Verilog Coding  

3. [Labs on GLS and Synthesis Simulation Mismatch](#labs-on-gls-and-synthesis-simulation-mismatch)  
   - `ternary_operator_mux.v`  
   - `bad_mux.v`  

4. [Labs on Synth-Sim Mismatch for Blocking Statement](#labs-on-synth-sim-mismatch-for-blocking-statement)  
   - `blocking_caveat.v`  
   - Corrected Non-Blocking Example  


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


# 3. 🧪 Labs on GLS and Synthesis Simulation Mismatch

In this lab, we explore **Gate-Level Simulation (GLS)** and the issues of **synthesis vs simulation mismatch** using a simple **2:1 MUX** design written with a ternary operator.

---

## 📌 Example: `ternary_operator_mux.v`

```verilog
// 2:1 MUX using ternary operator
module ternary_operator_mux (
  input a, b, sel,
  output y
);
  assign y = sel ? b : a;
endmodule
```

### 🖼️ Code Visualization:

<p align="center"> <img src="Images/ctom.png?raw=true" alt="ternary_operator_mux Verilog code" width="600"/> </p>

### ▶️ RTL Simulation with Icarus Verilog + GTKWave

```bash 
# Compile design and testbench
iverilog ternary_operator_mux.v tb_ternary_operator_mux.v

# Run simulation
./a.out

# View waveforms
gtkwave tb_ternary_operator_mux.vcd
```
### 🖼️ RTL Simulation Output:

<p align="center"> <img src="Images/wtom.png?raw=true" alt="RTL Simulation output in GTKWave" width="600"/> </p>


### 🖥️ Synthesis and Netlist Generation with Yosys

```bash
# 1. Start Yosys
yosys
```
```bash
# 2. Read Liberty timing file
read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
```
```bash
# 3. Read Verilog design files
read_verilog ternary_operator_mux.v
```
```bash
# 4. Synthesize the design 
synth -top ternary_operator_mux
```
```bash
# 5. Technology mapping
abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
```
```bash
# 6. Write the synthesized netlist
write_verilog -noattr ternary_operator_mux_net.v
```
```bash
# 7. Visualize the netlist
show
```
### 🖼️ Synthesized Netlist Visualization (Yosys):

<p align="center"> <img src="Images/stom.png?raw=true" alt="Yosys netlist of ternary_operator_mux" width="600"/> </p>

### ⚡ Gate-Level Simulation (GLS)

Now, simulate the **gate-level** netlist to verify functionality matches RTL.

```bash 
# Compile GLS with standard cell models + netlist + testbench
iverilog ../my_lib/verilog_model/primitives.v \
         ../my_lib/verilog_model/sky130_fd_sc_hd.v \
         ternary_operator_mux_net.v tb_ternary_operator_mux.v
```
```bash
# Run simulation
./a.out
```
```bash

# View waveforms
gtkwave tb_ternary_operator_mux.vcd
```

## 🖼️ GLS Output in GTKWave

<p align="center">
  <img src="Images/gtom.png?raw=true" 
       alt="GLS Simulation Output in GTKWave" 
       width="800"/>
</p>


## 📌 Example: `bad_mux.v`

```verilog
// 2:1 MUX with incomplete sensitivity list
module bad_mux (
  input a, b, sel,
  output reg y
);
  always @(sel) begin
    if (sel)
      y = b;
    else
      y = a;
  end
endmodule
```

## 🖼️ Code Visualization:
<p align="center"> <img src="Images/cbm.png?raw=true" alt="bad_mux Verilog code" width="600"/> </p>

### ▶️ RTL Simulation with Icarus Verilog + GTKWave

```bash 
# Compile design and testbench
iverilog bad_mux.v tb_bad_mux.v

# Run simulation
./a.out

# View waveforms
gtkwave tb_bad_mux.vcd
```
## 🖼️ RTL Simulation Output:
<p align="center"> <img src="Images/wbm.png?raw=true" alt="RTL Simulation Output in GTKWave" width="600"/> </p>

## Explanation:

-Here, the always @(sel) sensitivity list only triggers when sel changes.

-If a or b changes while sel remains the same, y does **not update correctly.**

-✅ This demonstrates a **simulation mismatch** due to an incomplete sensitivity list.


### 🖥️ Synthesis and Netlist Generation with Yosys

```bash
# 1. Start Yosys
yosys
```
```bash
# 2. Read Liberty timing file
read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
```
```bash
# 3. Read Verilog design files
read_verilog bad_mux.v
```
```bash
# 4. Synthesize the design
synth -top bad_mux
```
```bash
# 5. Technology mapping
abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
```
```bash
# 6. Write the synthesized netlist
write_verilog -noattr bad_mux_net.v
```
```bash
# 7. Visualize the netlist
show
```
### 🖼️ Synthesized Netlist Visualization (Yosys):
<p align="center"> <img src="Images/sbm.png?raw=true" alt="Yosys netlist of bad_mux" width="600"/> </p>

## ⚡ Gate-Level Simulation (GLS)

Now, simulate the **gate-level** netlist to verify functionality.

### ⚡ Gate-Level Simulation (GLS)

Now, simulate the **gate-level** netlist to verify functionality matches RTL.

```bash 
# Compile GLS with standard cell models + netlist + testbench
iverilog ../my_lib/verilog_model/primitives.v \
         ../my_lib/verilog_model/sky130_fd_sc_hd.v \
         bad_mux_net.v tb_bad_mux.v
```
```bash
# Run simulation
./a.out
```

```bash
# View waveforms
gtkwave tb_bad_mux.vcd
```

## 🖼️ GLS Output in GTKWave (Initial)
<p align="center"> <img src="Images/gbm.png?raw=true" alt="GLS Simulation Output - Initial" width="800"/> </p>

## Explanation:

-The output y does **not reflect changes** in a or b when sel remains constant.

-✅ This mismatch occurs because the sensitivity list only includes sel, missing the **combinational dependencies.**


# 4. Labs on Synth-Sim Mismatch for Blocking Statement

In this lab, we explore **blocking assignments (`=`) in sequential logic** and how they can lead to **simulation vs synthesis mismatches**.  
We will see an example where **`d = x & c; x = a | b;`** causes incorrect simulation behavior.

---

## 📌 Example: `blocking_caveat.v`

```verilog
// Example demonstrating blocking statement caveat
module blocking_caveat (
  input clk,
  input a, b, c,
  output reg d
);
  reg x;
  always @(posedge clk) begin
    d = x & c;   // uses "old" value of x during simulation
    x = a | b;   // updates x afterwards
  end
endmodule
```
## 🖼️ Code Visualization:
<p align="center"> <img src="cbc.png?raw=true" alt="blocking_caveat Verilog code" width="600"/> </p>

## ▶️ RTL Simulation with Icarus Verilog + GTKWave

```bash
# Compile design and testbench
iverilog blocking_caveat.v tb_blocking_caveat.v

# Run simulation
./a.out

# View waveforms
gtkwave tb_blocking_caveat.vcd
```

## 🖼️ RTL Simulation Output:
<p align="center"> <img src="wbc.png?raw=true" alt="RTL Simulation Output in GTKWave" width="600"/> </p>

## Explanation of RTL Simulation Output:

-The output d uses the **old value of x**, because blocking assignments execute sequentially in the always block.

-✅ This demonstrates a **simulation-synthesis mismatch**, as in hardware, d and x would update **in parallel**, not sequentially.


### 🖥️ Synthesis and Netlist Generation with Yosys

```bash
# 1. Start Yosys
yosys
```
```bash
# 2. Read Liberty timing file
read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
```
```bash
# 3. Read Verilog design files
read_verilog blocking_caveat.v
```
```bash
# 4. Synthesize the design 
synth -top blocking_caveat
```
```bash
# 5. Technology mapping
abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
```
```bash
# 6. Write the synthesized netlist
write_verilog -noattr blocking_caveat_net.v
```
```bash
# 7. Visualize the netlist
show
```

## 🖼️ Synthesized Netlist Visualization (Yosys):
<p align="center"> <img src="sbc.png?raw=true" alt="Yosys netlist of blocking_caveat" width="600"/> </p>


## ⚡ Gate-Level Simulation (GLS)

Simulate the **gate-level** netlist to verify the design.
```bash 
 # Compile GLS with standard cell models + netlist + testbench
iverilog ../my_lib/verilog_model/primitives.v \
         ../my_lib/verilog_model/sky130_fd_sc_hd.v \
         blocking_caveat_net.v tb_blocking_caveat.v
```
```bash
# Run simulation
./a.out
```

```bash
# View waveforms
gtkwave tb_blocking_caveat.vcd
```

## 🖼️ GLS Output in GTKWave
<p align="center"> <img src="gbc.png?raw=true" alt="Yosys netlist of blocking_caveat" width="600"/> </p>


## Explanation of GLS Output:

-After gate-level synthesis, the hardware behavior is **correct**, as the netlist evaluates d and x in parallel.

-✅ The output now matches **expected hardware behavior**, resolving the mismatch seen in RTL simulation.

-  This demonstrates the importance of using **non-blocking assignments (<=) in sequential logic** to match synthesis behavior.
