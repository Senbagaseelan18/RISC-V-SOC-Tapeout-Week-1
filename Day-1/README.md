# 🛠️ Day 1 – Introduction to Verilog RTL Design & Synthesis

Welcome to **Day 1** of the RTL Workshop!  
Today, you'll start your journey into digital design by learning Verilog, open-source simulation with **Icarus Verilog (iverilog)**, and waveform analysis with **GTKWave**. This guide walks you through practical labs, essential concepts, and hands-on exercises.

---

## 📘 Table of Contents

1. [Getting Started with Verilog Simulation](#1-getting-started-with-verilog-simulation)  
2. [Lab: Bad Counter](#2-lab-bad-counter)  

---

## 1. Getting Started with Verilog Simulation

**iverilog** is an open-source Verilog simulator that compiles and runs your RTL designs.  
A **testbench** is used to apply inputs and verify your design outputs.  

<div align="center">
  <img src="Images/Simulator_working.png" alt="Simulator Overview" width="70%">
  <p><i>Simulator workflow overview</i></p>
</div>

### Setting Up Your Design

- Both the **design** and **testbench** files are compiled together.  
- The simulator generates a `.vcd` file for waveform viewing in GTKWave.

<div align="center">
  <img src="Images/iverilog_lib_setup.png" alt="Iverilog setup" width="70%">
  <p><i>Compiling design and testbench in iverilog</i></p>
</div>

---

## 2. Lab: Bad Counter

In this lab, you will design and simulate a **bad counter** to understand sequential logic and register behavior.

### Step 1: Clone the Repository

```bash
git clone https://github.com/kunalg123/sky130RTLDesignAndSynthesisWorkshop.git
cd sky130RTLDesignAndSynthesisWorkshop/verilog_files
