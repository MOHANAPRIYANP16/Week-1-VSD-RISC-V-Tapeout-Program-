# Day 2 - Timing Libraries, Hierarchical vs Flat Synthesis, and Efficient Flip-Flop Coding Styles

This document introduces three key aspects of RTL design and synthesis:  
1. The `.lib` is a timing library (`sky130_fd_sc_hd__tt_025C_1v80.lib`) and open-source PDKs - defining timing, power, and functionality.  
2. Synthesis methodologies – comparing hierarchical and flat approaches with their trade-offs.  
3. Efficient flip-flop coding styles – improving performance, power, and synthesis quality.  

---

## 📑 Table of Contents
1. [Timing Libraries](#-timing-libraries)  
   - SKY130 PDK  
   - Understanding `.lib` files  
   - File inspection and contents  
2. [Hierarchical vs Flat Synthesis](#-hierarchical-vs-flat-synthesis)  
   - Hierarchical Synthesis (workflow, commands, verification)  
   - Flat Synthesis (workflow, commands, verification)  
   - Comparison Table  
   - Sub-module Level Synthesis  
3. [Flip-Flop Coding Styles](#-the-various-flip-flop-coding-styles)  
   - Asynchronous Reset/Set  
   - Synchronous Reset/Set  
4. [Simulation and Synthesis of Flip-Flops](#-synthesis-and-simulation-of-flip-flops)  
   - Simulation workflow (`iverilog → vvp → GTKWave`)  
   - Synthesis workflow (Yosys, netlist visualization)  
5. [Summary](#-summary)  

---

## ⏱ Timing Libraries

# 🌐 SKY130 PDK Overview  
- The SKY130 PDK is an open-source 130 nm CMOS process design kit released by SkyWater Technology Foundry in collaboration with Google.
- It allows students, researchers, and startups to design, simulate, and fabricate integrated circuits (ICs) without expensive commercial tools.
- It includes .lib timing libraries, LEF/DEF files for layout, and GDSII data for fabrication.
- In .lib timing libraries like sky130_fd_sc_hd__tt_025C_1v80.lib, the filename itself encodes the PVT (Process, Voltage, Temperature) conditions used to characterize the cells.

---

## 🛠 Tools with SKY130  
- **Front-end:** Yosys (synthesis), OpenLane (RTL → GDS).  
- **Back-end:** Magic, KLayout (layout, DRC, LVS).  
- **Simulation:** Ngspice, Xyce.  
- **Verification:** Netgen, OpenSTA.  

---

## 📘 `.lib` Timing Libraries  
- A .lib (Liberty) file is a timing and power model of standard cells. It tells EDA tools how each cell (NAND, NOR, flip-flop, etc.) behaves under specific process, voltage, and temperature (PVT) conditions. Essential for synthesis, static timing analysis (STA), and power estimation.
- **sky130_fd_sc_hd__tt_025C_1v80.lib**
   -  sky130 → PDK family (SkyWater 130 nm).
   -  fd → Foundry (FD = Foundry).
   -  sc → Standard Cells.
   -  hd → High Density library (optimized for area).
   -  tt → Process corner = Typical-Typical.
   -  025C → Temperature = 25 °C. 1v80 → Voltage = 1.8 V supply.
  

#### Why Multiple .lib Files Exist Since chips operate in different corners, 
you’ll see multiple .lib files:
- tt_025C_1v80.lib → Typical process,25 °C, 1.8 V. 
- ss_125C_1v60.lib → Slow process, 125 °C, 1.6 V (worst-case delay). 
- ff_n40C_1v95.lib → Fast process, -40 °C, 1.95 V (best-case delay).

  So, `sky130_fd_sc_hd__tt_025C_1v80.lib` specifically refers to the High-Density standard cell library for SkyWater 130nm, characterized at **typical process**, **25°C**, and **1.8V**. 
--- 


### Inspecting the `.lib` file
Navigate to the library directory and 
You can inspect the contents of the library using a text editor like `vim`, `nano`, or `gedit`, here iam using gedit .

```bash
cd ~/Documents/Verilog/sky130RTLDesignAndSynthesisWorkshop/lib
ls
gedit sky130_fd_sc_hd__tt_025C_1v80.lib
```
## 📂 Contents of a `.lib` File  

A `.lib` (Liberty) file contains detailed definitions of each standard cell, including:

- **Cell names and types**  
  - Logic gates (AND, OR, NOT) and sequential cells (DFF, Latch, etc.)  

- **Pin definitions**  
  - Input, output, clock, and enable pins  

- **Timing information**  
  - Propagation delays, rise/fall times, setup and hold constraints  

- **Power characteristics**  
  - Dynamic power per transition and leakage power  

- **Operating conditions / PVT corners**  
  - Process, voltage, and temperature variations for characterization  

- **Constraints**  
  - Maximum fanout, minimum pulse width, and other cell-specific limits  

>  By understanding the structure of the `.lib` file , can  perform **accurate synthesis, timing analysis, and power estimation**.

![Timing library](https://github.com/MOHANAPRIYANP16/Week-1-VSD-RISC-V-Tapeout-Program-/blob/main/Day2/Images/Timing.lib.png)

