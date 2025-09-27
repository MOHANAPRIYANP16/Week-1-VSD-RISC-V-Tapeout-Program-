
# *Week 1* – VSD RISC-V Tapeout Program ![Simulation & Optimisation](https://img.shields.io/badge/Simulation_%26_Optimisation-done-darkgreen)

📅 `20/09/2025` – `27/09/2025`  

This week covers **RTL design, simulation, and optimization** using **Yosys, Icarus Verilog, GTKWave**, and the **SKY130 PDK**.  

---

## 📑 Table of Contents
1. [Roadmap](#-roadmap)  
2. [Day-Wise Labs](#-day-wise-labs)  
3. [Simulation Workflow](#️-simulation-workflow)  
4. [Summary](#summary)   
5. [Contributor](#contributor)  

---

## 🛠 Roadmap

| Day   | Topic                     | Key Highlights                          |
| ----- | ------------------------- | --------------------------------------- |
| Day 1 | RTL Basics                | Mux, DFF, testbenches                   |
| Day 2 | Timing & Hierarchy        | Timing libs, Hierarchical vs Flat       |
| Day 3 | RTL Optimizations         | Constant propagation, Retiming, Cloning |
| Day 4 | Gate-Level Simulation     | Blocking vs Non-blocking, Mismatches    |
| Day 5 | Synthesis Optimizations   | If-else, Case, Latches, Loops, Generate |

---

## 📂 Day-Wise Labs

- Day 1 – RTL Design & Synthesis
- Day 2 – Timing, Hierarchy & Flop Styles
- Day 3 – Combinational & Sequential Optimizations 
- Day 4 – GLS & Simulation Mismatches
- Day 5 – Optimization in Synthesis

---

## ⚙️ Simulation Workflow
1. Go to the respective **Day X** folder.  
2. Compile RTL/netlist with:  
   ```bash
   iverilog design.v tb.v -o sim.out
   vvp sim.out
   gtkwave dump.vcd
3. Compare results with provided waveforms/screenshots.

## Summary

- Day 1: RTL basics → Mux, Flip-Flops, testbenches.

- Day 2: Timing libs, coding styles, Hierarchical vs Flat synthesis.

- Day 3: RTL optimization → propagation, cloning, retiming.

- Day 4: Gate-Level Simulation, blocking vs non-blocking, mismatch analysis.

- Day 5: Synthesis optimization → if-else/case, inferred latches, loops, generate constructs.

## Contributor
Mohanapriyan p
