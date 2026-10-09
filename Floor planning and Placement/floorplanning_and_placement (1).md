# Topic 2: Floorplanning and Standard Cell Placement

This part of the project covers what happens after the netlist is ready. The chip first gets a physical size and shape (floorplan). After that, the logic gates from synthesis are placed on it (placement). The design used here is **picorv32a**, run through **OpenLane** with the **sky130** PDK, and the final layout is checked in **Magic**.

---

## Table of Contents
1. [Where this fits in the flow](#1-where-this-fits-in-the-flow)
2. [Basic terms](#2-basic-terms)
3. [Floorplanning](#3-floorplanning)
4. [Macro and decap placement](#4-macro-and-decap-placement)
5. [Standard cell placement](#5-standard-cell-placement)
6. [Hands-on: picorv32a](#6-hands-on-picorv32a)
7. [Floorplan calculations](#7-floorplan-calculations)
8. [Summary](#8-summary)

---

## 1. Where this fits in the flow

```text
RTL -> Synthesis -> Floorplan -> Placement -> CTS -> Routing -> Signoff
                     ^^^^^^^^^^^^^^^^^^^^^
                     (this topic)
```

Synthesis gives a **netlist**, which is just a list of gates and how they connect. It has no physical information. Floorplanning and placement give those gates a real location on silicon.

---

## 2. Basic terms

| Term | Simple meaning |
|---|---|
| **Netlist** | List of gates and the wires connecting them, made by synthesis |
| **Die** | The whole chip area, including the boundary |
| **Core** | The inner area of the die where the cells are actually placed |
| **Utilization** | How much of the core is filled by cells |
| **Aspect ratio** | Height / Width of the core |
| **Macro** | A big pre-built block (like a memory or analog block) |
| **Standard cell** | A small ready-made gate (AND, OR, flip-flop, etc.) |
| **Row / Site** | Rows are the lines cells sit on. Each row is divided into small slots called sites |
| **Tap cell** | Cell placed at regular gaps in the rows to connect the substrate and wells to power |
| **Endcap cell** | Cell placed at the two ends of every row |
| **Decap cell** | Decoupling capacitor, used to keep the power supply steady |
| **HPWL** | Half-perimeter wirelength, a quick way to estimate how long the wires are |
| **Legalization** | Fixing the placement so cells don't overlap and sit exactly on the grid |

---

## 3. Floorplanning

Floorplanning decides the **size and shape of the chip**. It sets up the core and die boundaries, and this is based on the **utilization factor** and **aspect ratio**.

- **Utilization factor** tells how much of the core is covered by cells. If it is 50%, half of the core has cells and the other half is empty.
- The empty space is not wasted. It is needed for routing wires, adding buffers later, and fixing timing.
- **Aspect ratio** decides the shape. A value of 1 means a square core.

Other things done during floorplan:
- Standard cell **rows** are created inside the core.
- **I/O pins** are placed around the boundary.
- **Tap and endcap cells** are inserted in the rows.

---

## 4. Macro and decap placement

Once the floorplan is fixed:

- **Macros are placed first**, near the edges or close to the pins they talk to. This keeps the wires short and reduces wire delay. It also leaves the middle open for the standard cells.
- **Decoupling capacitors (decaps)** are placed around the macros. Macros switch a lot of current at once, which can make the supply voltage drop for a moment. Decaps hold some charge nearby and give the macro that current, so the voltage stays stable.
- Standard cells are not allowed on top of a macro, so that area is treated as a blockage.

> picorv32a has **no macros**, so this step has nothing to place in this design. This is also why the tool throws an error at the macro placement step in the hands-on section below.

---

## 5. Standard cell placement

After the macros are fixed, the standard cells are placed. This happens in two stages:

1. **Global placement**: The tool places the synthesized gates roughly in the right area. The goal is to keep connected cells close together so the total wirelength is small. At this point cells can still overlap a little.
2. **Detailed placement (legalization)**: The tool fixes the rough placement. Cells are moved so that
   - nothing overlaps,
   - every cell sits inside a row,
   - every cell lines up with the site grid.

After this the placement is legal and ready for the next step (clock tree synthesis).

---

## 6. Hands-on: picorv32a

### 6.1 Adding the custom inverter cell

A custom inverter cell (`sky130_vsdinv`) made earlier is added to the design so that it can be used during placement. Its LEF file is checked first. The LEF gives the cell size, `1.380 BY 2.720`, along with the pin positions.

![LEF file of the custom inverter](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/73.png)

The LEF file and the three liberty files (fast, slow, typical) are then copied into the `src` folder of picorv32a, and `ls` is used to confirm everything is there.

![Copying the LEF and lib files into the design src folder](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/74.png)

### 6.2 Setting the config

The `config.tcl` file holds the settings for the run. The important ones for this topic are:

- `FP_CORE_UTIL` = 65, which is the target core utilization in percent
- `FP_IO_VMETAL` and `FP_IO_HMETAL`, which are the metal layers used for the I/O pins
- `EXTRA_LEFS`, which picks up the custom cell's LEF from `src`

![config.tcl with the floorplan settings](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/75.png)

### 6.3 Re-running Synthesis with Custom LEF

Before jumping straight to floorplanning, the tool needs to synthesize the design again so it recognizes the newly added custom inverter. OpenLane is launched interactively and the `picorv32a` design is prepared.

![Launching OpenLane and preparing design](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/76.png)

Next, the custom LEF parameters are manually set in the OpenLane terminal to merge our `sky130_vsdinv` into the main library.

![Setting LEF variables in OpenLane](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/77.png)

The commands `set lefs` and `add_lefs` are executed to ensure the flow recognizes the physical properties of the custom cell during the upcoming placement step.

![Merging the custom LEF](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/78.png)
![Confirming LEF merge in the console](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/79.png)

With the library updated, `run_synthesis` is executed. 

![Executing run_synthesis](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/80.png)
![Synthesis running with ABC logic optimization](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/81.png)

The new synthesis log is generated. We check the area report to see how the inclusion of the custom cell has impacted the overall chip size.

![Synthesis area report](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/82.png)
![Reviewing total cell count](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/83.png)

It is crucial to verify that inserting the custom cell didn't ruin the timing. A quick pre-layout Static Timing Analysis (STA) check is done.

![Checking STA setup](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/84.png)
![Reviewing STA paths](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/85.png)
![Confirming clean timing slack](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/86.png)

### 6.4 Macro placement error

When the physical flow is continued, it stops at the **Basic Macro Placement** step. The log says `Cannot find any macros in this design` and the flow fails. This is expected because picorv32a has no macros. The remaining floorplan and placement steps are then run one by one by hand.

![Flow stops at macro placement because there are no macros](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/87.png)

### 6.5 Floorplan and I/O placement

`init_floorplan` creates the die, the core and the rows. The log prints the die area, the core area, and the number of rows and sites. Right after it, `place_io` puts the 409 pins around the boundary. It uses random pin placement and reports `#Macro blocks found: 0`.

![init_floorplan and place_io output](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/88.png)

### 6.6 Tap and endcap cells

`tap_decap_or` fills the rows with the special cells. The log shows it inserted **528 endcaps** and **7180 tapcells** in the 264 rows.

![tap_decap_or inserting endcaps and tapcells](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/89.png)

The floorplan DEF file is now written to the results folder, and the placement command (`run_placement`) is typed in next.

![Floorplan finished, run_placement about to start](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/90.png)

### 6.7 Placement results

After `run_placement` finishes, the design stats and placement analysis are printed. Some important values are:

| Item | Value |
|---|---|
| Total instances | 25355 |
| Fixed instances (tap + endcap) | 7708 |
| Rows | 264 |
| Row height | 2.7 µm |
| Utilization | 42 % |
| Total / average / max displacement | 0.0 µm |
| HPWL before legalization | 910806.5 µm |
| HPWL after legalization | 895297.0 µm |

The displacement being 0.0 means the cells from global placement were already legal, so detailed placement did not have to move any of them. The wirelength is also slightly lower after legalization (-1.7%).

![Design stats and placement analysis](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/91.png)

### 6.8 Viewing the placement in Magic

The placement DEF is opened in Magic together with the merged LEF, using the sky130A tech file.

![Command used to open the placement in Magic](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/92.png)

The full view shows the standard cells spread across the core, with the pins on the boundary. A few empty patches can be seen because the utilization is not 100%.

![Complete placed design in Magic](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/93.png)

Zooming in shows the individual cells (xor, and, or, nand, etc.) sitting in rows, with the tap cells in between. None of the cells overlap.

![Zoomed view of standard cells and tap cells](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/94.png)

Zooming in further shows the custom `sky130_vsdinv` cell. It has been picked up by the flow and placed in a row like any other standard cell.

![Custom inverter cell placed in the design](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/95.png)

---

## 7. Floorplan calculations

**Formulas**

```text
Core Area     = Netlist Area / Utilization
Aspect Ratio  = Core Height / Core Width
Rows          = Core Height / Row Height
Sites per row = Core Width / Site Width
```

For sky130 `hd` cells: row height = 2.72 µm and site width = 0.46 µm.

**Values from the floorplan log of picorv32a**

- Die: 0, 0 to 731.615, 742.335 µm
- Core: 5.52, 10.88 to 725.88, 728.96 µm

**Core size**

```text
Core Width  = 725.88 - 5.52  = 720.36 µm
Core Height = 728.96 - 10.88 = 718.08 µm
Core Area   = 720.36 x 718.08 ≈ 517,276 µm²
```

This matches the design area printed in the placement report (517276.1 µm²).

**Aspect ratio**

```text
Aspect Ratio = 718.08 / 720.36 ≈ 0.997 ≈ 1
```

So the core is almost a perfect square.

**Rows and sites**

```text
Rows          = 718.08 / 2.72 = 264
Sites per row = 720.36 / 0.46 = 1566
Total sites   = 264 x 1566   = 413,424
```

These match the log line `Added 264 rows of 1566 sites`.

**Die size**

```text
Die Area = 731.615 x 742.335 ≈ 543,103 µm²
```

The die is a bit bigger than the core because of the margin around the core (about 5.5 µm on the left and right, and 10.9 µm at the bottom and 13.4 µm at the top).

**Utilization (from the placement report)**

```text
Movable area = 213,417.2 µm²
Fixed area   = 10,965.5 µm²   (tap + endcap cells)

Utilization = Movable / (Core Area - Fixed)
            = 213,417.2 / (517,276.1 - 10,965.5)
            ≈ 0.42  ->  42 %
```

**Tap and endcap count check**

```text
Endcaps + Tapcells = 528 + 7180 = 7708
```

This equals the fixed instance count in the report.

**Custom cell width**

```text
Cell width = 1.38 µm
Sites used = 1.38 / 0.46 = 3 sites
```

The custom inverter is exactly 3 sites wide, so it fits neatly into the row grid.

---

## 8. Summary

- **Floorplan** sets the die and core size using utilization and aspect ratio.
- **Core Area = Netlist Area / Utilization**
- Rows and sites are created inside the core, and **endcap and tap cells** are added to them.
- **Macros** are placed first with decaps around them. picorv32a has none, so the macro step fails in this run.
- **Global placement** puts cells roughly in place to keep wirelength low.
- **Detailed placement** legalizes the cells (no overlap, aligned to the grid).
- The placed design was checked in **Magic**, and the custom inverter cell was found placed in a row.