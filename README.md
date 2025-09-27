<div align="center">
  <h1>🌟 Week 1 — RTL Design & Optimization</h1>
  <p>
    Welcome to <b>Week 1</b> of our journey into <b>RTL design and optimization</b>.  
    This week focuses on building a strong foundation in <b>digital circuits</b>, 
    <b>combinational and sequential design</b>, and <b>optimization techniques</b>.  
    Hands-on labs with Verilog and Yosys help learners understand synthesis, simulation, 
    and circuit optimization in real scenarios.
  </p>

  <img src="https://img.shields.io/badge/Week1-Overview-blueviolet?style=for-the-badge" />
  <img src="https://img.shields.io/badge/Tools-Yosys-orange?style=for-the-badge" />
  <img src="https://img.shields.io/badge/Language-Verilog-red?style=for-the-badge" />
  <img src="https://img.shields.io/badge/Simulation-GTKWave-green?style=for-the-badge" />
</div>

---

## 📑 Table of Contents

| Day | Topic | Subtopics | Description |
|-----|-------|-----------|-------------|
| [Day 1](Day-1/README.md) | Introduction to RTL & Digital Design | Combinational vs Sequential, RTL basics, Clocks & Resets, Verilog coding | Learned fundamental concepts of digital design, RTL coding styles, and simulation setup. |
| [Day 2](Day-2/README.md) | Combinational Logic & Timing Analysis | Gates, Logic Levels, Propagation Delay, Timing Analysis, Lab: Basic Combinational Circuits | Explored gate-level logic, timing delays, and performing functional and timing simulations using Yosys & GTKWave. |
| [Day 3](Day-3/README.md) | Introduction to Optimization | Constant Propagation, Boolean Simplification, Sequential Constant Propagation, Flip-Flop Optimization | Learned combinational and sequential optimization techniques, reducing gate/transistor count, and improving area & power. |
| [Day 4](Day-4/README.md) | GLS, Blocking vs Non-Blocking, Synth-Sim Mismatch | Gate-Level Simulation (GLS), Blocking vs non-blocking Assignments, Synth-Sim Differences, Lab: DFF & Counter Simulations | Understood timing behavior in synthesized circuits, differences between RTL and GLS, and how to write proper Verilog for synthesis. |
| [Day 5](Day-5/README.md) | Advanced Optimization Techniques | State Optimization, Cloning, Retiming, Counter Optimizations, Lab: Sequential Design Optimization | Explored advanced sequential optimizations, improving critical paths, reducing flip-flops, and learning practical RTL design improvements. |

---

## 🛠️ Tools & Software Learned

- **Yosys:** RTL synthesis, optimization, netlist generation, gate-level visualization.  
- **GTKWave:** Waveform viewer for simulation and verification of Verilog designs.  
- **Verilog:** RTL coding for combinational and sequential logic, including lab modules.  
- **SkyWater PDK (Optional):** Standard cell library for gate-level synthesis and transistor-level analysis.  
- **Gvim/VSCode:** Code editor for Verilog development and visualization.  

---

## ✅ Key Concepts Covered

### 🔹 Digital Design Basics
- Difference between **combinational** and **sequential circuits**  
- **Flip-flops, latches, and registers**  
- **Clocking, resets, and timing**  

### 🔹 Combinational Logic Design
- Boolean expressions, **Karnaugh Maps**, and truth tables  
- **Logic simplification**: AND-OR-NOT, NAND/NOR implementations  
- Understanding **propagation delay** and critical path  

### 🔹 Sequential Logic Design
- DFF, TFF, JKFF, and counters  
- Synchronous vs asynchronous resets  
- **State machines** and **state transitions**  

### 🔹 RTL & Optimization
- **Constant propagation** to reduce logic  
- **Boolean logic simplification** to reduce gate/transistor count  
- **Sequential constant propagation** to optimize flip-flops  
- Removing **unused outputs** to reduce area & power  
- Advanced: **state minimization, cloning, retiming** for performance improvement  

### 🔹 Simulation & Synthesis
- Writing testbenches for functional verification  
- Performing **RTL simulation** with GTKWave  
- Synthesizing RTL to **gate-level netlist** using Yosys  
- Comparing **RTL vs gate-level behavior**  

---

## 🧩 Labs and Hands-On Exercises

- **Day 1:** Basic combinational circuits and Verilog coding  
- **Day 2:** Propagation delay simulation and verification  
- **Day 3:** Constant propagation and Boolean simplification labs (`opt_check`, `dff_const`)  
- **Day 4:** GLS, blocking vs non-blocking assignment examples, counter simulations  
- **Day 5:** Sequential optimization labs (`counter_opt`, `counter_opt2`), state reduction, retiming  

---

## 📚 References  

1. [Icarus Verilog](http://iverilog.icarus.com/) – Simulation.  
2. [GTKWave](http://gtkwave.sourceforge.net/) – Waveform visualization.  
3. [Yosys HQ](https://yosyshq.net/yosys/) – Synthesis & optimization.  
4. [SkyWater Sky130 PDK](https://skywater-pdk.readthedocs.io/) – Cell libraries.  
5. Sudip Misra NPTEL Course – *Industry 4.0 and IIoT (Verilog Synthesis Part)*.

---

## Acknowledgments  

I would like to sincerely acknowledge and thank:  

- **Mr. Kunal Ghosh (Founder, VSD – VLSI System Design)**  
  For his exceptional mentorship, guidance, and vision in enabling students to learn *RTL design, synthesis, and open-source VLSI flows*.  

- **VSD Team**  
  For curating the workshop content and providing continuous learning resources.  

- **Open-Source EDA Community**  
  For making powerful tools freely available, including:  
  - 🖥️ [Icarus Verilog](http://iverilog.icarus.com/)  
  - 📊 [GTKWave](http://gtkwave.sourceforge.net/)  
  - ⚙️ [Yosys](https://yosyshq.net/yosys/)  

- **Faculty & Peers from the VLSI Department**  
  For their constant encouragement, support, and collaboration during this learning journey.  

---

✨ This repository is dedicated to the spirit of **open-source hardware education** and the belief that *accessible knowledge can empower innovation in VLSI design*.  

---

<div align="center">
  <p>Week 1 Complete: Days 1 to 5 – Strong Foundation in RTL Design, Simulation, and Optimization</p>
</div>
