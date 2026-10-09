# Topic 1: Synthesis and RTL to Gate Level Mapping

This module starts the physical design flow. The behavioral RTL of the **picorv32a** core is synthesized into a gate-level netlist using the **Sky130** standard cell library (Yosys + ABC). After that, the design goes through floorplan and placement in OpenLane, and the layout of a single standard cell (an inverter) is checked in Magic.

**Contents**
1. [Synthesis](#1-synthesis-images-1-4)
2. [Floorplan and Power Planning](#2-floorplan-and-power-planning-images-5-14)
3. [Placement](#3-placement-images-15-17)
4. [Sky130 Inverter Layout](#4-sky130-inverter-layout-images-18-24)
5. [Results Summary](#5-results-summary)

**Quick terms**
- **RTL**: Verilog code describing the design
- **Netlist**: the design written as real logic gates and their connections
- **Standard cell**: a ready-made gate or flip-flop from the library
- **Slack**: time left on a path. Negative slack means the path is too slow (WNS = worst, TNS = total)

---

## 1. Synthesis (Images 1-4)

### Image 1: Starting OpenLane
![Synthesis Step 1](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/1.png)

OpenLane is started in interactive mode with `./flow.tcl -interactive`. Then `prep -design picorv32a` sets up the design. It picks the `sky130A` PDK and the `sky130_fd_sc_hd` cell library, creates a new run folder, and merges the LEF files. After that, `run_synthesis` starts synthesis.

### Image 2: Synthesis done
![Synthesis Step 2](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/2.png)

Yosys finishes and reports a chip area of **147712.918400**. The netlist is saved as `picorv32a.synthesis.v`. OpenSTA then runs a first timing check, which gives **wns -24.89** and **tns -759.46**. The slack is negative at this stage, so timing still needs work later. The log ends with `Synthesis was successful`.

### Image 3: Synthesis report (part 1)
![Synthesis Step 3](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/3.png)

The Yosys report shows **14876 cells** and 14596 wires, followed by how many times each Sky130 cell is used. The flip-flop `dfxtp_2` is used **1613** times.

### Image 4: Synthesis report (part 2)
![Synthesis Step 4](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/4.png)

This is the rest of the cell list, ending with the chip area. Flip-flop ratio from the numbers in images 3 and 4:

```
Flip-flop ratio = flip-flops / total cells
                = 1613 / 14876
                = 0.1084
                = 10.84 %  (about 1 flip-flop in every 10 cells)
```

---

## 2. Floorplan and Power Planning (Images 5-14)

### Image 5: Running the floorplan
![Synthesis Step 5](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/5.png)

`run_floorplan` sets the die size to **660.685 x 671.405 microns**. The core runs from (5.52, 10.88) to (655.04, 658.24), so its size is:

```
Core width  = 655.04 - 5.52  = 649.52 microns
Core height = 658.24 - 10.88 = 647.36 microns
```

There are 238 rows. Then the tool places the **409 IO pins** and starts tap/decap cell insertion.

### Image 6: Power grid (PDN)
![Synthesis Step 6](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/6.png)

The power grid for **VPWR** and **VGND** is generated. The many warnings only say that the power source points were moved to the nearest stripe. The run ends with `PDN generation was successful`.

### Image 7: Floorplan DEF file
![Synthesis Step 7](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/7.png)

The floorplan DEF file opened as text. It has the die area and the list of rows. The file uses `UNITS DISTANCE MICRONS 1000`, so 1000 units = 1 micron. Converting the values:

```
Die width  = 660685 / 1000 = 660.685 microns
Die height = 671405 / 1000 = 671.405 microns
```

For the rows, `ROW_0` is at y = 10880 and `ROW_1` is at y = 13600, and each row has 1412 sites with `STEP 460`:

```
Row height = 13600 - 10880 = 2720 units = 2.72 microns
Row width  = 1412 x 460 = 649520 units = 649.52 microns   (same as the core width)
Core height = 238 rows x 2.72 = 647.36 microns            (same as the core height)
```

So the rows fill the core exactly.

### Image 8: Floorplan in Magic
![Synthesis Step 8](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/8.png)

The floorplan opened in Magic. The die looks empty because the cells are not placed yet. The vertical lines are columns of tap and decap cells.

### Image 9: Zoomed floorplan
![Synthesis Step 9](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/9.png)

Zooming in shows the `decap_3` cells and the `tapvpwrvgnd_1` tap cells (named `PHY_...`). The small hatched boxes like `pcpi_rs1[18]` and `mem_la_wdata[24]` are the IO pins.

### Image 10: Checking pin layers
![Synthesis Step 10](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/10.png)

The `what` command in the Magic console shows that the pins `mem_wdata[16]` and `pcpi_rs2[19]` are on **metal3**.

### Image 11: Another pin and the stacked cells
![Synthesis Step 11](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/11.png)

Here `trace_data[29]` is found on **metal2**. The black box at the bottom left with overlapping text is all the standard cells piled up in one place, since they have not been placed yet.

### Image 12: Left edge of the die
![Synthesis Step 12](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/12.png)

A closer view of the left edge with a column of `decap_3` cells, some tap cells, and pin labels like `_wstrb[0]`.

### Image 13: Tap cell pattern
![Synthesis Step 13](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/13.png)

Only tap cells are visible here, arranged in a staggered pattern. They connect the N-well to VPWR and the substrate to VGND, which helps avoid latch-up.

### Image 14: Selecting one stacked cell
![Synthesis Step 14](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/14.png)

One cell from the pile is selected. It is the instance `_13031_` of `sky130_fd_sc_hd__buf_1`, so the stacked objects are the real cells from the netlist.

---

## 3. Placement (Images 15-17)

### Image 15: Placement report
![Synthesis Step 15](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/15.png)

The placement report shows:
- **21699** total instances, **36 %** utilization (as reported by the tool) and 238 rows
- Wire length (HPWL) went from 779196.5 to **766080.0** microns

Extra instances compared to synthesis:

```
Extra instances = 21699 - 14876 = 6823
Fixed tap/decap cells = 6354
Remaining = 6823 - 6354 = 469   (most likely buffers added by the resizer)
```

HPWL change:

```
Change = 766080.0 - 779196.5 = -13116.5 microns
Percent = -13116.5 / 779196.5 x 100 = -1.68 %  (about -1.7 %)
```

### Image 16: Placed design in Magic
![Synthesis Step 16](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/16.png)

The same die as image 8, now filled with standard cells in rows. The cyan lines are the power grid stripes.

### Image 17: Zoomed placed cells
![Synthesis Step 17](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/17.png)

The individual cells (`dfxtp`, `a22o`, `mux2`, `buf`, `clkbuf`) sit in rows along the power rails, with tap cells in between. The big VPWR stripes come from the power grid.

---

## 4. Sky130 Inverter Layout (Images 18-24)

### Image 18: Opening the inverter in Magic
![Synthesis Step 18](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/18.png)

The `vsdstdcelldesign` repo is cloned, the `sky130A.tech` file is copied in, and `magic -T sky130A.tech sky130_inv.mag &` opens the inverter layout.

### Image 19: Inverter layout
![Synthesis Step 19](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/19.png)

The full inverter. **VPWR** is at the top, **VGND** at the bottom, **A** is the input (the red poly gate) and **Y** is the output. The PMOS is in the N-well at the top and the NMOS is at the bottom.

### Image 20: NMOS
![Synthesis Step 20](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/20.png)

The lower transistor is selected, and `what` shows **nmos**.

### Image 21: PMOS
![Synthesis Step 21](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/21.png)

The upper transistor shows **pmos**. Both transistors share the same poly gate, so they get the same input A.

### Image 22: Output Y
![Synthesis Step 22](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/22.png)

The output Y touches both `ndiff` and `pdiff` through `locali`. This means the drains of the PMOS and NMOS are joined to make the output.

### Image 23: VPWR connection
![Synthesis Step 23](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/23.png)

VPWR is on `metal1` and connects to the PMOS source and also to the N-well (through `nsubdiff`).

### Image 24: VGND connection
![Synthesis Step 24](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/24.png)

VGND is on `metal1` and connects to the NMOS source and also to the P-substrate (through `psubdiff`).

---

## 5. Results Summary

| Item | Value |
|------|-------|
| Design | picorv32a |
| Cells after synthesis | 14876 |
| Flip-flops (`dfxtp_2`) | 1613 (1613 / 14876 = 10.84 %) |
| Chip area (synthesis) | 147712.918400 |
| WNS / TNS (synthesis) | -24.89 / -759.46 |
| Die size | 660.685 x 671.405 microns |
| Core size | 649.52 x 647.36 microns (655.04 - 5.52 and 658.24 - 10.88) |
| IO pins | 409 |
| Instances after placement | 21699 |
| Utilization | 36 % |
| HPWL after placement | 766080.0 microns (-1.68 % from 779196.5) |
