# Topic 4: Clock Tree Synthesis (CTS) and Timing Analysis

This stage of the `picorv32a` physical-design flow checks timing before clock-tree synthesis, runs the CTS stage, and reviews the resulting timing reports. The flow uses OpenLane/OpenROAD with the Sky130 standard-cell library. The screenshots below are kept in their original numbered order from the project image set.

## Table of Contents

1. [Flow context](#flow-context)
2. [Floorplan and physical-layout checks](#floorplan-and-physical-layout-checks)
3. [Preparing pre-CTS static timing analysis](#preparing-pre-cts-static-timing-analysis)
4. [Reviewing timing reports](#reviewing-timing-reports)
5. [Clock-tree synthesis and post-CTS checks](#clock-tree-synthesis-and-post-cts-checks)
6. [Summary](#summary)

---

## Flow context

Clock-tree synthesis inserts buffers and/or inverters into the clock network so the clock can reach sequential cells with controlled skew and transition time. Static timing analysis (STA) checks whether data paths meet setup and hold requirements. Timing is checked before CTS and again after CTS because the inserted clock network changes clock arrival times and can affect timing slack.

## Floorplan and physical-layout checks

The first terminal screenshots show OpenROAD reading the merged technology/library LEF and the floorplan DEF, then progressing through physical-design preparation. The log reports the `picorv32a` design and its pins, components, and nets.

![OpenROAD reads the design and starts physical preparation](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/89.png)

The OpenROAD log continues through the floorplan preparation flow, including I/O placement and tap/decap processing.

![Floorplan preparation log continues](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/90.png)

The terminal shows further progress in the OpenROAD physical-design steps.

![OpenROAD physical-design progress](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/91.png)

The terminal shows the next command/output stage after the initial physical preparation.

![Continuation of the physical-design run](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/92.png)

The layout is opened in Magic to inspect the design at full-chip scale.

![Full layout view in Magic](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/93.png)

A more detailed view shows the layout geometry and regions inside the design.

![Detailed physical layout view](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/94.png)

The zoomed view highlights layout structures around the design boundary and internal rows/rails.

![Zoomed layout structures in Magic](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/95.png)

---

## Preparing pre-CTS static timing analysis

The following terminal screenshots show the commands and configuration used to prepare and run timing analysis. The STA setup reads the standard-cell timing libraries, the synthesized Verilog netlist, and the SDC constraints before reporting timing paths and slack.

![Terminal commands for timing setup](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/96.png)

The terminal continues the timing-flow setup and execution.

![Timing-flow command output](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/97.png)

The `pre_sta.conf` configuration contains the library and design inputs for OpenSTA/OpenROAD. It reads the slow library for maximum-delay analysis and the fast library for minimum-delay analysis, links the `picorv32a` design, reads the SDC constraints, and requests timing checks.

![Pre-STA configuration file](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/98.png)

The output from the timing command is opened for inspection.

![Pre-CTS timing report output](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/99.png)

---

## Reviewing timing reports

The timing-report screenshots show path and timing-summary output generated from the design and its constraints. These reports are used to review the critical paths and identify setup/hold issues before building the clock tree.

![Timing report listing](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/100.png)

![Continuation of timing report data](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/101.png)

![Additional timing report output](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/102.png)

The next terminal images show further OpenROAD/OpenSTA commands and their output as the flow proceeds from the pre-CTS checks toward clock-tree synthesis.

![Timing and physical-design commands](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/103.png)

![Continuation of the OpenROAD run](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/104.png)

![OpenROAD output during clock-tree preparation](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/105.png)

---

## Clock-tree synthesis and post-CTS checks

Clock-tree synthesis constructs the clock distribution network and inserts clock buffers as needed. After CTS, timing is checked again because the new clock paths affect clock latency, skew, and setup/hold slack. The following screenshots document the terminal output and reports from the later stages of the run.

![Clock-tree synthesis and timing-flow output](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/106.png)

![Clock-tree run output continues](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/107.png)

![Further clock-tree and timing output](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/108.png)

![Terminal output from the later CTS stage](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/109.png)

![Timing report output after CTS](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/110.png)

![Further post-CTS timing output](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/111.png)

![Post-CTS timing and slack report](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/112.png)

![Additional post-CTS command output](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/113.png)

![Further timing checks and report output](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/114.png)

![Final screenshot in the CTS and timing sequence](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/115.png)

---

## Summary

- The physical-design run reads the technology/library information and the floorplan DEF for `picorv32a`.
- Magic is used to inspect the layout at full-chip and zoomed-in scales.
- The pre-STA configuration specifies the timing libraries, synthesized netlist, SDC constraints, and timing-report commands.
- Pre-CTS timing reports are reviewed before clock-tree synthesis.
- CTS builds the clock distribution network using inserted clock buffers.
- Post-CTS timing checks are required to assess setup/hold slack after the clock network has been added.

The screenshots document the flow and its reports. Numeric setup slack, hold slack, skew, or buffer-count values should be quoted only when they are clearly readable in the corresponding report output.
