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
3. [Flip-Flop Coding Styles](#various-flip-flop-coding-styles)  
   - Asynchronous Reset/Set  
   - Synchronous Reset/Set  
4. [Simulation and Synthesis of Flip-Flops](#-simulation--synthesis-of-flip-flops
)  
   - Simulation workflow (`iverilog → vvp → GTKWave`)  
   - Synthesis workflow (Yosys, netlist visualization)  
5. [Summary](#summary)  

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

## 🔧 What is Synthesis?  
Synthesis is the process of converting **RTL code (Verilog/VHDL)** into a **gate-level netlist** using a standard cell library.  
It maps high-level design into actual logic gates that can be fabricated.  

---

## 🏗️ Types of Synthesis  

### 1. Hierarchical Synthesis  
- Design is kept **module-wise (hierarchical)**.  
- Each module is synthesized separately.  
- Preserves design structure → easier debugging and reuse.  
- Netlist is **organized by modules**.  

**Disadvantage:**  
- Limited optimization across module boundaries → may lead to **larger area and slower performance** compared to flat synthesis.  

---

### 2. Flat Synthesis  
- Entire design is **flattened into a single module**.  
- Optimizations are applied across module boundaries.  
- May achieve **better performance/area**.  
- Netlist is a **single-level structure**.  

**Disadvantage:**  
- Loss of hierarchy → **harder debugging, longer runtime, and higher memory usage** for large designs.  

---

✅ **In short:**  
- **Hierarchical →** Easier to debug, but less optimized.  
- **Flat →** Highly optimized, but harder to debug/manage.  

In practice, designers often use a **mixed approach**, keeping critical modules hierarchical while flattening performance-critical paths to achieve a balance between manageability and optimization.

The first step is to **locate the file** in your project directory. \
This is done using,
```bash
cd ~/Documents/Verilog/sky130RTLDesignAndSynthesisWorkshop/verilog_files
```
This design for multiple modules (verilog)
```verilog
module sub_module2 (input a, input b, output y);
	assign y = a | b;
endmodule

module sub_module1 (input a, input b, output y);
	assign y = a&b;
endmodule


module multiple_modules (input a, input b, input c , output y);
	wire net1;
	sub_module1 u1(.a(a),.b(b),.y(net1));  //net1 = a&b
	sub_module2 u2(.a(net1),.b(c),.y(y));  //y = net1|c ,ie y = a&b + c;
endmodule
```
## Hierarchical Synthesis Workflow

**Hierarchical synthesis of `multiple_modules.v` using Yosys:**

---

#### 1. **Navigate to the working directory**

```bash
cd ~/Documents/Verilog/Labs
```
#### 2. **Launch Yosys and run hierarchical synthesis**
```bash
yosys
```
#### 3. **Run this command inside yosys:**
```bash
read_liberty -lib ~/Documents/Verilog/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog ~/Documents/Verilog/sky130RTLDesignAndSynthesisWorkshop/verilog_files/multiple_modules.v 
dfflibmap -liberty ~/Documents/Verilog/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib  
abc -liberty ~/Documents/Verilog/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib  
synth -top multiple_modules
show multiple_modules
show -format png multiple_modules
write_verilog ~/Documents/Verilog/Labs/multiple_modules_hier.v 
```
### Verify the synthesis
#### The Hierarchical Synthesis Workflow :
![Workflow](https://github.com/MOHANAPRIYANP16/Week-1-VSD-RISC-V-Tapeout-Program-/blob/main/Day2/Images/Hierarchical%20Synthesis_workflow.png)

#### Netlist Dot File: : 

![yosys_show_multiple_modules](https://github.com/MOHANAPRIYANP16/Week-1-VSD-RISC-V-Tapeout-Program-/blob/main/Day2/Images/multiplemodule.png)

---

#### Statistics:

<pre>

=== multiple_modules ===

        +----------Local Count, excluding submodules.
        | 
        5 wires
        5 wire bits
        5 public wires
        5 public wire bits
        4 ports
        4 port bits
        2 submodules
        1   sub_module1
        1   sub_module2

=== sub_module1 ===

        +----------Local Count, excluding submodules.
        | 
        3 wires
        3 wire bits
        3 public wires
        3 public wire bits
        3 ports
        3 port bits
        1 cells
        1   $_AND_

=== sub_module2 ===

        +----------Local Count, excluding submodules.
        | 
        3 wires
        3 wire bits
        3 public wires
        3 public wire bits
        3 ports
        3 port bits
        1 cells
        1   $_OR_

=== design hierarchy ===

        +----------Count including submodules.
        | 
        2 multiple_modules
        1 sub_module1
        1 sub_module2

        +----------Count including submodules.
        | 
       11 wires
       11 wire bits
       11 public wires
       11 public wire bits
       10 ports
       10 port bits
        - memories
        - memory bits
        - processes
        2 cells
        1   $_AND_
        1   $_OR_
        2 submodules
        1   sub_module1
        1   sub_module2
</pre>

> [!TIP]  
>  Open another terminal for Flat Synthesis

## Flat synthesis .

**Flat synthesis of `multiple_modules.v` using Yosys:**

#### 1. **Open the directory where you want to run the synthesis**

```bash
cd ~/Documents/Verilog/Labs
```

#### 2. **Launch Yosys and run hierarchical synthesis**
```bash
yosys
```
#### 3. **Run this command inside yosys:**

```bash
read_verilog ~/Documents/Verilog/sky130RTLDesignAndSynthesisWorkshop/verilog_files/multiple_modules.v 
synth -top multiple_modules
abc -liberty ~/Documents/Verilog/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
flatten
show multiple_modules
show -format png multiple_modules
write_verilog ~/Documents/Verilog/Labs/multiple_modules_flat.v 
```
**Explanation:**  
- `synth -top` → generates the gate-level netlist of the flattened design. 
- `abc` → performs technology mapping and logic optimization. 
- `flatten` → removes module hierarchy so the tool can optimize across the entire design.    
- `write_verilog` → outputs the flat netlist ready for further analysis or place-and-route.


#### Flat synthesis workflow :

![Flat Synthesis Workflow](https://github.com/MOHANAPRIYANP16/Week-1-VSD-RISC-V-Tapeout-Program-/blob/main/Day2/Images/flat_workflow.png)

#### Netlist Dot File:

![yosys_show_multiple_modules_flat](https://github.com/MOHANAPRIYANP16/Week-1-VSD-RISC-V-Tapeout-Program-/blob/main/Day2/Images/Flat_Synthesis_show.png)

#### Statistics

<pre>
=== multiple_modules ===

        +----------Local Count, excluding submodules.
        | 
        5 wires
        5 wire bits
        5 public wires
        5 public wire bits
        4 ports
        4 port bits
        2 submodules
        1   sub_module1
        1   sub_module2

=== sub_module1 ===

        +----------Local Count, excluding submodules.
        | 
        3 wires
        3 wire bits
        3 public wires
        3 public wire bits
        3 ports
        3 port bits
        1 cells
        1   $_AND_

=== sub_module2 ===

        +----------Local Count, excluding submodules.
        | 
        3 wires
        3 wire bits
        3 public wires
        3 public wire bits
        3 ports
        3 port bits
        1 cells
        1   $_OR_

=== design hierarchy ===

        +----------Count including submodules.
        | 
        2 multiple_modules
        1 sub_module1
        1 sub_module2

        +----------Count including submodules.
        | 
       11 wires
       11 wire bits
       11 public wires
       11 public wire bits
       10 ports
       10 port bits
        - memories
        - memory bits
        - processes
        2 cells
        1   $_AND_
        1   $_OR_
        2 submodules
        1   sub_module1
        1   sub_module2
</pre>


### Comparison 

![Comparison](https://github.com/MOHANAPRIYANP16/Week-1-VSD-RISC-V-Tapeout-Program-/blob/main/Day2/Images/comparison.png)

## 📊 Hierarchical vs Flat Synthesis  

| Feature            | Hierarchical Synthesis | Flat Synthesis |
|--------------------|------------------------|----------------|
| **Design Style**   | Keeps **modules separate** | Flattens everything into **one module**  |
| **Optimization**   | Limited across modules  | Global optimization  |
| **Area/Performance** | Slightly larger area, slower  | Better area and timing  |
| **Debugging**      | Easier (preserves structure)  | Harder (no hierarchy)  |
| **Reuse**          | Modules can be reused easily  | Not reusable (flattened)  |
| **EDA Tool Effort**| Faster runtime, less memory  | Higher runtime & memory usage  |


# 🏗️ Sub-Module Synthesis  

RTL (Register Transfer Level) designs are typically modular, consisting of multiple functional blocks or sub-modules.  
**Sub-module level synthesis** allows each sub-module to be synthesized independently.  

---

##  Why is Sub-Module Level Synthesis Important?  

- ** Optimization & Area Reduction**  
  Each sub-module can be optimized separately (logic optimization, technology mapping, area minimization), leading to smaller overall chip area.  

- ** Reusability**  
  Sub-modules can be independently designed, verified, and reused across multiple designs.  

- **Parallel Processing**  
  Large designs can be synthesized faster by processing sub-modules concurrently.  

---

##  Commands to Run Sub-Module Synthesis  

```bash
read_liberty -lib ~/Documents/Verilog/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog ~/Documents/Verilog/sky130RTLDesignAndSynthesisWorkshop/verilog_files/multiple_modules.v
synth -top sub_module1
abc -liberty ~/Documents/Verilog/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
show -format png
```
## Workflow & Visualization

![Workflow](https://github.com/MOHANAPRIYANP16/Week-1-VSD-RISC-V-Tapeout-Program-/blob/main/Day2/Images/Sub-module%20Synthesis_workflow.png)

Netlist Dot File :

![show](https://github.com/MOHANAPRIYANP16/Week-1-VSD-RISC-V-Tapeout-Program-/blob/main/Day2/Images/sub_module_show.png)

#### Statistics 

<pre>
=== sub_module1 ===

        +----------Local Count, excluding submodules.
        | 
        3 wires
        3 wire bits
        3 public wires
        3 public wire bits
        3 ports
        3 port bits
        1 cells
        1   $_AND_

</pre>

---

## Various Flip-Flop Coding Styles
#### 1. Asynchronous Reset Flip-Flops
   
```
   always @(posedge clk or posedge rst) begin
    if (rst)
        q <= 0;
    else
        q <= d;
end
```
- Output q resets immediately when rst is asserted.

- Useful for global reset in ASIC/FPGA designs.

#### 2. Asynchronous Set Flip-Flop.
```
always @(posedge clk or posedge set) begin
    if (set)
        q <= 1;
    else
        q <= d;
end
```
- Output q is set to 1 immediately when set is asserted.

- Helps initialize signals quickly.

#### 3. Synchronous Reset Flip-Flop.
```
always @(posedge clk) begin
    if (rst)
        q <= 0;
    else
        q <= d;
end
```
- Reset happens only on clock edge.

- Keeps all FFs aligned with the clock.

#### 4. Synchronous Set Flip-Flop.
```
always @(posedge clk) begin
    if (set)
        q <= 1;
    else
        q <= d;
end
```
- Set happens only on clock edge.

- Ensures predictable sequential behavior.


## 🧪 Simulation & Synthesis of Flip-Flops

### Simulation

> [!TIP]  
>  Open another new terminal for Simulation & Synthesis of Flip-Flops.

1. Navigate to verilog_files folder:
```
cd Documents/Verilog/sky130RTLDesignAndSynthesisWorkshop/verilog_files
iverilog -o ~/Documents/Verilog/Labs/dff_syncres.vvp dff_syncres.v tb_dff_syncres.v

```
2. Run in Labs folder:
```
cd ~/Documents/Verilog/Labs
vvp dff_syncres.vvp
```
3. Open waveform in GTKWave:
 ```
gtkwave tb_dff_syncres.vcd
```
4. Waveform:

![Waveform](https://github.com/MOHANAPRIYANP16/Week-1-VSD-RISC-V-Tapeout-Program-/blob/main/Day2/Images/tb_dff_syncres.vcd.png)

### Synthesis (Yosys)
1. Run:
```
yosys
```

#### 2. **Run this command inside yosys:**
```
read_liberty -lib ~/Documents/Verilog/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog ~/Documents/Verilog/sky130RTLDesignAndSynthesisWorkshop/verilog_files/dff_syncres.v
synth -top dff_syncres
dfflibmap -liberty ~/Documents/Verilog/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty ~/Documents/Verilog/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
show -format png
```

## **Final Netlist Dot File** :

![Synthesis](https://github.com/MOHANAPRIYANP16/Week-1-VSD-RISC-V-Tapeout-Program-/blob/main/Day2/Images/final_netlistday2.png)

#  Summary

---

## Timing Libraries  
- `.lib` files (e.g., **`sky130_fd_sc_hd__tt_025C_1v80.lib`**)  
- Contain **standard cell timing, power, and functional models**  
- Used for **accurate synthesis, STA, and power analysis**

---

##  Synthesis Approaches  

### 🔹 Hierarchical Synthesis  
-  Synthesizes **sub-modules individually**  
-  Allows **local optimization** and **area control**  
-  May limit **global optimizations**

### 🔹 Flat Synthesis  
-  Synthesizes the **entire design as one block**  
-  Better **global optimization**  
-  Higher runtime & memory usage  
   Harder debugging, less reuse  

> [!TIP]  
>  Flattening can be applied **after hierarchical synthesis** if needed.
---

##  Flip-Flop Coding Styles  

-  **Asynchronous Reset/Set**  
  - Immediate response, independent of clock  
  - Great for **global initialization**  

-  **Synchronous Reset/Set**  
  - Changes only at **clock edges**  
  - Ensures **predictable timing** and avoids hazards  

---

##  Workflow (Simulation & Synthesis)  

###  Simulation  
```bash
iverilog → vvp → GTKWave
```
### Synthesis (Yosys)
```
read_liberty → read_verilog → synth → dfflibmap → abc → show
```

With this flow, you ensure timing accuracy, efficient synthesis, and reliable sequential logic design.


---


