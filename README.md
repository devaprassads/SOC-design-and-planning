# SoC Design and Physical Design Using Sky130

## About the Project

This project follows a digital design from Verilog RTL toward a physical chip layout. I worked with the PicoRV32A processor design and the SkyWater SKY130 platform. The repository contains notes, screenshots, configuration files, reports, and results from different stages.

## Documentation

1. **[Synthesis](./1_Synthesis.md)** — RTL, standard-cell libraries, synthesis, netlists, and synthesis reports.
2. **[Floorplanning and Placement](./2_Floorplan_Placement.md)** — core area, utilization, cell placement, and their effect on routing and timing.
3. **[Custom Cell Design](./3_Custom_Cell.md)** — CMOS inverter theory, layout, DRC, extraction, SPICE simulation, and cell files.
4. **[CTS and Timing](./4_CTS_Timing.md)** — clock distribution, latency, skew, setup and hold checks, and slack.
5. **[Routing and Sign-off Checks](./5_Routing_Signoff.md)** — physical interconnect, parasitic extraction, routing checks, and post-route timing.

Custom-cell design normally comes before synthesis if the cell is intended to be used in the synthesis library. CMOS fabrication concepts are included as background for understanding the devices and layout.

## Project Sources and Existing Stage Folders

- [CMOS Fabrication notes](./CMOS%20Fabrication/)
- [Custom Cell work and files](./Custom%20Cell/)
- [Synthesis work](./synthesis/)
- [Floorplanning and Placement work](./Floor%20planning%20and%20Placement/)
- [CTS Timing material](./CTS%20Timing/)
- [Clock Tree Synthesis work](./Clock%20Tree%20synthesis/)
- [Routing work](./Routing/)
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

Through this project, I explored how RTL is converted into logic cells, how cells are arranged and connected during physical design, how the clock is distributed, and how parasitic effects and timing reports help evaluate an implementation. The documentation links the theory to the project folders, screenshots, results, and reports.
