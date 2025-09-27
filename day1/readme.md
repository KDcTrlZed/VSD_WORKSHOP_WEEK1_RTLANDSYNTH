# RTL Workshop Day 1: Verilog Design & Synthesis

A hands-on introduction to digital design using Verilog, open-source simulation with Icarus Verilog, and logic synthesis with Yosys.

## 📋 Prerequisites

- Linux environment (Ubuntu recommended)
- Basic understanding of digital logic

## 🛠️ Tool Installation

```bash
# Install required tools
sudo apt install iverilog gtkwave
```

## 🚀 Quick Start

### 1. Clone Workshop Files
```bash
git clone https://github.com/kunalg123/sky130RTLDesignAndSynthesisWorkshop.git
cd sky130RTLDesignAndSynthesisWorkshop/verilog_files
```

### 2. Simulate a 2:1 Multiplexer
```bash
# Compile design and testbench
iverilog good_mux.v tb_good_mux.v

# Run simulation
./a.out

# View waveforms
gtkwave tb_good_mux.vcd
```

<img width="1920" height="1080" alt="Image" src="https://github.com/user-attachments/assets/1da20d59-3e23-4a93-b41a-5d60b0191df7" />

*GTKWave simulation results showing multiplexer behavior*

### 3. Synthesize with Yosys
```bash
yosys
read_liberty -lib <path_to_sky130_lib_file>
read_verilog good_mux.v
synth -top good_mux
abc -liberty <path_to_sky130_lib_file>
show
```
![Image](https://github.com/user-attachments/assets/cfe5f8e1-d5c0-4a82-92f2-da04b0edee0d)
*Yosys-generated gate-level schematic of the 2:1 multiplexer*

## 📁 Key Files

- **`good_mux.v`** - 2:1 Multiplexer Verilog code
- **`tb_good_mux.v`** - Testbench for verification
- **`*.lib`** - Standard cell library for synthesis

## 🔍 What You'll Learn

- **Simulation Flow**: Design → Testbench → Simulation → Waveform Analysis
- **Verilog Basics**: Writing synthesizable RTL code
- **Synthesis Process**: RTL → Gate-level netlist using Yosys
- **Gate Libraries**: Understanding different cell variants (speed, power, area)

## 📊 Example: 2:1 Multiplexer

```verilog
module good_mux (input i0, input i1, input sel, output reg y);
always @ (*) begin
    if(sel)
        y <= i1;
    else 
        y <= i0;
end
endmodule
```

**Logic**: Selects `i1` when `sel=1`, otherwise selects `i0`

## 🔧 Key Commands

| Tool | Purpose | Command |
|------|---------|---------|
| **iverilog** | Verilog compilation | `iverilog design.v testbench.v` |
| **gtkwave** | Waveform viewer | `gtkwave file.vcd` |
| **yosys** | Logic synthesis | `synth -top module_name` |

## 📈 Workflow Overview

```
Verilog RTL → Simulation → Verification → Synthesis → Gate-level Netlist
```

## 💡 Key Takeaways

- Use **iverilog** for simulation and verification
- **GTKWave** provides excellent waveform visualization
- **Yosys** converts RTL to optimized gate-level circuits
- Gate libraries offer multiple cell variants for different optimization goals


*This README covers Day 1 fundamentals. Continue with Day 2 for advanced topics!*
