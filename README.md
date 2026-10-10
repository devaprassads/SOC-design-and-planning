# SoC Design and Physical Design Using Sky130

This project covers the SoC design and physical design flow of the PicoRV32A processor using Verilog and the SkyWater SKY130 platform. It includes source files, results, reports, and screenshots from different stages.

## 1. Synthesis

Synthesis converts Verilog RTL into a gate-level netlist using standard cells.

- [Synthesis](./synthesis/README.md) — Synthesis flow and results.
- [PicoRV32A RTL](./src/picorv32a.v) — Verilog source code of the processor.
- [OpenLane Configuration](./src/config.tcl) — Configuration for the design flow.
- [Timing Constraints](./src/my_base.sdc) — Timing constraints used in the design.
- [PicoRV32A SDC](./src/picorv32a.sdc) — Additional timing constraints.
- [Typical Cell Library](./src/sky130_fd_sc_hd__typical.lib) — Standard-cell library for typical conditions.
- [Fast Cell Library](./src/sky130_fd_sc_hd__fast.lib) — Standard-cell library for fast conditions.
- [Slow Cell Library](./src/sky130_fd_sc_hd__slow.lib) — Standard-cell library for slow conditions.
- [Synthesis Results](./results/synthesis/) — Generated gate-level netlists.
- [Synthesis Reports](./reports/synthesis/) — Timing analysis reports.

## 2. Floorplanning and Placement

Floorplanning defines the core area, while placement assigns locations to standard cells.

- [Floor Planning and Placement](./Floor%20planning%20and%20Placement/README.md) — Floorplanning and placement flow.
- [Floorplan Results](./results/floorplan/) — Floorplan output files.
- [Placement Results](./results/placement/) — Results after standard-cell placement.
- [Core Area Report](./reports/floorplan/3-verilog2def.core_area.rpt) — Report showing the core area.

## 3. CMOS Fabrication and Custom Cell

- [CMOS Fabrication](./CMOS%20Fabrication/README.md) — Notes and images about CMOS fabrication.
- [Custom Cell](./Custom%20Cell/README.md) — Custom inverter design and related files.
- [SPICE Netlist](./Custom%20Cell/files/sky130_inv.spice) — Netlist for inverter simulation.
- [Inverter Layout](./Custom%20Cell/files/sky130_vsdinv.mag) — Layout file of the custom inverter.
- [Custom Cell LEF](./Custom%20Cell/files/sky130_vsdinv.lef) — Physical information for the custom cell.
- [LEF in Source Folder](./src/sky130_vsdinv.lef) — Custom cell LEF file included with the design sources.

## 4. CTS and Timing

Clock Tree Synthesis distributes the clock signal to sequential cells. Timing analysis checks whether the design meets its timing requirements.

- [CTS Timing](./CTS%20Timing/README.md) — Notes on clock timing and timing analysis.
- [Clock Tree Synthesis](./Clock%20Tree%20synthesis/README.md) — Notes and screenshots from the CTS stage.
- [CTS Results](./results/cts/) — Output files generated after CTS.
- [Timing Reports](./reports/synthesis/) — Reports containing timing information.

## 5. Routing and Sign-off Checks

Routing connects the placed cells through metal wires.

- [Routing](./Routing/README.md) — Routing flow and screenshots.
- [Routing Results](./results/routing/) — Routed design files and extracted parasitics.
- [Routing Report](./reports/routing/18-tritonRoute.klayout.xml) — Routing report data.
- [Full-Flow Screenshots](./full%20flow/) — Screenshots from different stages of the design flow.

## What I Learned

- RTL synthesis and gate-level netlist generation.
- The role of standard-cell libraries and timing constraints.
- Floorplanning and standard-cell placement.
- CMOS fabrication and custom inverter design.
- Clock Tree Synthesis and timing analysis.
- Routing and examination of physical design results.

## License

See the [LICENSE](./LICENSE) file for license information.
