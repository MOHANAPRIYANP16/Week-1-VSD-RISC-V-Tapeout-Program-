# Day 3: Combinational and Sequential Optimization

---

## 📑 Table of Contents
- [Constant Propagation](#constant-propagation)  
- [State Optimization](#state-optimization)  
- [Cloning](#cloning)  
- [Retiming](#retiming)
- [Labs on optimization](#labs-on-Optimization)
  - [Combinational Logic Optimization](#combinational-logic-optimization)
     - Labs1
     - Labs2
     - Labs3
     - Labs4 
  - [Sequential Logic Optimization](#sequential-logic-optimization)
     - Labs6
     - Labs7
  -[summary](#summary)

---

## Constant Propagation  
### 1. What is it?  
- Replace signals driven by **constant values (0 or 1)** with fixed logic.  
- Eliminates unnecessary gates and wires.  

### 2. Detailed Info  
Constant propagation checks if a net or signal always evaluates to a fixed value and simplifies the circuit accordingly. For example, if a gate input is always `0`, the output can be optimized to `0` without the actual logic.  

### Advantages  
- Reduces **gate count**  
- Simplifies **logic equations**  
- Improves **area and power efficiency**  

---

## State Optimization  
### 1. What is it?  
- Reduces the number of **states in a finite state machine (FSM)** by merging equivalent or unreachable states.  

### 2. Detailed Info  
In FSMs, some states may be redundant (behaving the same as others) or unreachable. State optimization identifies and removes them, leading to a minimal FSM.  

###  Advantages  
- Reduces **flip-flop count**  
- Improves **timing** (fewer state transitions)  
- Lower **area and power** usage  

---

## Cloning  
### 1. What is it?  
- Duplicate logic or registers to **reduce fanout** and **improve timing**.  

### 2. Detailed Info  
If a single gate or flip-flop drives many loads, the delay increases. Cloning creates a copy of that logic/register so loads are split between them.  

###  Advantages  
- Reduces **net delay** from high fanout  
- Improves **timing closure**  
- Helps achieve **higher operating frequency**

  ![Timing library](https://github.com/MOHANAPRIYANP16/Week-1-VSD-RISC-V-Tapeout-Program-/blob/main/Day2/Images/sky130starting.png)

---

## Retiming  
### 1. What is it?  
- **Reposition registers (flip-flops)** in a circuit without changing functionality.  

### 2. Detailed Info  
Retiming balances combinational delays by moving flip-flops across logic gates. It helps meet setup/hold timing while preserving overall input-output behavior.  

###  Advantages  
- Optimizes **critical path delay**  
- Enables **higher clock frequency**  
- Balances **pipeline stages** for better performance  

 ![Timing library](https://github.com/MOHANAPRIYANP16/Week-1-VSD-RISC-V-Tapeout-Program-/blob/main/Day2/Images/sky130starting.png)

---

# 5. Labs on Optimization

Optimization in digital design ensures **minimal area**, **reduced power consumption**, and **improved timing performance**.  
This lab explores **Combinational** and **Sequential Logic Optimization** techniques.

---

## 🔹 Combinational Logic Optimization
###  Objective:
Squeeze and simplify logic to achieve the **most optimized design** in terms of **area** and **power**.

---

###  Techniques:

#### 1. Constant Propagation
- Replaces **logic blocks with constants** when input values are fixed.  
- **Example:**
  `Y = ((A·B) + C)'
If A = 0 → Y = (0 + C)' = C'`

- **Result:**  
- Complex gate logic → **6 MOS transistors**  
- Simplified to → **1 inverter (2 MOS transistors)** ✅

---

#### 2. Boolean Logic Optimization
- Simplify Boolean expressions using:
- Karnaugh Map (K-Map)  
- Quine McCluskey Method  
- **Example Verilog:**
```verilog
assign y = a ? (b ? c : (c ? a : 0)) : (!c);
```
  - Optimized Expression:
    
     `y = a ⊕ c`

  ### Yosys Optimization Commands:
  
  - The command to perform logic optimization in Yosys is opt_clean.
    ```
       opt_clean
    ```
  - Additionally, for a hierarchical design involving multiple sub-modules, the design must be flattened by running the flatten command before executing the opt_clean command.
   ```
    flatten
   ```
  ### Full Flow:

  ```
  synth -top <module_name>
  opt_clean -purge
```
- `opt_clean` → removes unused wires and cells

- `-purge`    → also removes internal nets with public names
 ---

## 🔹 Sequential Logic Optimization

### Basic Techniques:
 
#### 1.Sequential Constant Propagation:

- Propagates known constant values through flip-flops during synthesis.

 **Advanced Techniques:**
 
 #### 1. State Optimization

- Reduces the number of FSM states → optimizing area and transitions.

 #### 2. Retiming

Moves registers across logic boundaries to balance delay and improve timing.

 #### 3. Sequential Logic Cloning

- Duplicates logic in floorplan-aware synthesis to meet timing and congestion goals.

**Outcome:**
- These optimization techniques ensure faster, smaller, and power-efficient circuits, ready for real-world chip design.

---
### **To get start through the optimization of design logic**

**Go through the step by step worflow**

#### 1. **locate the file** in your project directory. 

This is done using,
```bash
cd ~/sky130RTLDesignAndSynthesisWorkshop/verilog_files
```
#### 2. **list the optimization designs**

 By  Running :
 
 ```
 ls *opt*
```

#### 4. **list the sequential optimization designs**
 By  Running :
 
 ```
 ls *dff*
```

## Lab 1 :

to see the verilog logic of Lab1.

Run:
```
gedit opt_check.v
```

![opt_check1](https://github.com/MOHANAPRIYANP16/Week-1-VSD-RISC-V-Tapeout-Program-/blob/main/Day3/Images/opt_check.v.png)

#### Explanation:

- assign y = a ? b : 0; means:
- If a is true, y is assigned the value of b.
- If a is false, y is 0.

#### 5: lanch yosys:
```
yosys
```
#### 6. **Run this command inside yosys:**
```
read_liberty -lib ~/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog opt_check.v
synth -top opt_check
opt_clean -purge
abc -liberty ~/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```

![opt_check1_dotfile](https://github.com/MOHANAPRIYANP16/Week-1-VSD-RISC-V-Tapeout-Program-/blob/main/Day3/Images/opt_check_dot_file.png)

Statistics:

![opt_check1statistics](https://github.com/MOHANAPRIYANP16/Week-1-VSD-RISC-V-Tapeout-Program-/blob/main/Day3/Images/statisticsopt1.png)
After view the Dot file :
run:
```
exit
# to exit form yosys
```
## Lab2:

#### 1.to see the verilog logic of Lab2.

Run:
```
gedit opt_check2.v
```

![opt_check1](https://github.com/MOHANAPRIYANP16/Week-1-VSD-RISC-V-Tapeout-Program-/blob/main/Day3/Images/opt_check2.v.png)

**Code Analysis**:

Acts as a multiplexer:
- y = 1 if a is true.
- y = b if a is false.

#### 2: lanch yosys:
```
yosys
```
#### 3. **Run this command inside yosys:**
```
read_liberty -lib ~/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog opt_check2.v
synth -top opt_check2
opt_clean -purge
abc -liberty ~/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```

see something like :

![opt_check1_dotfile](https://github.com/MOHANAPRIYANP16/Week-1-VSD-RISC-V-Tapeout-Program-/blob/main/Day3/Images/opt_check2or_dot_file.png)

Statistics:

![opt_check1statistics](https://github.com/MOHANAPRIYANP16/Week-1-VSD-RISC-V-Tapeout-Program-/blob/main/Day3/Images/opt2satistics.png)

After view the Dot file :

run:
```
exit
# to exit form yosys
```




## Summary



