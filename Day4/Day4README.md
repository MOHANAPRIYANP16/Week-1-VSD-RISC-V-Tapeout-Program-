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
   - [Blocking Statements](#31-blocking-statements)  
   - [Non-Blocking Statements](#32-non-blocking-statements)  
   - [Comparison Table](#33-comparison-table)  
4. [Labs](#4-labs)  
5. [Summary](#5-summary)  

---

## 1. Gate-Level Simulation (GLS)  

**Gate-Level Simulation (GLS)** is an essential verification step in the VLSI flow where the synthesized gate-level netlist is simulated to ensure:  

- Functional accuracy  
- Timing correctness  
- Power estimation  
- Test features (e.g., scan chains for DFT)  
 
  ![gls](<gls image.png>)

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

