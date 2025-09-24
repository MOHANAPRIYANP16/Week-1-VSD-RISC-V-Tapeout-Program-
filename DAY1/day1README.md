# Week-1-VSD-RISC-V-Tapeout-Program

# 🔧 Verilog RTL Design & Synthesis – Day 1  
**Design, Simulation, and Synthesis of a 2:1 Multiplexer using Yosys & SkyWater 130nm PDK**

![Verilog](https://img.shields.io/badge/HDL-Verilog-blue)  
![Yosys](https://img.shields.io/badge/EDA-Yosys-green)  
![GTKWave](https://img.shields.io/badge/Simulator-GTKWave-orange)  
![SkyWater](https://img.shields.io/badge/PDK-Sky130-red)  

---

## 📑 Table of Contents  
1. [Overview](#overview)  

2. [Simulation](#simulation)
    - Simulator, Design, Testbench
    - Simulation WorkFlow
    - Compiling and Running good_mux.v
    - Viewing Waveforms in GTKWave
    - Verifying Results
    
3. [Synthesis Workflow](#synthesis-workflow) 
    - invoke Yosys
    - Reading Design and Library
    - Generic Synthesis and Technology Mapping
    - Netlist Visualizing 
    - Writing Gate-Level Netlist
    - Generating Reports and Area Estimation
   
4.[Summary](#summary)  


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

![simulation_workflow](https://github.com/MOHANAPRIYANP16/Week-1-VSD-RISC-V-Tapeout-Program-/blob/main/DAY1/images/SIMULATION.png)

---

# Lab Simulation 

 ## Step 1: clone the repository
 
 The Verilog programs are cloned from the sky130RTLDesignAndSynthesisWorkshop repository.
This repository also contains library files and Yosys run scripts which will help automate synthesis later.

To clone it  run:
```
git clone https://github.com/kunalg123/sky130RTLDesignAndSynthesisWorkshop
```
To list the files run :
```
cd sky130RTLDesignAndSynthesisWorkshop/verilog_files  
ls  
```
it will see something like :

![ls_png](https://github.com/MOHANAPRIYANP16/Week-1-VSD-RISC-V-Tapeout-Program-/blob/main/DAY1/images/ls.png)

## Step 2: Stimulate the design 
 compile the Design and Testbench
 ```
iverilog -o good_mux_sim.vvp good_mux.v tb_good_mux.v
```
Execute the compiled simulation file with:
```
vvp good_mux_sim.vvp
```
This runs the simulation and generates a .vcd file (Value Change Dump)

## Step 4: Open the waveform in GTKWave
  run the vcd file (contains all signal transitions during the simulation) in terminal to view wave forms in gtkwave
  ```
   gtkwave tb_good_mux.vcd
  ``` 
you will see something like : 
![good_mux](https://github.com/MOHANAPRIYANP16/Week-1-VSD-RISC-V-Tapeout-Program-/blob/main/DAY1/images/good_mux.png)


## verifying Outputs 
   - Observe the input signals and the corresponding output signals in GTKWave.
   - Match the output waveform from the gtkwave to the truth table of the 2:1 multiplexer.
   - ensure all the stimuli are matches well.

## Verilog Code Analysis
The code for the 2:1 multiplexer (good_mux.v):

```verilog
module good_mux (
    input  i0, 
    input  i1, 
    input  sel, 
    output reg y
);
always @(*)
begin
    if (sel)
        y <= i1;
    else 
        y <= i0;
end
endmodule
```
## How It Works
- Inputs: i0, i1 (data), sel (select line)
- Output: y (registered output)
- Logic: If selection line (sel) is 1, then output (y) gets input1(i1); if selection line  is 0, output (y) gets input0(i0).


## Summary  
...
