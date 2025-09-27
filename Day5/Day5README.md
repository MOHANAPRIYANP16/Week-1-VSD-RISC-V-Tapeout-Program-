# Day 5 - Optimization in Synthesis  

Welcome to **Day 5 of the RTL Workshop!**  
In this session, we focus on **Verilog conditional constructs (if-else, case, and combined forms)**,  
their impact on **synthesis optimization**, and the issues of **inferred latches**.  
You’ll also run lab experiments to understand these concepts in practice.  

---

## 📑 Table of Contents  

1. [If-Else Statements](#-1-if-else-statements)  
2. [Case Statements](#-2-case-statements)  
3. [If-Else with Case](#-3-if-else-with-case)  
4. [If-Else vs Case](#-if-else-vs-case--comparison)  
5. [Inferred Latches](#inferred-latches-in-verilog)  
6. [Labs](#lab) 
7. [summary](#summary) 


##  Verilog Conditional Constructs
Two main decision-making constructs in Verilog: **If-Else** and **Case**.

---

### 🔹 1. If-Else Statements
Conditional branching based on Boolean expressions.

  **Syntax**
```verilog
always @(*) begin
    if (condition)
        statement1;
    else
        statement2;
end
```
Example

```verilog

always @(*) begin
    if (sel)
        y = a;
    else
        y = b;
end
```
🔸 Nested If Example:

```verilog
always @(*) begin
    if (sel1) begin
        if (sel2)
            y = a;
        else
            y = b;
    end else begin
        y = c;
    end
end
```
---

### 🔹 2. Case Statements:

- Multi-way branching, useful for MUX or state machines.

**Syntax**
```verilog 
always @(*) begin
    case(expression)
        value1: statement1;
        value2: statement2;
        default: statementN;
    endcase
end
```
Example

```verilog
always @(*) begin
    case(sel)
        2'b00: y = a;
        2'b01: y = b;
        2'b10: y = c;
        default: y = 0;
    endcase
end
```
### 🔹 3. If-Else with Case

- A combination of conditional and multi-way branching.

```verilog
always @(*) begin
    if (enable) begin
        case(sel)
            2'b00: y = a;
            2'b01: y = b;
            2'b10: y = c;
            default: y = 0;
        endcase
    end else begin
        y = 0;
    end
end
```
> [!NOTE]
> Outer if controls enabling condition.
> Inner case selects among multiple inputs.

---

## 📊 If-Else vs Case – Comparison

| Feature            | If-Else                        | Case                                  |
|--------------------|--------------------------------|---------------------------------------|
| **Purpose**        | Binary / nested conditions     | Multi-way discrete selection           |
| **Readability**    | Harder for many conditions     | Cleaner with multiple options          |
| **Default Handling** | Optional (may infer latches) | Must add `default` to avoid latches    |
| **Evaluation**     | Checked sequentially           | Expression matched directly            |

---


##  Inferred Latches in Verilog  

In Verilog, **inferred latches** happen when a combinational block does **not assign a value to an output in all possible paths**. This causes the hardware to "remember" the previous value of the signal.  

Common causes include:  
- Missing `else` in `if-else` statements  
- Missing `default` in `case` statements  
- Partial assignments to vectors or multiple outputs  

These latches can lead to **simulation vs synthesis mismatches**, timing problems, and harder debugging.  

**To avoid inferred latches:**  
- Always assign outputs in every possible branch  
- Use `else` in `if-else` statements  
- Include `default` in `case` statements  

**Example:**  
```verilog
always @(*) begin
    if (enable)
        y = a; // else missing → latch inferred
end
```
**No Latch:**

```verilog 
always @(*) begin
    if (enable)
        y = a;
    else
        y = 0; // all paths covered
end
```

##  Lab 

###  Incomplete If (`incomp_if.v`)

![if1](https://github.com/MOHANAPRIYANP16/Week-1-VSD-RISC-V-Tapeout-Program-/blob/main/Day5/Images/incom_if_verilog.png)

**Observation:**

- If i0 = 1 → y = i1

- If i0 = 0 → y retains previous value → Latch inferred

**simulation:**

![if1](https://github.com/MOHANAPRIYANP16/Week-1-VSD-RISC-V-Tapeout-Program-/blob/main/Day5/Images/incomp_if_gtkwave.png)

**synthesis:**

> [!Note]
> follow the [Day4](https://github.com/MOHANAPRIYANP16/Week-1-VSD-RISC-V-Tapeout-Program-/blob/main/Day4/Day4README.md#labs) steps for synthesis

Dot File

![if1](https://github.com/MOHANAPRIYANP16/Week-1-VSD-RISC-V-Tapeout-Program-/blob/main/Day5/Images/incomp_if_dot_file.png)

---

### Incomplete If-Else (`incomp_if2.v`)

![if2](https://github.com/MOHANAPRIYANP16/Week-1-VSD-RISC-V-Tapeout-Program-/blob/main/Day5/Images/incomp_if2verilog.png)

**Observation:**

- If i0 = 1 → y = i1

- Else if i2 = 1 → y = i3

- If both false → y retains old value → Latch inferred

**simulation:**

![if2](https://github.com/MOHANAPRIYANP16/Week-1-VSD-RISC-V-Tapeout-Program-/blob/main/Day5/Images/incomp_if2_gtkwave.png)

**synthesis:**

Dot File:

![if2](https://github.com/MOHANAPRIYANP16/Week-1-VSD-RISC-V-Tapeout-Program-/blob/main/Day5/Images/ifcomp_if2_dot_file.png)


### Incomplete Case (`incomp_case.v`)

![incomp1](https://github.com/MOHANAPRIYANP16/Week-1-VSD-RISC-V-Tapeout-Program-/blob/main/Day5/Images/incomp_caseverilog.png)

**Observation:**

- Works fine for sel = 00 or 01

- For sel = 10 or 11 → y holds old value → Latch inferred

**simulation:**

![incomp1](https://github.com/MOHANAPRIYANP16/Week-1-VSD-RISC-V-Tapeout-Program-/blob/main/Day5/Images/incomp_case_gtkwave.png)

**synthesis:**

Dot File:

![alt text](https://github.com/MOHANAPRIYANP16/Week-1-VSD-RISC-V-Tapeout-Program-/blob/main/Day5/Images/incomp_case_dot_file.png)


### Bad Case Example (`bad_case.v`)

![badcase](https://github.com/MOHANAPRIYANP16/Week-1-VSD-RISC-V-Tapeout-Program-/blob/main/Day5/Images/bad_caseverilog.png)

**Observation:**

- Missing explicit 2'b11

- RTL simulation → latch may appear

- GLS simulation → latch corrected

**simulation:**

![badcase](https://github.com/MOHANAPRIYANP16/Week-1-VSD-RISC-V-Tapeout-Program-/blob/main/Day5/Images/bad_case_gtkwave.png)

**synthesis:**

Dot File:

![badcase](https://github.com/MOHANAPRIYANP16/Week-1-VSD-RISC-V-Tapeout-Program-/blob/main/Day5/Images/bad_case_dot_file.png)

## GLS of Bad case :

Gate-Level Simulation is performed using the synthesized netlist (bad_case_net.v).

> [!Note]
> follow the [Day4](https://github.com/MOHANAPRIYANP16/Week-1-VSD-RISC-V-Tapeout-Program-/blob/main/Day4/Day4README.md#labs) steps for Gate level synthesis

#### GLS waveform :

![gls](https://github.com/MOHANAPRIYANP16/Week-1-VSD-RISC-V-Tapeout-Program-/blob/main/Day5/Images/bad_case_gls.png)

- For sel = 2'b00, y = i0;
- For sel = 2'b01, y = i1;
- For sel = 2'b10, y = i2;
- sel = 2'b11 is commented out, so for this value y is not assigned, causing a latch to be inferred in RTL Simulation.
- But this inferred latch is corrected in simulation as seen in the GLS Waveform


## Verilog Looping Constructs: `for` Loop & `generate for` Loop

---

### 1. `for` Loop

- **Purpose:** Executes a block of statements multiple times inside an `always` block.  
- **Use Case:** Iterative assignments in **combinational or sequential logic**.

**Syntax:**
```verilog
always @(*) begin
    integer i;
    for (i = 0; i < N; i = i + 1) begin
        array[i] = input_signal[i];
    end
end
```

- **Observation:**  
  - Executes **during simulation**, not synthesis-time replication.  
  - Loop index must be an **integer**.  
  - Useful for operations like summing arrays, shifting, or copying signals.

---

### 2. `generate for` Loop

- **Purpose:** Used at **compile/synthesis time** to generate multiple instances of modules or repeated RTL structures.
- **Usage:** Creates hardware replication, like multiple adders, flip-flops, or gates.

**Syntax:**
```verilog
genvar i;
generate
    for (i = 0; i < N; i = i + 1) begin : loop_label
        my_module u_inst (
            .in(signal_in[i]),
            .out(signal_out[i])
        );
    end
endgenerate
```

- **Observation:**  
  - Executes **at synthesis/compile time**, producing multiple hardware instances.  
  - `genvar` is required for the loop index.  
  - Commonly used for arrays of registers, multiplexers, or replicated modules like **Ripple Carry Adders (RCA)**.

---

## Labs on `for` loop

## `mux_generate.v`

**Verilog Code**

![mux_gen](https://github.com/MOHANAPRIYANP16/Week-1-VSD-RISC-V-Tapeout-Program-/blob/main/Day5/Images/mux_generate_verilog.png)

**Simulation**

_Workflow_ :

![workflowgen](https://github.com/MOHANAPRIYANP16/Week-1-VSD-RISC-V-Tapeout-Program-/blob/main/Day5/Images/mux_genrate_workflow.png)

_Waveform_ :

![mux_gen](https://github.com/MOHANAPRIYANP16/Week-1-VSD-RISC-V-Tapeout-Program-/blob/main/Day5/Images/mux_generate_gtkwave2.png)


- Implements a 4-to-1 multiplexer using a `for` loop.
- Inputs `i0`–`i3` are packed into a 4-bit wire `i_int` for indexing.
- Loop iterates over indices 0 to 3 inside an `always` block.
- Assigns `y = i_int[k]` when `k` matches the select signal `sel`.
- Only the selected input is passed to the output.
- Avoids multiple nested `if-else` or `case` statements, making code concise.

---


## `demux_generate.v`

**Verilog Code**

![verilog](https://github.com/MOHANAPRIYANP16/Week-1-VSD-RISC-V-Tapeout-Program-/blob/main/Day5/Images/mux_generate_gtkwave2.png)

**Simulation**

_Workflow_ :

![workflowdemux](https://github.com/MOHANAPRIYANP16/Week-1-VSD-RISC-V-Tapeout-Program-/blob/main/Day5/Images/demux_workflow.png)

_Waveform_ :

![demux](https://github.com/MOHANAPRIYANP16/Week-1-VSD-RISC-V-Tapeout-Program-/blob/main/Day5/Images/demux_gtkwave.png)

- Implements an 8-output demultiplexer using a `for` loop.  
- Outputs `o0`–`o7` are packed into an 8-bit register `y_int` for easy indexing.  
- `y_int` is initialized to 0, then only the bit matching `sel` is set to input `i`.  
- Routes the single input `i` to the selected output while others remain 0.  
- Loop executes sequentially during simulation; no latches are inferred.  
- Provides a concise alternative to multiple if-else or case statements.

---

## Labs on `generate for` loop

## 8-bit Ripple Carry Adder (RCA)

- An RCA is built by cascading multiple **full adders**.  
- Each full adder adds two input bits along with a carry-in, producing a sum and a carry-out.  
- In an 8-bit RCA, the carry-out of each stage is passed as the carry-in to the next stage.  
- The first adder takes an external carry-in (usually 0), and the final adder produces the overall carry-out.
- The `generate for` loop instantiates one full adder per bit, automatically chaining the carry-out of stage *k* to the carry-in of stage *k+1*.  
- This makes the code **shorter, scalable, and less error-prone**, especially for larger adders (e.g., 16-bit, 32-bit).  
- Only the loop index changes for each stage, while the wiring of sum and carry is handled systematically.  
- If you change the bit-width (say from 8 to 16), only the loop limit needs updating.

**Verilog Code**

RCA (`rca.v`): _8 bit Ripple Carry Adder_

![rca](https://github.com/MOHANAPRIYANP16/Week-1-VSD-RISC-V-Tapeout-Program-/blob/main/Day5/Images/rca_verilog.png)

FA (`fa.v`): _1 bit Full Adder_

![fa](https://github.com/MOHANAPRIYANP16/Week-1-VSD-RISC-V-Tapeout-Program-/blob/main/Day5/Images/fa.png)

**RTL Simulation**

_Workflow_ :

![workflow](https://github.com/MOHANAPRIYANP16/Week-1-VSD-RISC-V-Tapeout-Program-/blob/main/Day5/Images/rca_sim_workflow2.png)

_Waveform_ :

![gtkwave](https://github.com/MOHANAPRIYANP16/Week-1-VSD-RISC-V-Tapeout-Program-/blob/main/Day5/Images/rca_sim_gtkwave.png)


**Synthesis**

_Workflow_ :

![workflow](http://github.com/MOHANAPRIYANP16/Week-1-VSD-RISC-V-Tapeout-Program-/blob/main/Day5/Images/rca_workflow.png)

_Netlist Dot File_ :

![dot](https://github.com/MOHANAPRIYANP16/Week-1-VSD-RISC-V-Tapeout-Program-/blob/main/Day5/Images/rca_dot_file.png)

**Gate-Level Simulation**

_Workflow_ :

![workflow](https://github.com/MOHANAPRIYANP16/Week-1-VSD-RISC-V-Tapeout-Program-/blob/main/Day5/Images/gls_rca_workflow.png)

_Waveform_ :

![gls](https://github.com/MOHANAPRIYANP16/Week-1-VSD-RISC-V-Tapeout-Program-/blob/main/Day5/Images/gls_rca_gtkwave.png)

Thus, the *RTL Simulation*, *Synthesis*, and *Gate-level simulation* of the **8-bit Ripple Carry Adder** have been successfully completed and verified.

---

## Summary

- **If-Else & Case:** Control logic in Verilog; `if-else` handles conditional decisions, while `case` is better for multi-way selection. Missing branches in either can cause **inferred latches**, which hold old values unintentionally.  
- **Inferred Latches:** Occur when outputs are not assigned in all paths (incomplete `if`, `case`, or partial assignments). Always use `else` or `default` to avoid them.  
- **For Loops:** Used inside `always` blocks for simulation-time iteration, simplifying repeated assignments.  
- **Generate For Loops:** Create multiple hardware instances at synthesis time; useful for scalable designs like adders or multiplexers.  
- **Lab Highlights:**  
  - Incomplete `if`/`case` → inferred latches.  
  - `mux_generate` and `demux_generate` show how `for` loops reduce repetitive code.  
  - 8-bit RCA with `generate for` demonstrates scalable hardware replication.  

Overall, careful use of conditional constructs and loops helps write clean, latch-free, and scalable Verilog code.

---