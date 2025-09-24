<h2 align="center">🚀 Week 1 – Day 2</h2>
<h3 align="center">Timing Libraries | Hierarchical vs Flat Synthesis | Flop Coding Styles</h3>

---

# ⏱️ 1. Timing Libraries (.lib) – Introduction

In digital design, a **timing library (.lib)** is the *blueprint* of standard cells used during synthesis.  
It captures how each logic gate behaves in terms of **function, delay, power, and constraints**.  
Without `.lib`, synthesis tools like **Yosys** cannot map RTL code to real technology cells.  

---

## 🔑 Why `.lib` Files are Important?
- Define **functional behavior** of cells (NAND, NOR, DFF, etc.)  
- Contain **timing arcs**: input → output delays, setup & hold times  
- Describe **power data** (leakage, dynamic switching power)  
- Specify **conditions**: process, voltage, temperature (PVT)  

---

## 🧩 Example of a `.lib` Filename

`sky130_fd_sc_hd__ss_100C_1v60.lib`

| Part | Meaning |
|------|---------|
| `sky130_fd_sc_hd` | Skywater 130nm, high-density cell library |
| `ss` | Slow-Slow process corner |
| `100C` | Characterized at 100 °C |
| `1v60` | Operating at 1.60 V |

💡 A single project may use multiple `.lib` files for different **corners** (worst case, best case, typical).

---

## 📂 Typical Information Inside a `.lib`

```liberty
cell (NAND2_X1) {
  area : 1.24;
  pin(A1) {
    direction : input;
    capacitance : 0.0021;
  }
  pin(ZN) {
    direction : output;
    function : "!(A1 & A2)";
    timing() {
      related_pin : "A1";
      rise_constraint(...) { ... }
      fall_constraint(...) { ... }
    }
  }
}

