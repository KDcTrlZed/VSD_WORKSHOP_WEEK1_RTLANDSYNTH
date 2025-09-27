# RTL Workshop Day 4: GLS, Blocking vs Non-Blocking, and Synthesis Mismatches

Master gate-level simulation, proper Verilog coding practices, and troubleshoot synthesis-simulation mismatches for robust digital design verification.

## 📋 Overview

This workshop covers critical verification concepts:
- **Gate-Level Simulation (GLS)**: Post-synthesis netlist validation
- **Blocking vs Non-Blocking**: Proper assignment usage in Verilog
- **Synthesis Mismatches**: Identifying and resolving design issues

## 🔧 Prerequisites

- Completed Day 1-3 workshops
- Understanding of RTL simulation and synthesis
- Familiarity with Verilog coding

## ⚙️ Gate-Level Simulation (GLS)

### What is GLS?

Gate-Level Simulation validates the synthesized netlist by simulating with actual gate primitives instead of RTL behavioral code.

### 🎯 Why Perform GLS?

- ✅ **Synthesis Validation**: Verify RTL-to-gate translation accuracy
- ✅ **Timing Verification**: Check setup/hold violations with real delays
- ✅ **Testability Confirmation**: Validate scan chains and DFT features
- ✅ **Power Estimation**: Analyze switching activity at gate level

### 📊 Types of GLS

| Type | Description | Use Case |
|------|-------------|----------|
| **Functional GLS** | Logic-only with zero/unit delays | Basic functionality check |
| **Timing GLS** | Uses SDF timing annotation | Real-world timing verification |

### 🔄 GLS Workflow

```bash
# Standard GLS command structure
iverilog primitives.v technology_lib.v netlist.v testbench.v
./a.out
gtkwave simulation.vcd
```

## ⚠️ Synthesis-Simulation Mismatch

Common causes of mismatches between RTL simulation and gate-level behavior:

### 🚫 Root Causes
- **Non-synthesizable constructs**: Delays, initial blocks
- **Incomplete sensitivity lists**: Missing input signals
- **Ambiguous coding**: Missing else clauses
- **Tool interpretation differences**: Synthesis vs simulation tools

### ✅ Prevention Strategies
- Write **synthesizable, unambiguous RTL**
- Use **complete sensitivity lists** (`always @(*)`)
- Follow **coding guidelines** consistently
- Perform **regular GLS checks**

## 🔄 Blocking vs Non-Blocking Assignments

### Blocking Assignments (`=`)

```verilog
always @(*) begin
    y = a & b;  // Immediate execution
end
```

**Characteristics:**
- **Execution**: Sequential, immediate
- **Use Case**: Combinational logic
- **Timing**: Updates instantly in code order

### Non-Blocking Assignments (`<=`)

```verilog
always @(posedge clk) begin
    q <= d;  // Scheduled execution
end
```

**Characteristics:**
- **Execution**: Concurrent, end-of-timestep
- **Use Case**: Sequential logic (flip-flops)
- **Timing**: Updates applied after time step

### 📊 Comparison Table

| Aspect | Blocking (`=`) | Non-Blocking (`<=`) |
|--------|----------------|---------------------|
| **Operator** | `=` | `<=` |
| **Execution** | Sequential/Immediate | Concurrent/Scheduled |
| **Logic Type** | Combinational | Sequential |
| **Infers** | Gates/Logic | Flip-flops/Registers |
| **Use in** | `always @(*)` | `always @(posedge clk)` |

## 🧪 Laboratory Exercises

### Lab 1: Ternary Operator MUX

**RTL Design:**
```verilog
module ternary_operator_mux (input i0, input i1, input sel, output y);
    assign y = sel ? i1 : i0;
endmodule
```

**Function**: 2:1 multiplexer using ternary operator
<img width="1052" height="699" alt="Image" src="https://github.com/user-attachments/assets/bfdf1003-7260-4831-980c-744b71861a13" />
*RTL simulation showing multiplexer behavior*

### Lab 2: Synthesis with Yosys

**Synthesis Flow:**
```bash
yosys
read_liberty -lib <sky130_lib_path>
read_verilog ternary_operator_mux.v
synth -top ternary_operator_mux
abc -liberty <sky130_lib_path>
show
```

![Image](https://github.com/user-attachments/assets/93f6d5d0-9541-4a20-a933-0e25cf431ef4)
*Synthesized gate-level schematic of the multiplexer*

### Lab 3: Gate-Level Simulation

**GLS Command:**
```bash
iverilog primitives.v sky130_fd_sc_hd.v ternary_operator_mux.v testbench.v
./a.out
gtkwave simulation.vcd
```

<img width="1052" height="699" alt="Image" src="https://github.com/user-attachments/assets/2a337f65-d370-442a-aba2-300b64a9798e" />
*Gate-level simulation waveforms matching RTL behavior*

### Lab 4: Bad MUX (Common Pitfalls)

**Problematic Code:**
```verilog
module bad_mux (input i0, input i1, input sel, output reg y);
    always @ (sel) begin  // Incomplete sensitivity list!
        if (sel)
            y <= i1;      // Wrong assignment type!
        else 
            y <= i0;
    end
endmodule
```

**Issues:**
- ❌ **Incomplete sensitivity list**: Missing `i0`, `i1`
- ❌ **Wrong assignment**: Non-blocking in combinational logic

**Corrected Version:**
```verilog
always @ (*) begin        // Complete sensitivity list
    if (sel)
        y = i1;          // Blocking assignment
    else
        y = i0;
end
```

<img width="1920" height="982" alt="Image" src="https://github.com/user-attachments/assets/af90d436-8971-4748-b3fe-ec9e2bbe3bfc" />
*Simulation showing incorrect behavior due to coding issues*

### Lab 5: GLS of Bad MUX

Demonstrates synthesis-simulation mismatch when poor coding practices are used.

<img width="1920" height="982" alt="Image" src="https://github.com/user-attachments/assets/af90d436-8971-4748-b3fe-ec9e2bbe3bfc" />
*GLS results showing mismatch with RTL simulation*

### Lab 6: Blocking Assignment Caveat

**Problematic Code:**
```verilog
module blocking_caveat (input a, input b, input c, output reg d);
    reg x;
    always @ (*) begin
        d = x & c;    // Uses OLD value of x!
        x = a | b;    // Updates x AFTER d is computed
    end
endmodule
```

**Issue**: Order dependency causes incorrect logic

**Fixed Version:**
```verilog
always @ (*) begin
    x = a | b;    // Compute x first
    d = x & c;    // Then use new value of x
end
```

<img width="1920" height="982" alt="Image" src="https://github.com/user-attachments/assets/339f1298-17be-4826-8bc8-885fc56a2c76" />
*Demonstration of blocking assignment order dependency*

### Lab 7: Synthesis of Corrected Module

Shows proper synthesis results when blocking assignments are used correctly.

![Image](https://github.com/user-attachments/assets/6fba72d9-2916-4288-b8bf-67148b026c7e)
*Synthesized circuit with proper logic implementation*

## 🔑 Key Commands Reference

| Tool | Command | Purpose |
|------|---------|---------|
| **iverilog** | `iverilog primitives.v lib.v netlist.v tb.v` | GLS compilation |
| **GTKWave** | `gtkwave simulation.vcd` | Waveform analysis |
| **Yosys** | `synth -top <module>` | RTL synthesis |
| **Yosys** | `show` | Display gate-level schematic |

## 💡 Best Practices

### Coding Guidelines
- **Use `always @(*)`** for combinational logic
- **Use `always @(posedge clk)`** for sequential logic
- **Apply blocking assignments (`=`)** in combinational blocks
- **Apply non-blocking assignments (`<=`)** in sequential blocks
- **Write complete sensitivity lists** or use `always @(*)`

### Verification Strategy
- **Simulate RTL first** to verify functionality
- **Synthesize and check** for warnings/errors
- **Run GLS** to validate synthesis accuracy
- **Compare RTL and GLS** waveforms for mismatches
- **Fix coding issues** before proceeding to layout

### Debugging Synthesis Mismatches
1. **Check synthesis warnings** for optimization messages
2. **Review sensitivity lists** for completeness
3. **Verify assignment types** (blocking vs non-blocking)
4. **Examine tool reports** for inference issues
5. **Run functional simulation** on both RTL and netlist

## ⚠️ Common Pitfalls to Avoid

- **Incomplete sensitivity lists** in combinational logic
- **Mixed blocking/non-blocking** in same always block
- **Using non-blocking** for combinational logic
- **Using blocking** for sequential logic
- **Order dependency** in blocking assignments
- **Missing else clauses** in conditional statements

## 📈 Next Steps

- Study advanced verification methodologies (UVM)
- Learn formal verification techniques
- Explore timing analysis and constraints
- Practice with complex FSM designs
- Study power-aware verification flows

---

*Day 4 emphasizes the critical importance of proper verification techniques and coding practices for robust digital design.*
