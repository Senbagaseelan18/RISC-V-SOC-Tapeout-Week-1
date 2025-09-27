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

## B) Incomplete if Statements – Danger in Combinational Logic

-Missing a final else can cause **inferred latches.**

-**Reason**: The synthesis tool must “hold” the previous value when no condition is true.

-❌ Bad Practice: Using incomplete if in combinational blocks.

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


## 2️⃣ Case Studies on Incomplete Constructs  

- **Incomplete If:** Missing `else` → causes **latch inference**.  
- **Incomplete Case:** Missing `default` → causes **latch inference**.  
- **Overlapping Case:** Multiple matches → **ambiguous synthesis result**.  

---

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

