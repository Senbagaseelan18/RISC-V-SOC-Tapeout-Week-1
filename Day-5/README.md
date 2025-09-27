<div align="center">
  <h1>🚀 Day 5 — Control Constructs & Looping in Verilog</h1>
  <p>
    In <b>Day 5</b>, we dive into <b>decision-making constructs</b>  
    (`if-else`, `case`) and <b>looping mechanisms</b> (`for`, `generate`).  
    We also study how incomplete constructs lead to <b>latch inference</b>  
    and how loops enable <b>scalable hardware design</b>.
  </p>

  <img src="https://img.shields.io/badge/Week1-Day5-darkgreen?style=for-the-badge" />
  <img src="https://img.shields.io/badge/Verilog-Control_Structures-blue?style=for-the-badge" />
  <img src="https://img.shields.io/badge/Loops-Generate_and_Procedural-orange?style=for-the-badge" />
  <img src="https://img.shields.io/badge/Synthesis-Optimization-yellow?style=for-the-badge" />
</div>

---

## 📑 Table of Contents  

-# 📘 Table of Contents — Day 5

| 🔢 # | 📂 Topic | 🔗 Link |
|------|----------|---------|
| 1 | **Decision-Making Constructs** | [Go to Section](#1-decision-making-constructs) |
| 1.1 | ➡️ `if-else` Statements | [Go to Section](#11-if-else-statements) |
| 1.2 | ➡️ `case` Statements | [Go to Section](#12-case-statements) |
| 1.3 | ⚠️ Incomplete Constructs → Latch Inference | [Go to Section](#13-incomplete-constructs--latch-inference) |
| 2 | **Looping Constructs** | [Go to Section](#2-looping-constructs) |
| 2.1 | ➡️ `for` Loops (Behavioral Iteration) | [Go to Section](#21-for-loops-behavioral-iteration) |
| 2.2 | ➡️ `generate` Loops (Structural Replication) | [Go to Section](#22-generate-loops-structural-replication) |
| 3 | **Synthesis Considerations** | [Go to Section](#3-synthesis-considerations) |
| 3.1 | ✅ Loop Unrolling in Hardware | [Go to Section](#31-loop-unrolling-in-hardware) |
| 3.2 | ⚡ Optimization & RTL Scalability | [Go to Section](#32-optimization--rtl-scalability) |
| 4 | **Labs & Hands-On** | [Go to Section](#4-labs--hands-on) |
| 4.1 | 🧪 Lab: `if-else` & `case` Decision Testing | [Go to Section](#41-lab-if-else--case-decision-testing) |
| 4.2 | 🧪 Lab: Loop-Based Counter Design | [Go to Section](#42-lab-loop-based-counter-design) |
| 4.3 | 🧪 Lab: Generate-Based Multiplexer Array | [Go to Section](#43-lab-generate-based-multiplexer-array) |

---

<div align="center">
  <h1>🔀 Conditional Constructs in Verilog</h1>
</div>

Conditional logic is at the heart of hardware design in Verilog. Designers use **`if` and `case` statements** to express **decision-making behavior**. These constructs are directly synthesized into hardware, so **how you write them impacts the resulting circuit**.  

⚡ **Key Points:**
- Conditional constructs are used in **combinational** and **sequential** logic.  
- Incorrect or incomplete usage can lead to **inferred latches**, **unexpected hardware**, or **inefficient circuits**.  
- Always understand **synthesis implications** when writing conditionals.

---

## 🔹 1) The `if` Statement in Verilog

The `if` statement is a primary construct for **decision-making** and **priority-based logic**.

### A) Priority Logic Implementation
- An `if-else if-else` chain synthesizes into **priority logic**.  
- Hardware equivalent: **chain of multiplexers**, where the first true condition determines the output.

```verilog
begin
    if (<cond_1>)
        // Action 1
    else if (<cond_2>)
        // Action 2
    else if (<cond_3>)
        // Action 3
    else
        // Default Action
end
```
<p align="center"> <img src="Images/priority.png?raw=true" alt="If_else_hardware" width="700"/> </p>

## Execution Flow:

- <cond_1> → highest priority.

- If false, <cond_2> is checked, then <cond_3>, and finally the else block.

### B) Incomplete if Statements – Danger in Combinational Logic

-  Missing a final else can cause **inferred latches.**

-  **Reason**: The synthesis tool must “hold” the previous value when no condition is true.

-  ❌ Bad Practice: Using incomplete if in combinational blocks.

<p align="center"> <img src="Images/danger_with_if.png?raw=true" alt="Danger with if" width="700"/> </p> <p align="center"> <img src="Images/Infered_latch.png?raw=true" alt="Inferred latch" width="700"/> </p>


## C) Valid Usage in Sequential Logic

In **sequential circuits**, incomplete if statements are **intentional**, e.g., counters
```verilog
always @(posedge clk or posedge rst) begin
    if (rst)
        count <= 0;
    else if (en)
        count <= count + 1;
    // No final else: holds previous value
end
```
<p align="center"> <img src="Images/Valid_infered_latch.png?raw=true" alt="Valid inferred latch" width="700"/> </p>

## 🔹 2) The case Statement in Verilog

The `case` statement provides a **structured way** to select between multiple options based on a **selector signal**.

#### A) Basic Implementation
- Synthesizes into a **multiplexer**, driven by the selector.  
- Easier to read and maintain than multiple `if-else` statements.

```verilog
module mux4x1_case (
    input  [1:0] sel,    // 2-bit selector
    input  C1, C2, C3, C4, // 4 data inputs
    output reg y         // output
);

always @(*) begin
    case (sel)
        2'b00: y = C1;
        2'b01: y = C2;
        2'b10: y = C3;
        2'b11: y = C4;
        default: y = 0;   // safe default to avoid latch inference
    endcase
end

endmodule
```
<p align="center"> <img src="Images/mux_case.png?raw=true" alt="Mux_Case" width="700"/> </p>

### B) Common Pitfalls & Solutions for `case`

#### 1) Incomplete Case
- ❌ Missing cases cause **latches to be inferred**.
- ✅ Fix: Always include a `default` branch.

```verilog
case (sel)
    2'b00: y = C1;
    2'b01: y = C2;
    default: y = 0;  // prevents latch inference
endcase
```
<p align="center"> <img src="Images/latched_mux.png?raw=true" alt="latched_mux" width="700"/> </p> 

### C) Overlapping Cases

- Unlike `if-else`, `case` statements **do not have priority**.  
- ❌ Overlapping selectors lead to ambiguity.

```verilog
always @(*) begin
    case (sel)
        2'b00: y = a;
        2'b01: y = b;
        2'b10: y = c;
        2'b10: y = d;  // ❌ Overlapping selector
        default: y = 0;
    endcase
end
```

**Summary:**

- ✅ `if-else` → **priority logic**
- ✅ `case` → **multiplexer selection**
- ⚠️ Always handle **incomplete and overlapping cases** to prevent unintended hardware

<p align="center">
  <img src="Images/over.png" />
</p>

## 3) 🧪 Labs on Incomplete If-Case  

In this lab, we explore **how incomplete `if` statements** can lead to **inferred latches** in combinational logic.  
We'll simulate the designs using **Icarus Verilog + GTKWave** and observe synthesized hardware using **Yosys**.

---

### ⛏️ Commands Overview  

- **Simulate RTL:** `iverilog <module.v> <testbench.v>` → `./a.out`  
- **View Waveform:** `gtkwave <wavefile.vcd>`  
- **Synthesize & Analyze:** `yosys` commands (`read_verilog`, `synth`, `abc`, `show`)  

---

### 🔹 A — `incomp_if` (Incomplete `if` → inferred latch)

#### Design Code
```verilog
module incomp_if (
    input i0, input i1, input i2,
    output reg y
);
always @(*) begin
    if(i0)
        y <= i1;
end
endmodule
```
## Simulation with Icarus Verilog

```bash
# Compile and run RTL + testbench
iverilog incomp_if.v tb_incomp_if.v
./a.out
gtkwave tb_incomp_if.vcd
```
<p align="center"> <img src="Images/gtk_ncomp_if.png?raw=true" alt="GTKWave - incom_if" width="700"/> </p>

### Observation:

-  Since there is no final else, a latch is inferred to hold the previous value when i0 = 0.

-  This is undesirable in combinational logic.

## Synthesis with Yosys

```yosys
read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog incomp_if.v
synth -top incomp_if
abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```
<p align="center"> <img src="Images/dlatch_infered.png?raw=true" alt="Dlatch inferred" width="700"/> </p> <p align="center"> <img src="Images/incomp_if_netlist.png?raw=true" alt="incomp_if netlist" width="700"/> </p>

✅ Observation: The synthesized netlist clearly shows an inferred D-latch, caused by incomplete conditional logic.


## 🔹 B — incomp_if2 (Incomplete if-else if → inferred latch)

## Design Code
```verilog
module incomp_if2 (
    input i0, i1, i2, i3,
    output reg y
);
always @(*) begin
    if(i0)
        y <= i1;
    else if (i2)
        y <= i3;
    // No final else → inferred latch
end
endmodule
```

## Simulation with Icarus Verilog
```bash
iverilog incomp_if2.v tb_incomp_if2.v
./a.out
gtkwave tb_incomp_if2.vcd
```
<p align="center"> <img src="Images/gtk_incomp_if2.png?raw=true" alt="GTKWave - incom_if2" width="700"/> </p>

## Synthesis with Yosys
```yosys
read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog incomp_if2.v
synth -top incomp_if2
abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```
<p align="center"> <img src="Images/incomp_if2_netlist.png?raw=true" alt="incomp_if2 netlist" width="700"/>

  ⚡ Observation: Even with multiple conditions (if-else if), the absence of a final else causes synthesis to infer a latch, highlighting a common pitfall in combinational logic design.

<p align="center"> <img src="Images/Overlappinge" /> </p> 


## 3️⃣ Looping Constructs in Verilog  

### 🔹 Procedural For Loop  
- Written **inside always block**.  
- Used for **behavioral iteration**.  

### 🔹 Generate For Loop  
- Written **outside always block**.  
- Used for **replicating hardware structures**.  

| Feature | Procedural For | Generate For |
|---------|----------------|--------------|
| Location | Inside `always` | Outside `always` |
| Usage | Behavioral logic | Structural replication |
| Example | Testbench counters | Array of adders |

---

## 4️⃣ Practical Experiments  

✅ **MUX with procedural for**  
✅ **MUX with generate for**  
✅ **DEMUX: case vs generate**  
✅ **Ripple Carry Adder using generate loop**  

---

## 5️⃣ Key Takeaways  

- `if-else` → **priority-driven logic**  
- `case` → **parallel multiplexer logic**  
- Missing `else/default` → **unintended latches**  
- Procedural `for` → **behavioral modeling**  
- Generate `for` → **hardware replication**  
- Loops help in **scalable & reusable hardware design**  

---

