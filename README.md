# SoC Design and Physical Design Using Sky130

## About the Project

This project follows the main steps used to take a digital design from Verilog code toward a physical chip layout. I worked with the PicoRV32A processor design and the SkyWater SKY130 platform. The repository contains notes, screenshots, configuration files, reports, and results from the different stages.

The sections below are arranged in **technical workflow order**, rather than the order of the folders in the repository.

## Overall Flow

1. Learn the CMOS fabrication process
2. Design and check a custom CMOS inverter
3. Prepare the RTL, configuration, and timing constraints
4. Synthesize the RTL into a gate-level netlist
5. Create the floorplan
6. Place the standard cells
7. Build the clock distribution network using CTS
8. Route the design and extract parasitic effects
9. Check setup and hold timing using Static Timing Analysis (STA)

## 1. CMOS Fabrication Basics

This section covers the basic steps involved in manufacturing CMOS devices, such as well formation, active regions, transistor formation, contacts, and metal layers. It provides background on how the structures used in integrated circuits are made.

- [Open CMOS Fabrication](./CMOS%20Fabrication/)
- [Read the section README](./CMOS%20Fabrication/README.md)

## 2. Custom Cell Design: CMOS Inverter

A CMOS inverter reverses its input logic value: a LOW input produces a HIGH output, and a HIGH input produces a LOW output. This section covers custom inverter work, including layout, design-rule checks, extraction, SPICE simulation, timing measurements, and cell files.

- [Open Custom Cell](./Custom%20Cell/)
- [Read the section README](./Custom%20Cell/README.md)
- [Custom-cell files](./Custom%20Cell/files/)

## 3. RTL, Configuration, and Timing Constraints

The processor design is described in Verilog. The configuration and timing-constraint files provide settings and timing requirements used by the implementation flow.

- [RTL and configuration files](./src/)
- [PicoRV32A Verilog source](./src/picorv32a.v)
- [OpenLane configuration](./src/config.tcl)
- [Timing constraints (SDC)](./src/my_base.sdc)

## 4. RTL Synthesis

Synthesis converts the Verilog RTL into a gate-level netlist by mapping the design to cells available in the selected technology. The resulting netlist is used by the physical-design stages.

- [Open Synthesis](./synthesis/)
- [Read the section README](./synthesis/README.md)
- [Synthesis results](./results/synthesis/)
- [Synthesis reports](./reports/synthesis/)

## 5. Floorplanning

Floorplanning sets up the main layout area and establishes the basic structure of the design before the standard cells are placed.

- [Open Floorplanning and Placement](./Floor%20planning%20and%20Placement/)
- [Read the section README](./Floor%20planning%20and%20Placement/README.md)
- [Floorplan results](./results/floorplan/)
- [Floorplan reports](./reports/floorplan/)

## 6. Standard-Cell Placement

Placement assigns physical locations to the standard cells inside the floorplan. Placement quality can affect wire length, congestion, area, and timing.

- [Placement results](./results/placement/)
- [Floorplanning and Placement section](./Floor%20planning%20and%20Placement/)

## 7. Clock-Tree Synthesis (CTS)

The clock signal must reach sequential cells across the design. CTS builds a clock distribution network, typically inserting clock buffers to help manage clock latency and skew.

- [Open Clock Tree Synthesis](./Clock%20Tree%20synthesis/)
- [Read the section README](./Clock%20Tree%20synthesis/README.md)
- [CTS results](./results/cts/)

## 8. Routing and Parasitic Extraction

Routing creates the physical metal connections between cells. The wires add resistance and capacitance, so parasitic extraction estimates these effects for more realistic timing analysis.

- [Open Routing](./Routing/)
- [Read the section README](./Routing/README.md)
- [Routing results](./results/routing/)
- [Routing reports](./reports/routing/)

## 9. Static Timing Analysis (STA)

STA checks whether signals can reach their destinations within the timing requirements. Setup checks that data arrives early enough; hold checks that data does not arrive too soon. Negative slack indicates a timing violation for the reported check. Timing results should be interpreted together with the implementation stage and constraints they belong to.

- [Open CTS Timing](./CTS%20Timing/)
- [Read the section README](./CTS%20Timing/README.md)
- [Synthesis reports](./reports/synthesis/)
- [Floorplan reports](./reports/floorplan/)
- [Routing reports](./reports/routing/)

## Full-Flow Screenshots and Outputs

- [Workflow screenshots](./full%20flow/)
- [All generated results](./results/)
- [All reports](./reports/)

## What I Learned

Through this project, I followed the main stages of a digital physical-design flow. I learned how CMOS devices and a custom inverter relate to digital logic, how RTL is synthesized into cells, and how those cells are arranged and connected during physical design. I also explored clock-tree synthesis, routing, parasitic effects, and timing reports.

This repository documents the stages and keeps the related files, screenshots, results, and reports together.
