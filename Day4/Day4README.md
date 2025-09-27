# Day 4: Gate-Level Simulation (GLS), Blocking vs. Non-Blocking in Verilog, and Synthesis-Simulation Mismatch  

Welcome to **Day 4 of the RTL Workshop!**  
In this session, we will dive into three important concepts in digital design:  

- **Gate-Level Simulation (GLS)**  
- **Blocking vs. Non-Blocking Assignments in Verilog**  
- **Synthesis-Simulation Mismatch**  

You’ll explore the theory along with practical insights, followed by hands-on labs to strengthen your understanding.  

---

## 📑 Table of Contents  
1. [Gate-Level Simulation (GLS)](#1-gate-level-simulation-gls)  
2. [Synthesis-Simulation Mismatch](#2-synthesis-simulation-mismatch)  
3. [Blocking vs. Non-Blocking Assignments in Verilog](#3-blocking-vs-non-blocking-assignments-in-verilog)  
   - [Blocking Statements](#blocking-statements-in-verilog-)  
   - [Non-Blocking Statements](#non-blocking-statements-in-verilog-)  
   - [Comparison Table](#comparison-table)  
4. [Labs](#labs)  
5. [Summary](#summary)  

---

## 1. Gate-Level Simulation (GLS)  

**Gate-Level Simulation (GLS)** is an essential verification step in the VLSI flow where the synthesized gate-level netlist is simulated to ensure:  

- Functional accuracy  
- Timing correctness  
- Power estimation  
- Test features (e.g., scan chains for DFT)  
 
  ![gls](https://github.com/MOHANAPRIYANP16/Week-1-VSD-RISC-V-Tapeout-Program-/blob/main/Day4/Images/gls%20image.png)

### 🔹 Why Perform GLS?  
- **Synthesis Verification:** Confirms that RTL is accurately translated into gates by synthesis tools.  
- **Timing Analysis:** Simulates with realistic delays (from SDF files) to identify setup/hold violations.  
- **DFT Validation:** Ensures scan chains and other testability structures work correctly after synthesis.  

### 🔹 When is GLS Done?  
- **Post-synthesis:** After converting RTL to a gate-level netlist.  
- **Pre-layout:** Before physical design, to detect issues early.  

### 🔹 Types of GLS  
- **Functional GLS:** Logic-only simulation (zero or unit delays).  
- **Timing GLS:** Simulation with annotated timing data for real-world behavior.  

---

## 2. Synthesis-Simulation Mismatch  

A **synthesis-simulation mismatch** occurs when the behavior of RTL simulation (before synthesis) does not align with the gate-level simulation or actual hardware results.  

### 🔹 Causes of Mismatch:  
- **Non-synthesizable code:** Use of delays, `initial` blocks, or unsupported constructs.  
- **Incomplete coding style:** Missing `else` statements, poor sensitivity lists, etc.  
- **Tool interpretation differences:** Ambiguous RTL may be understood differently by simulation and synthesis tools.  

> [!TIP]  
>   Always write **clear, synthesizable RTL** and follow best practices to avoid mismatches.  

---

## 3. Blocking vs. Non-Blocking Assignments in Verilog  

Verilog provides two types of procedural assignments:  

## Blocking Statements in Verilog (`=`)

Blocking statements are mainly used to model **combinational logic**.  
They execute **immediately** in the given order, just like normal programming assignments.  

### 🔹 Key Points
- **Operator:** `=`
- **Execution:** Sequential; the next statement executes only after the current one finishes.
- **Behavior:** Updates happen instantly in the order written.
- **Usage:** Commonly used inside `always @(*)` for combinational logic.
- **Caution:** Can cause race conditions if misused in sequential logic.

###  Example
```verilog
always @(*) begin
    y = a & b;       // y is updated immediately
    z = y | c;       // z uses the updated value of y
end
```

## Non-Blocking Statements in Verilog (`<=`)

Non-blocking statements are used to model **sequential logic** such as registers and flip-flops.  
They allow multiple assignments to occur **concurrently** at the end of the simulation time step,  
making them ideal for clocked logic.  

### 🔹 Key Points
- **Operator:** `<=`
- **Execution:** Scheduled; assignment happens at the end of the current time step.
- **Behavior:** Multiple updates can occur in parallel without overwriting each other.
- **Usage:** Commonly used inside `always @(posedge clk)` or `always @(negedge clk)`.
- **Advantage:** Prevents race conditions in sequential circuits.

###  Example
```verilog
always @(posedge clk) begin
    q <= d;          // q gets updated with d at the clock edge
    count <= count + 1;  // safe parallel update
end
```

###  Comparison Table  

| Feature / Aspect              | Blocking (`=`)                          | Non-Blocking (`<=`)                   |
|-------------------------------|------------------------------------------|----------------------------------------|
| **Operator Used**             | `=`                                      | `<=`                                   |
| **Execution Order**           | Executes sequentially, in code order     | Executes concurrently, scheduled later |
| **Update Timing**             | Updates immediately                     | Updates at the end of the time step    |
| **Best Suited For**           | Combinational logic                     | Sequential logic (registers/FFs)       |
| **Hardware Inference**        | Infers combinational gates               | Infers flip-flops / sequential logic   |
| **Coding Style Example**      | `always @(*) y = a & b;`                 | `always @(posedge clk) q <= d;`        |

## Labs 

### Labs1 (ternary_operator_mux)

#### 1. **locate the file** in your project directory. 

This is done using,
```bash
cd ~/sky130RTLDesignAndSynthesisWorkshop/verilog_files
```
**Verilog code**

![verilog](https://github.com/MOHANAPRIYANP16/Week-1-VSD-RISC-V-Tapeout-Program-/blob/main/Day4/Images/ternaryverilog.png)

#### 2. **Simulation**

Run:

```

iverilog -o ternary_operator_mux_sim.vvp  ternary_operator_mux.v tb_ternary_operator_mux.v
vvp ternary_operator_mux_sim.vvp 
gtkwave tb_ternary_operator_mux.vcd

```
GTKwave:

![gtkwave_ternary_operator](https://github.com/MOHANAPRIYANP16/Week-1-VSD-RISC-V-Tapeout-Program-/blob/main/Day4/Images/gtkwave_ternary_operator.png)

##  Synthesis of ternary operator mux Using Yosys

### launch yosys :

Run:
```
yosys
```
then run :
```bash
read_liberty -lib ~/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog ternary_operator_mux.v
synth -top  ternary_operator_mux
opt_clean -purge
abc -liberty ~/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```
DOt file :

![dot_file](https://github.com/MOHANAPRIYANP16/Week-1-VSD-RISC-V-Tapeout-Program-/blob/main/Day4/Images/ternary_dot_file.png)

##  Gate-Level Simulation (GLS) of ternary operator MUX

Gate-Level Simulation is performed using the synthesized netlist (ternary_operator_mux_net.v). This helps verify the functional correctness of the design after synthesis, using the actual standard cells and any delays (if modeled)

```bash
iverilog -o ~/a.out ../my_lib/verilog_model/primitives.v  ../my_lib/verilog_model/sky130_fd_sc_hd.v ternary_operator_mux_net.v tb_ternary_operator_mux.v
cd 
./a.out
gtkwave tb_ternary_operator_mux.vcd
```

GTKwave:

![gls](https://github.com/MOHANAPRIYANP16/Week-1-VSD-RISC-V-Tapeout-Program-/blob/main/Day4/Images/ternary_operatorgls2now.png)

## Lab 2

**Get into the directory:**

run :
```bash
cd ~/sky130RTLDesignAndSynthesisWorkshop/verilog_files
```

 verilog file 

```
gedit bad_mux.v
```
![bad_mux](https://github.com/MOHANAPRIYANP16/Week-1-VSD-RISC-V-Tapeout-Program-/blob/main/Day4/Images/bad_muxverilog.png)

### simulation:

```
iverilog -o bad_mux_sim.vvp bad_mux.v tb_bad_mux.v
vvp bad_mux_sim.vvp
```

then,
```
gtkwave tb_bad_mux.vcd
```

![gtkwave_bad](https://github.com/MOHANAPRIYANP16/Week-1-VSD-RISC-V-Tapeout-Program-/blob/main/Day4/Images/gtkwave_bad_mux.png)


#### Synthesis of Bad mux Using Yosys

```
yosys
```
Inside yosys, run:

```bash
read_liberty -lib ~/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog bad_mux.v
synth -top  bad_mux
opt_clean -purge
abc -liberty ~/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show

```
**Dot file:**

![dotfile](https://github.com/MOHANAPRIYANP16/Week-1-VSD-RISC-V-Tapeout-Program-/blob/main/Day4/Images/bad_mux_dot_file.png)

---
## GLS of Bad MUX
Gate-Level Simulation is performed using the synthesized netlist (bad_mux_net.v). 

```bash
iverilog -o ~/a.out ../my_lib/verilog_model/primitives.v  ../my_lib/verilog_model/sky130_fd_sc_hd.v bad_mux_net.v tb_bad_mux.v
cd 
./a.out
gtkwave tb_bad_mux.vcd
```
GTKwave:

![gls3](https://github.com/MOHANAPRIYANP16/Week-1-VSD-RISC-V-Tapeout-Program-/blob/main/Day4/Images/gls3bad.png)


## Lab3: Blocking Assignment Caveat

**Get into the directory:**

run :
```bash
cd ~/sky130RTLDesignAndSynthesisWorkshop/verilog_files
```

verilog code for blocking caveat :

```bash
gedit blocking_caveat.v 

```

![caveatverilog](https://github.com/MOHANAPRIYANP16/Week-1-VSD-RISC-V-Tapeout-Program-/blob/main/Day4/Images/blocking_caveatverilog.png)

**Simulation:**
```bash
iverilog -o blocking_caveat_sim.vvp blocking_caveat.v tb_blocking_caveat.v
vvp blocking_caveat_sim.vvp
gtkwave tb_blocking_caveat.vcd
```

> note
> this will save in root
> and gtkwave also run in root


#### Synthesis of Blocking  Caveat  Using Yosys

```
yosys
```
Inside yosys, run:

```bash
read_liberty -lib ~/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog blocking_caveat.v
synth -top  blocking_caveat
opt_clean -purge
abc -liberty ~/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show

```
DOt file:

![blocking dot file](https://github.com/MOHANAPRIYANP16/Week-1-VSD-RISC-V-Tapeout-Program-/blob/main/Day4/Images/blocking_caveat_dot_file.png)

## GLS of Blocking  Caveat 
Gate-Level Simulation is performed using the synthesized netlist (blocking_caveat_net.v). 

```bash
iverilog -o ~/a.out ../my_lib/verilog_model/primitives.v  ../my_lib/verilog_model/sky130_fd_sc_hd.v blocking_caveat_net.v tb_blocking_caveat.v
cd 
./a.out
gtkwave tb_blocking_caveat.vcd
```
GTKwave:

![glsblocking](https://github.com/MOHANAPRIYANP16/Week-1-VSD-RISC-V-Tapeout-Program-/blob/main/Day4/Images/blocking_caveatgls.png)

## Summary 

- **GLS:** Checks functionality, timing, and testability after synthesis.  
- **Mismatch:** Happens when RTL sim ≠ gate-level sim; avoid by using synthesizable, clear RTL.  
- **Blocking (`=`):** Executes step-by-step, used for **combinational logic**.  
- **Non-Blocking (`<=`):** Executes in parallel at clock edge, used for **sequential logic**.  

> [!TIP]
> Use `=` for combinational, `<=` for sequential, and always verify with GLS.  



