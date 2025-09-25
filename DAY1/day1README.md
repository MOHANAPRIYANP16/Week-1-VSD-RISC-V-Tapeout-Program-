# Week-1-VSD-RISC-V-Tapeout-Program

# 🔧 Verilog RTL Design & Synthesis – Day 1  
**Design, Simulation, and Synthesis of a 2:1 Multiplexer using Yosys & SkyWater 130nm PDK**

![Verilog](https://img.shields.io/badge/HDL-Verilog-blue)  
![Yosys](https://img.shields.io/badge/EDA-Yosys-green) 
![Yosys](https://img.shields.io/badge/EDA-iverilog-green)  
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
    
3. [Synthesis](#synthesis) 
    - invoke Yosys
    - Reading Design and Library
    - Generic Synthesis and Technology Mapping
    - Netlist Visualizing 
    - Writing Gate-Level Netlist
    - Generating Reports and Area Estimation
   
4. [Summary](#summary)  

---
## Overview  
This repository demonstrates the **complete RTL design flow** of a 2:1 multiplexer (`good_mux.v`).  
We go step by step from:  
1. Writing the design and testbench.  
2. Simulating with **Icarus Verilog** and analyzing waveforms in **GTKWave**.  
3. Synthesizing with **Yosys** using the **Skywater 130nm standard cell library**.  

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

## Step 3: Open the waveform in GTKWave
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

---

### Summary

This section explains the full Verilog simulation workflow using `good_mux.v` and its testbench `tb_good_mux.v`. The steps are:

1. **Go** to the folder containing both the design and testbench so that all files are available.  
2. **Compile** the design and testbench with `iverilog -o` to create an executable simulation file (`good_mux_sim.vvp`).  
3. **Execute** the simulation using `vvp`, which generates a `.vcd` file (Value Change Dump) that records all signal activities.  
4. **Open** the `.vcd` file in GTKWave (`gtkwave tb_good_mux.vcd`) to study waveforms and confirm design correctness.
   This flow demonstrates the common **Verilog simulation cycle: compile → simulate → analyze → validate**. Following these commands helps maintain clean file management and ensures reliable functional verification of RTL designs.
--- 
# synthesis 

Synthesis is the process of converting RTL design (Verilog) into a gate-level netlist.  
It uses tools like **Yosys** with the **SkyWater 130nm library** to map logic onto standard cells.  
This step ensures the design is functionally correct, optimized, and ready for physical design.  

---

**Workflow:**  
1. **Read RTL** – Load the Verilog design files.  
2. **Read Library** – Import SkyWater standard cell library.  
3. **Synthesize** – Use Yosys to translate RTL → gate-level representation.  
4. **Optimize** – Improve area, timing, and power usage.  
5. **Write Netlist** – Export the final synthesized design for backend flow.

 ---
## step by step flow of synthesis
### 1 : Invoke yosys
Run :

```
  yosys
```
This will launch the yosys . inside yosys systhesis is performed.

The following command are run in root :

### 2 . Read the liberty library
```
 read_liberty -lib ~/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib 

```
- `read_liberty`: Reads the target standard cell library.  

### 3. Read the Verilog code.
```
read_verilog ~/sky130RTLDesignAndSynthesisWorkshop/verilog_files/good_mux.v
```
- `read_verilog`: Reads the Verilog design file.

### 4: Synthesize the top-level module
this synthesizing the top-level module, which converts the RTL design into a technology-independent netlist.

```
synth -top good_mux
```
### 5. Technology mapping
dfflibmap: Maps flip-flops to standard cells.
```
dfflibmap -liberty ~/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib

```
abc: Optimizes logic and maps it to the technology library.

```
abc -liberty ~/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib

```
### 6. Visualize the gate-level netlist

```
show
```
### Synthesis Outputs

#### Netlist Dot File:

![Dot File](https://github.com/MOHANAPRIYANP16/Week-1-VSD-RISC-V-Tapeout-Program-/blob/main/DAY1/images/show.png)

### 7: Write the Gate-Level Netlist

```bash
write_verilog good_mux_netlist.v 
```

This generates the synthesized gate-level netlist in Verilog format. 

---

### 7: Generate Reports

```bash
stat
```


#### Statistics:

```=== good_mux ===

        +----------Local Count, excluding submodules.
        | 
        8 wires
        8 wire bits
        4 public wires
        4 public wire bits
        4 ports
        4 port bits
        1 cells
        1   sky130_fd_sc_hd__mux2_1

```
#### Rough Area :

Multiply counts × Liberty cell area

1 × `sky130_fd_sc_hd__mux2_1`

1 × 4.0 µm² = 4.0 µm²

Estimated area: **4.0 µm²**

---

### Summary  

In this section, we carried out the **RTL-to-gate-level synthesis** of `good_mux.v` using Yosys. The steps involved were:  

1. **Starting Yosys** and loading both the Verilog design and the target standard cell library.  
2. **Executing synthesis** (`synth`) to transform the RTL into a generic logic network.  
3. **Technology mapping** (`dfflibmap` and `abc`) to optimize and bind the logic to library cells.  
4. **Viewing the synthesized netlist** with `show` to examine gates and interconnections.  
5. **Exporting the gate-level netlist** (`write_verilog`) to a chosen directory for later stages.  
6. **Producing synthesis reports** (`stat`) to review cell usage, area, and other details.  

This process illustrates the end-to-end synthesis flow: **read design → synthesize → map → inspect → export netlist → review reports**, confirming functionality and preparing the design for backend steps like placement and routing.  

---

