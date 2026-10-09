# Topic 2: Floorplanning and Standard Cell Placement

This part of the project covers what happens after the netlist is ready. The chip now gets a physical size and shape (floorplan), and the logic gates from synthesis are placed on it (placement). The result is checked in the **Magic** layout viewer.

---

## Table of Contents
1. [Where this fits in the flow](#1-where-this-fits-in-the-flow)
2. [Basic terms](#2-basic-terms)
3. [Floorplanning](#3-floorplanning)
4. [Floorplan calculations](#4-floorplan-calculations)
5. [Macro and decap placement](#5-macro-and-decap-placement)
6. [Standard cell placement](#6-standard-cell-placement)
7. [Step-by-step visuals](#7-step-by-step-visuals)
8. [Summary](#8-summary)

---

## 1. Where this fits in the flow

```
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
| **Decap cell** | Decoupling capacitor, used to keep the power supply steady |
| **Legalization** | Fixing the placement so cells don't overlap and sit exactly on the grid |

---

## 3. Floorplanning

Floorplanning decides the **size and shape of the chip**. It sets up the core and die boundaries, and this is based on the **utilization factor** and **aspect ratio**.

- **Utilization factor** tells how much of the core is covered by cells. If it is 50%, then half of the core has cells and the other half is empty.
- The empty space is not wasted. It is needed for routing wires, adding buffers later, and fixing timing.
- **Aspect ratio** decides the shape. 1 means a square core.

Other things set during floorplan:
- Standard cell **rows** are created inside the core.
- **I/O pins** are placed around the boundary.
- **Power planning** (VDD/VSS) is prepared.

---

## 4. Floorplan calculations

The formulas below are used to get the core and die size.

**Utilization**

```
Utilization = (Area occupied by netlist cells) / (Total core area)
Core Area = Netlist Area / Utilization
```

**Aspect ratio**

```
Aspect Ratio = Core Height / Core Width
```

For a square core (aspect ratio = 1), width = height = sqrt(Core Area).

### Worked example (sample numbers)

Assume:
- Netlist area = 100,000 µm²
- Utilization = 50% = 0.5
- Aspect ratio = 1

**Step 1: Core area**

```
Core Area = 100,000 / 0.5 = 200,000 µm²
```

**Step 2: Core width and height** (square core)

```
Width = Height = sqrt(200,000) ≈ 447.2 µm
```

**Step 3: Number of rows**

For the sky130 `hd` library, one standard cell row is 2.72 µm tall and one site is 0.46 µm wide.

```
Number of rows   = 447.2 / 2.72 ≈ 164 rows
Sites per row    = 447.2 / 0.46 ≈ 972 sites
```

**Step 4: Die size**

Die = Core + the margin around it (the margin is set by the config file), so the die is always a bit bigger than the core.

## 5. Macro and decap placement

Once the floorplan is fixed:

- **Macros are placed first**, near the edges or close to the pins they talk to. This keeps the wires short and reduces wire delay. It also leaves the middle open for the standard cells.
- **Decoupling capacitors (decaps)** are placed around the macros. Macros switch a lot of current at once, which can make the supply voltage drop for a moment. Decaps hold some charge nearby and give the macro that current, so the voltage stays stable.
- Cells are not allowed to be placed in the macro area, so this region is marked as a **blockage**.

---

## 6. Standard cell placement

After the macros are fixed, the standard cells are placed. This happens in stages:

1. **Global placement**: The tool places the synthesized gates roughly in the right area. The goal is to keep connected cells close together so that total wirelength is small. At this point cells can still overlap a little.
2. **Detailed placement (legalization)**: The tool fixes the rough placement. Cells are moved so that
   - nothing overlaps,
   - every cell sits inside a row,
   - every cell lines up with the site grid.

After this the placement is legal and ready for the next step (clock tree synthesis). The final layout is opened and verified in **Magic**.

---

## 7. Step-by-step visuals

### 7.1 Running the floorplan

Here the floorplan step is launched from the flow. The tool reads the netlist and the config values (utilization, aspect ratio, etc.) and creates the die and core.

![Placement Step 25](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/25.png)
Floorplan command is started and the tool begins reading the netlist and config.

![Placement Step 26](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/26.png)
 Tool output showing the floorplan stages running one after another.

![Placement Step 27](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/27.png)
Floorplan finishes. The die and core area are now defined.

![Placement Step 28](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/28.png)
The floorplan output files (like the DEF file) are generated in the results folder.

### 7.2 Checking the floorplan

The generated floorplan file is checked to confirm the die size, core size and rows match the calculations in section 4.

![Placement Step 29](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/29.png)
Opening the floorplan file to read the die area values.

![Placement Step 30](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/30.png)
Die dimensions are read from the file and converted to µm to compare with the expected size.

![Placement Step 31](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/31.png)
**Step 31:** Launching Magic to see the floorplan visually.

![Placement Step 32](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/32.png)
**Step 32:** The floorplan loaded in Magic. The die boundary and I/O pins can be seen.

![Placement Step 33](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/33.png)
**Step 33:** Zoomed view of the pins placed around the die edge.

![Placement Step 34](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/34.png)
**Step 34:** Zoomed view of the standard cell rows and the power rails (tap/decap cells at the row ends).

![Placement Step 35](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/35.png)
**Step 35:** Closer look at the cells sitting in the empty rows. Standard cells are still not placed properly at this stage, they are stacked at the corner or not yet placed.

![Placement Step 36](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/36.png)
**Step 36:** Checking the layout details (selecting cells/pins to see names and sizes).

### 7.3 Macro and decap placement

![Placement Step 37](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/37.png)
**Step 37:** Macro/decap related step. Fixed cells and macros are positioned first.

![Placement Step 38](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/38.png)
**Step 38:** Layout after the fixed cells are placed.

![Placement Step 39](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/39.png)
**Step 39:** Zoomed view of the decap cells around the macro area.

![Placement Step 40](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/40.png)
**Step 40:** Confirming the macro area is blocked so that standard cells won't be placed on it.

### 7.4 Running placement

![Placement Step 41](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/41.png)
**Step 41:** Placement command is started.

![Placement Step 42](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/42.png)
**Step 42:** Global placement running. The tool shows progress while it spreads the cells and reduces wirelength.

![Placement Step 43](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/43.png)
**Step 43:** Global placement output with overflow values coming down, which means the cells are getting spread out properly.

![Placement Step 44](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/44.png)
**Step 44:** Global placement completed.

![Placement Step 45](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/45.png)
**Step 45:** Detailed placement starts. Cells are legalized and aligned to the grid.

![Placement Step 46](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/46.png)
**Step 46:** Detailed placement report with the movement of cells and the legality check.

![Placement Step 47](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/47.png)
**Step 47:** Placement finished and the placement DEF file is generated.

### 7.5 Viewing the placed design in Magic

![Placement Step 48](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/48.png)
**Step 48:** Opening the placement result in Magic.

![Placement Step 49](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/49.png)
**Step 49:** Full view of the placed design. The standard cells are now spread across the core.

![Placement Step 50](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/50.png)
**Step 50:** Zoomed in to see the cells sitting in rows.

![Placement Step 51](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/51.png)
**Step 51:** Closer view showing cells next to each other with no overlap.

![Placement Step 52](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/52.png)
**Step 52:** Selecting a cell to check its name and size.

![Placement Step 53](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/53.png)
**Step 53:** Checking that cells are aligned to the row and site grid.

![Placement Step 54](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/54.png)
**Step 54:** Another zoomed area confirming the legal placement.

![Placement Step 55](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/55.png)
**Step 55:** Final placed layout. Floorplan and placement are done, and the design is ready for the next stage (CTS).

---

## 8. Summary

- **Floorplan** sets the die and core size using utilization and aspect ratio.
- **Core Area = Netlist Area / Utilization**
- **Macros** are placed first, near the edges, with **decap cells** around them.
- **Global placement** puts cells roughly in place to keep wirelength low.
- **Detailed placement** legalizes the cells (no overlap, aligned to the grid).
- The result is checked in **Magic**.
