# RTL Workshop Day 2: Timing Libraries & Advanced Synthesis

Deep dive into timing libraries, synthesis approaches, and efficient flip-flop coding techniques using SKY130 PDK.

## 📋 Overview

This workshop covers:
- **Timing Libraries**: Understanding SKY130 PDK `.lib` files
- **Synthesis Strategies**: Hierarchical vs Flattened approaches  
- **Sequential Logic**: Efficient flip-flop coding styles

## 🔧 Prerequisites

- Completed Day 1 workshop
- Basic knowledge of sequential logic
- SKY130 PDK library files

## 📚 SKY130 Timing Library

### Library Naming Convention: `sky130_fd_sc_hd__tt_025C_1v80.lib`

| Component | Meaning |
|-----------|---------|
| **tt** | Typical process corner |
| **025C** | Temperature: 25°C |
| **1v80** | Voltage: 1.8V |

### Exploring the Library
```bash
# Install text editor
sudo apt install gedit

# Open library file
gedit sky130_fd_sc_hd__tt_025C_1v80.lib
```

<img width="952" height="799" alt="Image" src="https://github.com/user-attachments/assets/1df9670c-7633-4d77-9e8f-8853755aee0c" />
*SKY130 timing library showing cell definitions and characteristics*

## ⚡ Synthesis Approaches

### 🏗️ Hierarchical Synthesis
Preserves module hierarchy for better organization and debugging.

**Advantages:**
- ✅ Faster synthesis for large designs
- ✅ Better debugging capabilities
- ✅ Modular approach

**Disadvantages:**
- ❌ Limited cross-module optimization
- ❌ Complex reporting setup



### 🔄 Flattened Synthesis
Collapses all modules into a single netlist for maximum optimization.

```bash
# Yosys flatten command
flatten
```

**Advantages:**
- ✅ Aggressive whole-design optimization
- ✅ Unified netlist output

**Disadvantages:**
- ❌ Longer runtime for large designs
- ❌ Loss of hierarchy complicates debugging


### 📊 Comparison Table

| Aspect | Hierarchical | Flattened |
|--------|-------------|-----------|
| **Hierarchy** | Preserved | Collapsed |
| **Optimization** | Module-level | Whole-design |
| **Runtime** | Faster | Slower |
| **Debugging** | Easier | Harder |
| **Use Case** | Analysis, reporting | Maximum optimization |

## 🔄 Flip-Flop Coding Styles

### 1. Asynchronous Reset D Flip-Flop
```verilog
module dff_asyncres (input clk, input async_reset, input d, output reg q);
  always @ (posedge clk, posedge async_reset)
    if (async_reset)
      q <= 1'b0;
    else
      q <= d;
endmodule
```
**Key Feature**: Reset overrides clock immediately

### 2. Asynchronous Set D Flip-Flop
```verilog
module dff_async_set (input clk, input async_set, input d, output reg q);
  always @ (posedge clk, posedge async_set)
    if (async_set)
      q <= 1'b1;
    else
      q <= d;
endmodule
```
**Key Feature**: Set overrides clock immediately

### 3. Synchronous Reset D Flip-Flop
```verilog
module dff_syncres (input clk, input sync_reset, input d, output reg q);
  always @ (posedge clk)
    if (sync_reset)
      q <= 1'b0;
    else
      q <= d;
endmodule
```
**Key Feature**: Reset synchronized with clock edge

## 🚀 Simulation & Synthesis Workflow

### Simulation with Icarus Verilog
```bash
# Compile design and testbench
iverilog dff_asyncres.v tb_dff_asyncres.v

# Run simulation
./a.out

# View waveforms
gtkwave tb_dff_asyncres.vcd
```

<img width="1920" height="982" alt="Image" src="https://github.com/user-attachments/assets/154b8653-6400-4d76-a2e8-b452e7ec7540" />
*GTKWave showing flip-flop behavior with async reset*

### Synthesis with Yosys
```bash
yosys
read_liberty -lib <path_to_sky130_lib>
read_verilog <flip_flop_design.v>
synth -top <module_name>
dfflibmap -liberty <path_to_sky130_lib>
abc -liberty <path_to_sky130_lib>
show
```

![Image](https://github.com/user-attachments/assets/4c173843-36ed-42c0-935c-ea0ce1374a68)
*Yosys-generated flip-flop schematic with reset logic*

## 🔑 Key Commands Reference

| Tool | Command | Purpose |
|------|---------|---------|
| **Yosys** | `flatten` | Collapse hierarchy |
| **Yosys** | `dfflibmap` | Map flip-flops to library cells |
| **Yosys** | `hierarchy -top <module>` | Set design hierarchy |
| **Yosys** | `show` | Display gate-level schematic |

## 💡 Best Practices

### Flip-Flop Design Guidelines
- Use **asynchronous reset** for global system reset
- Use **synchronous reset** for local control logic
- Always include reset in sensitivity list for async designs
- Consider reset polarity consistency across design

### Synthesis Strategy Selection
- **Choose Hierarchical** for: Large designs, debugging, incremental flows
- **Choose Flattened** for: Small designs, maximum optimization, timing closure

## 📈 Next Steps

- Explore advanced sequential elements (latches, counters)
- Study timing constraints and clock domain crossing
- Learn about power optimization techniques
- Practice with complex FSM designs

---

*Day 2 builds upon fundamental concepts with advanced synthesis techniques and sequential design patterns.*
