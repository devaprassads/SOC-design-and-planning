# Topic 5: Detailed Routing and Final Sign-off

This stage documents timing-path inspection, the preparation of the design files, synthesis with the required cell definitions, power-distribution-network (PDN) generation, and the final routing and parasitic-extraction run for **picorv32a** using OpenLane and the Sky130A PDK.

## Table of Contents

1. [Pre-route timing inspection](#1-pre-route-timing-inspection)
2. [Preserving and preparing the synthesis netlist](#2-preserving-and-preparing-the-synthesis-netlist)
3. [Synthesis and floorplan initialization](#3-synthesis-and-floorplan-initialization)
4. [OpenLane run setup and physical-design preparation](#4-openlane-run-setup-and-physical-design-preparation)
5. [Power Distribution Network generation](#5-power-distribution-network-generation)
6. [Routing and parasitic extraction](#6-routing-and-parasitic-extraction)
7. [Summary](#7-summary)

---

## 1. Pre-route timing inspection

The timing report is inspected before proceeding with the physical-design run. It identifies a register-to-register path in the `clk` path group and reports a negative slack, so the shown path has a setup-timing violation at this point in the flow.

![Timing path details from the OpenSTA report](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/116.png)

*The terminal displays the path's cell and net information from a timing/path report. The entries identify connected standard-cell pins and nets along the path.*

![Setup timing report with violated slack](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/117.png)

*OpenSTA reports a path from startpoint `_29043_` to endpoint `_30440_`, both rising-edge-triggered flip-flops clocked by `clk`. The report shows a data required time of **24.4361**, a data arrival time of **−47.0534**, and slack of **−22.6173 (VIOLATED)**. The negative slack is the report's evidence of a setup-timing problem for this path.*

The report values are recorded as shown in the screenshot; no timing improvement is inferred from this report alone.

---

## 2. Preserving and preparing the synthesis netlist

The synthesis output is backed up before editing. This keeps a copy of the original synthesized Verilog available while the working version is adjusted.

![Copying the synthesized Verilog netlist](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/118.png)

*The command `cp picorv32a.synthesis.v picorv32a.synthesis_old.v` creates a backup named `picorv32a.synthesis_old.v`. The directory listing confirms that both the original and backup files are present.*

![Reviewing the synthesized netlist and cell definitions](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/119.png)

*The terminal shows the continuation of the timing-path listing, including the sequence of standard-cell pins and the endpoint. This view provides the cell-level context for the path being investigated.*

![Opening the synthesized Verilog for editing](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/120.png)

*The synthesized Verilog netlist is open in a text editor. The displayed section contains instantiated Sky130 standard cells and their signal connections, allowing the cell definitions in the netlist to be reviewed.*

---

## 3. Synthesis and floorplan initialization

The OpenLane design is prepared again with the selected configuration and Sky130A platform files. The run output shows the LEF files being merged, the custom `sky130_vsdinv.lef` being included, and synthesis being launched.

![Preparing the OpenLane design and adding the custom LEF](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/121.png)

*OpenLane prepares the `picorv32a` run, reads the Sky130A technology and standard-cell LEFs, merges the LEF files, and adds `sky130_vsdinv.lef`. The terminal then sets the synthesis strategy and starts `run_synthesis`.*

![Synthesis completion and floorplan initialization](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/122.png)

*The terminal reports that synthesis was successful and starts `init_floorplan`. The output includes the synthesized module's reported chip area and the timing constraints used by the flow, including input and output delay settings.*

---

## 4. OpenLane run setup and physical-design preparation

The following terminal screenshots document the continuing OpenLane run setup and the commands used to prepare the design for the physical implementation stages. They are kept in their original screenshot order.

![OpenLane run and configuration output](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/123.png)

*`init_floorplan` completes the initial floorplan setup. The log records a die area of about 731.615 × 742.335 µm and a core area of about 720.528 × 718.08 µm, then starts I/O placement (`ioPlacer`).*

![Physical-design setup output](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/124.png)

*OpenLane completes I/O pin placement and starts tap/decap insertion. The log reports 528 end-cap cells and 7,180 tap cells inserted, then writes the floorplan DEF output.*

![Reviewing generated design data](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/125.png)

*The placement resizer stage reports placement statistics: 25,535 instances, 264 rows, 42% utilization (60% padded), and an HPWL change of about +2%. The terminal then starts `run_cts`.*

![Continuing the physical-design commands](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/126.png)

*Clock Tree Synthesis completes successfully. The timing summary shown above the CTS message reports data required time 24.44, data arrival time −14.41, and slack **10.03 (MET)**. The generated CTS DEF and CTS Verilog are written and a layout screenshot is captured.*

![Checking the run output](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/127.png)

*The CTS DEF and synthesized CTS netlist are loaded for timing checks. The log includes library/LEF warnings and a minimum-delay (`Path Type: min`) report for a register-to-register path; these warnings and the path report need to be considered when reviewing timing.*

![Inspecting generated implementation data](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/128.png)

*A minimum-delay timing path is displayed through clock buffers and a flip-flop. The report ends with slack **−0.0228 (VIOLATED)**, indicating a hold-timing issue for the path shown in this run.*

![Continuing the implementation-file review](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/129.png)

*The terminal continues the timing-path/report output, showing the path's cell and net sequence for inspection.*

![OpenLane commands and implementation output](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/130.png)

*A new OpenLane run named `fresh1` is prepared. The CTS clock-buffer list is set to Sky130 clock-buffer cells, the placement DEF is selected as the current design, and `run_cts` is launched.*

![Reviewing the next run stage](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/131.png)

*The placement analysis reports a 1.6% reduction in HPWL after legalization. A minimum-delay check for a register-to-register path reports slack **0.25 (MET)** in this run.*

![Implementation flow output](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/132.png)

*The CTS stage completes successfully again. The displayed timing summary shows data required time 24.44, data arrival time −14.41, and slack **10.03 (MET)**. The CTS DEF is written and the layout screenshot is captured.*

![Checking implementation results](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/133.png)

*OpenROAD reads the CTS DEF and CTS netlist, links the design, sets the propagated clock, and runs a minimum-delay timing check. The report identifies a register-to-register path in the `clk` group.*

![Generated file listing](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/134.png)

*The terminal shows the timing-path table continuing, with the clock network and data-path elements listed alongside fanout, capacitance, slew, delay, and arrival-time columns.*

![Continuing the generated-file review](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/135.png)

*The timing report continues through the remaining elements of the reported path, providing the cell-by-cell delay breakdown.*

![Reviewing physical-design output](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/136.png)

*The terminal shows the end of the path report and subsequent flow commands. It is part of the timing-check sequence before the PDN step.*

![Final commands before PDN generation](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/137.png)

*The CTS timing summary again shows slack **10.03 (MET)**, followed by the message that Clock Tree Synthesis was successful. The terminal then enters the `gen_pdn` command to generate the power-distribution network.*

---

## 5. Power Distribution Network generation

The PDN stage creates the power and ground distribution structures and connects the design's power pins to the network. The log also reports that some source locations do not fall directly on a power stripe and are moved to the closest stripe.

![PDN generation log and stripe-location warnings](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/138.png)

*The terminal reports the creation of the power grid for `VPWR` and `VGND`, including a successful grid-matrix creation and connections between PDN nodes. Warnings indicate that some source locations are not on a stripe and are moved to the closest stripe. The log ends with **PDN generation was successful** and a command to change the layout to the generated CTS DEF and PDN DEF.*

![PDN log continuation and routing command](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/139.png)

*The terminal shows the continuation of the PDN messages and the successful-generation status. The `run_routing` command is entered to start the routing stage.*

---

## 6. Routing and parasitic extraction

After routing, the flow extracts interconnect resistance and capacitance and writes the extracted parasitics to a SPEF file. Static timing analysis is then run using the extracted data.

![RC extraction, SPEF writing, and static timing analysis](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/140.png)

*The terminal reports that RC extraction is done and the SPEF file has been written. OpenSTA then runs static timing analysis. The log also shows the routing run completing for `picorv32a` in **0h19m17s**. The screenshot contains warnings about some cell modules being unavailable in the synthesis netlist and represented as black boxes; those warnings should be reviewed before treating the result as final sign-off.*

### Timing values available in the screenshots

The screenshots show timing values at more than one stage/run. These values are kept separate because they do not all represent the same timing check.

| Check/stage | Values shown |
|---|---|
| Pre-route maximum-delay path | Required time: 24.4361; arrival time: −47.0534; slack: **−22.6173 (VIOLATED)** |
| CTS timing summary | Required time: 24.44; arrival time: −14.41; slack: **10.03 (MET)** |
| Minimum-delay path | Slack: **−0.0228 (VIOLATED)** |
| Minimum-delay path in a separate run | Slack: **0.25 (MET)** |

The reports show mixed results across separate checks/runs, including a hold violation in one minimum-delay report. The screenshots do not provide a clearly identified final post-route slack summary, so the CTS values above are not presented as final post-route timing. No unsupported timing calculation is added.

---

## 7. Summary

- The pre-route OpenSTA report identifies a register-to-register path with **−22.6173 slack**, marked as violated.
- The synthesized Verilog netlist is backed up before the working netlist is reviewed.
- OpenLane is prepared with the Sky130A platform files and the custom `sky130_vsdinv.lef`; synthesis completes and floorplan initialization begins.
- The CTS summaries show **10.03 slack (MET)**, while separate minimum-delay reports include both **−0.0228 (VIOLATED)** and **0.25 (MET)**; these belong to different checks/runs and are not treated as one result.
- The PDN log reports successful power-grid generation, while also noting that some source locations are moved to the nearest power stripe.
- The routing log reports completion, followed by RC extraction, SPEF generation, and a static timing analysis run.
- A final post-route slack value is not legible in the supplied screenshots, so it is not claimed here.
