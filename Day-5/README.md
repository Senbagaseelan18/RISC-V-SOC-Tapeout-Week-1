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

- [1️⃣ Conditional Constructs in Verilog](#1️⃣-conditional-constructs-in-verilog)  
- [2️⃣ Case Studies on Incomplete Constructs](#2️⃣-case-studies-on-incomplete-constructs)  
- [3️⃣ Looping Constructs in Verilog](#3️⃣-looping-constructs-in-verilog)  
- [4️⃣ Practical Experiments](#4️⃣-practical-experiments)  
- [5️⃣ Key Takeaways](#5️⃣-key-takeaways)  

---

## 1️⃣ Conditional Constructs in Verilog  

### 🔹 If-Else (Priority Logic)  
- Synthesized as **chained multiplexers**.  
- Higher priority for the first true condition.  

### 🔹 Case (Parallel Multiplexer)  
- Synthesized as **clean multiplexer logic**.  
- Easier to read than long if-else chains.  

---

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

