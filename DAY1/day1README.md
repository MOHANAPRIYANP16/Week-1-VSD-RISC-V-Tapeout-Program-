# Week-1-VSD-RISC-V-Tapeout-Program

# 🔧 Verilog RTL Design & Synthesis – Day 1  
**Design, Simulation, and Synthesis of a 2:1 Multiplexer using Yosys & SkyWater 130nm PDK**

![Verilog](https://img.shields.io/badge/HDL-Verilog-blue)  
![Yosys](https://img.shields.io/badge/EDA-Yosys-green)  
![GTKWave](https://img.shields.io/badge/Simulator-GTKWave-orange)  
![SkyWater](https://img.shields.io/badge/PDK-Sky130-red)  

---

## 📑 Table of Contents  
- [Overview](#overview)  
- [Simulation](#simulation)
    - Simulator, Design, Testbench
    - Simulation WorkFlow
    - Compiling and Running good_mux.v
    - Viewing Waveforms in GTKWave
    - Verifying Results
- [Synthesis Workflow](#synthesis-workflow)  
- [Summary](#summary)  


## Overview  
This repository demonstrates the **complete RTL design flow** of a 2:1 multiplexer (`good_mux.v`).  
We go step by step from:  
1. Writing the design and testbench.  
2. Simulating with **Icarus Verilog** and analyzing waveforms in **GTKWave**.  
3. Synthesizing with **Yosys** using the **SkyWater 130nm standard cell library**.  

---

## Simulation  
  A Verilog simulation requires 3 key components:

- **Simulator** → A software tool that verifies your digital circuit’s functionality by applying test inputs and observing outputs.  
- **Design**    → The Verilog code that describes the intended logic functionality.
 
- **Testbench** → A simulation environment that applies inputs to the design and checks if the outputs are correct.  


### Simulation Flow
1. Write the Verilog code (RTL Logic) for **Design**.  
2. Write the **Testbench** code  (applies stimulus).  
3. Compile both Design and Testbench using **Icarus Verilog (`iverilog`)**.  
4. Run the simulation with **`vvp`**.  
5. View waveforms in **GTKWave**.

![simulation_workflow](https://github.com/navneetprasad1311/vsd-soc-pgrm-w1/blob/008a514609306cd1d7bc3812fa1ff36b4c96e25a/Day1/Images/Example.png)

---

## Lab Simulation & Synthesis

The Verilog programs are cloned from the [sky130RTLDesignAndSynthesisWorkshop](https://github.com/kunalg123/sky130RTLDesignAndSynthesisWorkshop/tree/main/verilog_files) repository.  
This repository also contains library files and Yosys run scripts which will help automate synthesis later.

To clone the repository, run:
```bash
cd ~/Documents/Verilog  
# The directory where you want to store the files  
git clone https://github.com/kunalg123/sky130RTLDesignAndSynthesisWorkshop  


## Outputs  
...

## Summary  
...
