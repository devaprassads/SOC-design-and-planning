# 1. Synthesis

## Purpose

Synthesis converts a digital design written in Verilog RTL into a gate-level netlist. The netlist describes the logic cells used and how they are connected. It is the bridge between the RTL description and physical design.

## Theory

### RTL and Verilog

RTL (Register-Transfer Level) describes the data operations and registers in a digital design. In this project, the design is based on the PicoRV32A processor and its Verilog source is in [src/picorv32a.v](./src/picorv32a.v).

### What synthesis does

A synthesis flow typically:
1. Reads the RTL and required libraries.
2. Checks and elaborates the design.
3. Optimizes the logic.
4. Maps the logic to cells available in the selected technology library.
5. Writes a gate-level netlist and reports.

### Standard-cell library

A standard-cell library contains pre-designed cells such as inverters, logic gates, buffers, and flip-flops. Synthesis chooses cells from the library to implement the RTL. Cell area and timing characteristics affect the mapping decisions.

### Timing constraints

Timing constraints tell the tools the timing targets they should consider. The project includes [src/my_base.sdc](./src/my_base.sdc). The actual results depend on the constraints, libraries, and tool settings used.

## Project Files and Results

- [Synthesis folder](./synthesis/)
- [Synthesis results](./results/synthesis/)
- [Synthesis reports](./reports/synthesis/)
- [PicoRV32A RTL](./src/picorv32a.v)
- [Configuration](./src/config.tcl)
- [Timing constraints](./src/my_base.sdc)

## What to Check in the Reports

- Whether synthesis completed successfully.
- The cells used and the resulting cell area.
- Timing estimates and any reported violations.
- Whether the generated netlist is available for the next physical-design stages.

## Output

The main output is a gate-level netlist and synthesis reports. Physical design uses this netlist as its starting point.
