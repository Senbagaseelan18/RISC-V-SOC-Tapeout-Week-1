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
<p align="center"> <img src="Images/gtk_ncomp_if.png?raw=true" alt="GTKWave - incom_if" width="1000"/> </p>

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
</p> <p align="center"> <img src="Images/incomp_if_netlist.png?raw=true" alt="incomp_if netlist" width="700"/> </p>

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
<p align="center"> <img src="Images/gtk_incomp_if2.png?raw=true" alt="GTKWave - incom_if2" width="1000"/> </p>

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


## 4) 🧪 Labs on Incomplete & Overlapping Case Statements  

In this lab, we explore how **incomplete, overlapping, or partially assigned case statements** can affect **hardware synthesis** in Verilog.  
We'll simulate the designs using **Icarus Verilog + GTKWave** and synthesize using **Yosys** to observe inferred latches and hardware mapping.

---

### 🔹 A — `incomp_case` [Incomplete Case]

#### Design Code
```bash
module incomp_case (input i0 , input i1 , input i2 , input [1:0] sel, output reg y);
always @ (*)
begin
	case(sel)
		2'b00 : y = i0;
		2'b01 : y = i1;
	endcase
end
endmodule
```

## RTL Simulation with Icarus Verilog
```bash
# Compile RTL + testbench
iverilog incomp_case.v tb_incomp_case.v
./a.out
gtkwave tb_incom_case.vcd
```
<p align="center"> <img src="Images/gtk_incomp_case.png?raw=true" alt="gtk_incomp_case" width="700"/> </p>

## Yosys Synthesis
```yosys
read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog incomp_case.v
synth -top incomp_case
abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show 
```
<p align="center"> <img src="Images/incomp_case_netlist.png?raw=true" alt="incomp_case_netlist" width="700"/> </p>

⚠️ Observation: **Incomplete case statements** can lead to **inferred latches**, as unhandled selector values are stored in memory.

## 🔹 B — comp_case [Complete Case]

## Design Code
  ```verilog
  module comp_case (input i0 , input i1 , input i2 , input [1:0] sel, output reg y);
always @ (*)
begin
	case(sel)
		2'b00 : y = i0;
		2'b01 : y = i1;
		default : y = i2;
	endcase
end
endmodule
```
## RTL Simulation with Icarus Verilog
```bash
iverilog comp_case.v tb_comp_case.v
./a.out
gtkwave tb_comp_case.vcd
```
<p align="center"> <img src="Images/gtk_comp_case.png?raw=true" alt="gtk_comp_case" width="700"/> </p>

## Yosys Synthesis
```yosys
read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog comp_case.v
synth -top comp_case
abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show 
```
<p align="center"> <img src="Images/comp_case_netlist.png?raw=true" alt="comp_case_netlist" width="700"/> </p>

✅ Observation: Adding a default branch handles all remaining selector values and prevents latch inference.

## 🔹 C — bad_case [Ambiguous / Overlapping Case]
## Design Code
```verilog
module bad_case (input i0 , input i1, input i2, input i3 , input [1:0] sel, output reg y);
always @(*)
begin
	case(sel)
		2'b00: y = i0;
		2'b01: y = i1;
		2'b10: y = i2;
		2'b1?: y = i3;
	endcase
end

endmodule
```
## RTL Simulation with Icarus Verilog
```bash
iverilog bad_case.v tb_bad_case.v
./a.out
gtkwave tb_bad_case.vcd
```
<p align="center"> <img src="Images/gtk_bad_case.png?raw=true" alt="gtk_bad_case" width="700"/> </p>

## Yosys Synthesis
```yosys
read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog bad_case.v
synth -top bad_case
abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show 
```
<p align="center"> <img src="Images/bad_case_netlist.png?raw=true" alt="bad_case_netlist" width="700"/> </p>

⚠️ Observation: Overlapping or wildcard cases can create ambiguous hardware logic, leading to unpredictable behavior.

## 🔹 D — partial_case_assign [Partial Case Assignment]
## Design Code
```verilog
module partial_case_assign (input i0 , input i1 , input i2 , input [1:0] sel, output reg y , output reg x);
always @ (*)
begin
	case(sel)
		2'b00 : begin
			y = i0;
			x = i2;
			end
		2'b01 : y = i1;
		default : begin
		           x = i1;
			   y = i2;
			  end
	endcase
end
endmodule
```

## Yosys Synthesis
```yosys
read_liberty -lib ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog partial_case_assign.v
synth -top partial_case_assign
abc -liberty ../my_lib/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show 
```
<p align="center"> <img src="Images/partial_case_assign_netlist.png?raw=true" alt="partial_case_assign" width="700"/> </p>

⚠️ Observation: Partial assignments can also infer latches. Always assign all outputs in every case branch to ensure deterministic behavior.


## 5) 🔁 Loops in Verilog: Procedural & Generate  

Loops are essential in **hardware design** for describing both **behavioral logic** and **structural replication**.  
Verilog supports two primary looping constructs:

---

### 1️⃣ Procedural `for` Loop (Behavioral)

**Key Points:**  
- Used **inside `always` blocks**.  
- Describes **behavioral logic**, not direct hardware replication.  
- Ideal for **iterative assignments**, **MUX/DEMUX logic**, or **bit-wise operations**.

#### 🔹 Example: 32×1 Multiplexer using Procedural `for`
```verilog
module mux32x1 (
    input  wire [31:0] inp,
    input  wire [4:0]  sel,
    output reg         Y
);
integer i;
always @(*) begin
    Y = 0;
    for (i = 0; i < 32; i = i + 1) begin
        if (sel == i)
            Y = inp[i];
    end
end
endmodule
```
💡 Note: Here, the for loop **evaluates conditions sequentially** at runtime, and synthesizes into a priority multiplexer chain.

### 2️⃣ generate for Loop (Structural / Hardware Replication)

**Key Points:**

-  Used outside always blocks.

-  Generates multiple hardware instances in a structured way.

-  Useful for arrays of gates, adders, or replicated modules.

## 🔹 Example 1: Array of AND Gates
```verilog
module and_array #(parameter N=4)(
    input  wire [N-1:0] A, B,
    output wire [N-1:0] Y
);
genvar i;
generate
    for (i = 0; i < N; i = i + 1) begin : AND_LOOP
        and g (Y[i], A[i], B[i]);
    end
endgenerate
endmodule
```
## 🔹 Example 2: N-bit Ripple Carry Adder
``` verilog
module rca #(parameter N=4)(
    input  wire [N-1:0] A, B,
    input  wire Cin,
    output wire [N-1:0] Sum,
    output wire Cout
);
wire [N:0] c;
assign c[0] = Cin;
assign Cout = c[N];

genvar i;
generate
    for (i = 0; i < N; i = i + 1) begin : RCA_LOOP
        full_adder FA (
            .a(A[i]), .b(B[i]), .cin(c[i]),
            .sum(Sum[i]), .cout(c[i+1])
        );
    end
endgenerate
endmodule
```
💡 Note: generate for **creates multiple instances at compile time**, producing replicated hardware blocks efficiently.

### 📊 Procedural `for` vs `generate for` Loops in Verilog

| **Feature**               | **Procedural `for` Loop**              | **`generate for` Loop**                  |
|----------------------------|----------------------------------------|-----------------------------------------|
| **Placement**              | Inside an `always` block               | Outside an `always` block               |
| **Purpose**                | Evaluate expressions / describe logic behavior | Instantiate / replicate hardware blocks |
| **Synthesis Concept**      | Behavioral logic (e.g., priority MUX) | Structural replication of modules or gates |
| **Execution / Runtime**    | Sequential evaluation during simulation | Compile-time hardware generation        |
| **Use Case**               | Iterative assignments, MUX selection   | Arrays of gates, adders, repeated modules |




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

