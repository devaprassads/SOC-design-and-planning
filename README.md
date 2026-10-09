# SoC Design and Physical Design Using Sky130

## About the Project

This project follows the main steps used to take a digital design through synthesis and physical design. I worked with the PicoRV32A design and the SkyWater SKY130 platform. The repository contains notes, screenshots, configuration files, reports, and results from the different stages.

The work starts with learning about CMOS fabrication and creating a custom inverter cell. It then moves through synthesis, floorplanning, placement, clock-tree synthesis, routing, and timing checks.

## Project Flow

1. CMOS fabrication
2. Custom inverter design and checks
3. RTL synthesis
4. Floorplanning
5. Standard-cell placement
6. Clock-tree synthesis (CTS)
7. Routing and parasitic extraction
8. Static timing analysis

## Project Sections

### 1. CMOS Fabrication

This section describes the main steps in CMOS fabrication, including well formation, active regions, transistor formation, contacts, and metal layers.

- [Open CMOS Fabrication](./CMOS%20Fabrication/)
- [Read the section README](./CMOS%20Fabrication/README.md)

### 2. Custom Cell Design

This section covers the custom CMOS inverter. It includes layout work, design-rule checks, circuit extraction, SPICE simulation, timing measurements, and cell files used by the physical-design flow.

- [Open Custom Cell](./Custom%20Cell/)
- [Read the section README](./Custom%20Cell/README.md)
- [Custom-cell files](./Custom%20Cell/files/)

### 3. Synthesis

This section covers the synthesis stage, where the RTL is converted into a gate-level netlist for the next steps in the flow.

- [Open Synthesis](./synthesis/)
- [Read the section README](./synthesis/README.md)
- [Synthesis results](./results/synthesis/)
- [Synthesis reports](./reports/synthesis/)

### 4. Floorplanning and Placement

This section covers the initial physical layout setup, floorplan creation, and placement of standard cells. The screenshots and reports show the design at this stage.

- [Open Floorplanning and Placement](./Floor%20planning%20and%20Placement/)
- [Read the section README](./Floor%20planning%20and%20Placement/README.md)
- [Floorplan results](./results/floorplan/)
- [Placement results](./results/placement/)
- [Floorplan reports](./reports/floorplan/)

### 5. CTS and Timing

This section contains timing material used to understand and check the design around clock-tree synthesis. Timing results need to be read with the report and implementation stage they belong to.

- [Open CTS Timing](./CTS%20Timing/)
- [Read the section README](./CTS%20Timing/README.md)

### 6. Clock-Tree Synthesis

Clock-tree synthesis builds the clock distribution network for the design. This section documents the CTS stage and related checks.

- [Open Clock Tree Synthesis](./Clock%20Tree%20synthesis/)
- [Read the section README](./Clock%20Tree%20synthesis/README.md)
- [CTS results](./results/cts/)

### 7. Routing and Sign-off

This section covers the later physical-design steps, including power-distribution setup, routing, parasitic extraction, and static timing analysis.

- [Open Routing](./Routing/)
- [Read the section README](./Routing/README.md)
- [Routing results](./results/routing/)
- [Routing reports](./reports/routing/)

## Main Project Files

- [RTL and configuration files](./src/)
- [PicoRV32A Verilog source](./src/picorv32a.v)
- [OpenLane configuration](./src/config.tcl)
- [Timing constraints](./src/my_base.sdc)
- [Workflow screenshots](./full%20flow/)
- [Generated results](./results/)
- [Reports](./reports/)

## Timing Checks

I used timing reports to review the design at different stages. Setup and hold slack show whether the relevant timing checks pass or fail. A negative slack value means that the reported check has a timing violation.

Results from synthesis, placement, CTS, and routing can differ, so each value should be read with its own report. The section READMEs contain the screenshots and details for the corresponding stage.

## What I Learned

Through this project, I worked through the main steps of a digital physical-design flow. I explored CMOS fabrication, altered and checked an inverter cell, reviewed synthesis outputs, and followed the design through floorplanning, placement, CTS, routing, and timing analysis. The repository keeps the files, screenshots, and reports for these steps together.

## Repository

[View the complete GitHub repository](https://github.com/devaprassads/SOC-design-and-planning)
