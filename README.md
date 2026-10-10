# SoC Design and Physical Design Using Sky130

## About the Project

This project follows the PicoRV32A processor design from Verilog RTL toward physical layout using the SkyWater SKY130 platform. The repository contains the source files, configuration, notes, screenshots, reports, and results from the design flow.

## Project Files and Stages

- [CMOS Fabrication](./CMOS%20Fabrication/)
- [Custom Cell](./Custom%20Cell/)
- [Synthesis](./synthesis/)
- [Floor Planning and Placement](./Floor%20planning%20and%20Placement/)
- [CTS Timing](./CTS%20Timing/)
- [Clock Tree Synthesis](./Clock%20Tree%20synthesis/)
- [Routing](./Routing/)
- [RTL and configuration files](./src/)
- [PicoRV32A Verilog source](./src/picorv32a.v)
- [OpenLane configuration](./src/config.tcl)
- [Timing constraints](./src/my_base.sdc)

## Results and Reports

- [Synthesis results](./results/synthesis/)
- [Floorplan results](./results/floorplan/)
- [Placement results](./results/placement/)
- [CTS results](./results/cts/)
- [Routing results](./results/routing/)
- [All results](./results/)
- [Synthesis reports](./reports/synthesis/)
- [Floorplan reports](./reports/floorplan/)
- [Routing reports](./reports/routing/)
- [Full-flow screenshots](./full%20flow/)

## What I Learned

I explored how RTL is converted into logic cells, how cells are placed and connected, how the clock is distributed, and how timing reports and parasitic effects are checked during physical design.
