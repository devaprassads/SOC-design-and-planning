# Topic 2: Floorplanning and Standard Cell Placement

This part of the project explains how the synthesized design is prepared for physical implementation. First, the chip area is planned, and then the logic cells are placed inside it. The design used here is **picorv32a**, with **OpenLane** and the **sky130 PDK**. Magic is used to view and check the layout.

---

## Table of Contents

1. [Where this fits in the flow](#where-this-fits-in-the-flow)
2. [Preparing the Flow](#preparing-the-flow)
3. [Floorplanning](#floorplanning)
4. [Standard Cell Placement](#standard-cell-placement)
5. [Viewing the Placement Results](#viewing-the-placement-results)
6. [Summary](#summary)

---

## Where this fits in the flow

```text
RTL -> Synthesis -> Floorplan -> Placement -> CTS -> Routing -> Signoff
                    ^^^^^^^^^^^^^^^^^^^^^^
                      This part
```

Synthesis produces a **netlist**, which contains the logic cells and their connections. However, it does not specify the physical locations of those cells. Floorplanning decides the chip area, and placement gives the cells their positions on the chip.

---

## Preparing the Flow

Before starting floorplanning, Check that the design files and synthesis results are available. These checks help make sure the flow is ready for the physical design stage.

![Directory Check](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/25.png)

*The terminal shows the project directory being checked. This helps confirm that the required design files can be accessed.*

![Results Summary](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/26.png)

*This view is used to check the available results from the previous stage. These results are needed before continuing with floorplanning.*

![Moving to next stage](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/27.png)

*The commands shown move the workflow towards physical design. The synthesized design is now ready to be used by the floorplanning stage.*

![Preparation for Floorplan](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/28.png)

*This shows the setup immediately before floorplan initialization. The design and its configuration need to be ready before the chip area is created.*

---

## Floorplanning

Floorplanning decides the **size and shape of the chip**. It defines the die boundary and the core area where most standard cells are placed.

- **Utilization** is the percentage of the core area occupied by cells. Some free space is needed for routing and later changes.
- **Aspect ratio** is the core height divided by its width. An aspect ratio of 1 means the core is square.

The floorplan is initialized in OpenLane using the selected configuration.

![Floorplan Initialization](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/29.png)

*The floorplan initialization command is run. This starts the process of defining the physical area for the design.*

![Setting Core Area](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/30.png)

*The core-area settings determine the space available for standard cells. The core is smaller than the complete die because space is left around its boundary.*

![Setting Aspect Ratio](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/31.png)

*The aspect-ratio setting controls the shape of the core. It helps OpenLane decide whether the core should be square or rectangular.*

![Utilization Factor Setup](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/32.png)

*The utilization target is configured here. The tool uses it when deciding how much core area to reserve for the cells and how much space to leave free.*

After the core is defined, the flow also places I/O pins around the boundary and prepares routing tracks. Tap cells are added to help prevent latch-up in the CMOS structure. Decap cells help reduce short-term changes in the power-supply voltage.

![I/O Pin Placement](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/33.png)

*The I/O placement stage assigns locations to the input and output pins around the chip boundary. These pins allow signals to enter and leave the design.*

![Macro Placement Check](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/34.png)

*The tool checks whether the design contains macros that need physical placement. The picorv32a design used here has no macros, so the macro count is zero.*

![Tracks Information](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/35.png)

*The routing-track information defines the available tracks for metal wires. These tracks are used later when the tool connects the placed cells.*

![Tap and Decap Insertion](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/36.png)

*Tap and decap cells are inserted as part of floorplan preparation. Tap cells help maintain proper substrate and well connections, while decap cells help stabilize the power supply.*

### Checking the Floorplan in Magic

The floorplan DEF file is opened in Magic to inspect the layout visually. This helps check the core boundary, I/O pins, and the placement of special cells.

![Opening Magic for Floorplan](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/37.png)

*The command opens the floorplan layout in Magic. Magic allows the physical design to be viewed and checked.*

![Magic Floorplan View](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/38.png)

*The layout view shows the planned chip area. It provides a visual check that the floorplan has been created.*

![Inspecting Pins](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/39.png)

*The view is zoomed in to inspect the I/O pins near the boundary. Their positions matter because they connect the chip to external signals.*

![Inspecting Decaps](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/40.png)

*This view is used to inspect the special cells in the floorplan, including decap cells. These cells support a more stable power supply.*

---

## Standard Cell Placement

Once the floorplan is ready, the standard cells from the netlist are placed inside the core. Placement is done in two main stages.

### Global Placement

During **global placement**, the tool gives cells approximate positions. It tries to keep connected cells close together so that the estimated wirelength is reduced. Some overlap may still exist at this stage.

![Starting Global Placement](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/41.png)

*The global-placement process begins. The tool starts assigning approximate locations to the standard cells inside the core.*

![HPWL Optimization](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/42.png)

*The placement tool evaluates estimated wirelength. HPWL stands for Half-Perimeter Wirelength and is a quick estimate of the wiring needed to connect cells.*

![Placement Iterations](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/43.png)

*The tool performs placement iterations to improve the arrangement of the cells. It tries to balance wirelength and the available core area.*

![Global Placement Complete](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/44.png)

*The global-placement stage finishes with approximate locations assigned to the cells. The design can now move to detailed placement, where the positions are corrected.*

### Detailed Placement and Legalization

During **detailed placement**, the tool adjusts the cells so that they do not overlap and fit correctly into the standard-cell rows and placement grid. This is called **legalization**.

![Starting Detailed Placement](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/45.png)

*The detailed-placement stage starts using the result from global placement. The tool prepares to make the cell positions legal.*

![Legalization Process](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/46.png)

*The tool adjusts cell positions to remove overlaps and align the cells with the placement rows and site grid.*

![Legalization Complete](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/47.png)

*The legalization stage completes. The cells now have valid physical positions for the next stages of the design flow.*

![Checking Placement DEF](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/48.png)

*The placement DEF output is checked. DEF stores physical-design information such as component locations and pin positions, so it can be used to view the placement in a layout tool.*

---

## Viewing the Placement Results

The placement result is opened in Magic to check how the standard cells are arranged in the core.

![Opening Magic for Placement](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/49.png)

*The command loads the placed design into Magic so the physical arrangement can be inspected.*

![Magic Placement Overview](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/50.png)

*The full layout gives an overview of the standard cells spread across the core. The empty spaces are useful for routing and future adjustments.*

![Zoomed in Cells](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/51.png)

*The zoomed view makes individual standard cells easier to see. It helps check that the cells are arranged in rows rather than randomly placed.*

![Standard Cell Alignment](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/52.png)

*This closer view is used to check the alignment of the cells with the placement grid. Correct alignment is important for a legal layout.*

![Row Verification](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/53.png)

*The row arrangement is inspected to confirm that the standard cells fit into the defined placement rows.*

![Power Rail Alignment](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/54.png)

*The power and ground connections are inspected in the layout. These rails distribute the supply connections needed by the cells.*

![Placement Congestion Check](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/55.png)

*The congestion view helps identify areas where many connections may compete for limited routing space. This gives an early indication of possible routing difficulties.*

---

## Summary

- **Floorplanning** defines the die and core boundaries and prepares the area for cell placement.
- I/O pins, routing tracks, tap cells, and decap cells are handled during floorplan preparation.
- **Global placement** assigns approximate positions while trying to reduce wirelength.
- **Detailed placement** removes overlaps and aligns cells to the placement grid.
- Magic is used to inspect the floorplan and placement visually.
- After placement is checked, the design is ready to proceed to **Clock Tree Synthesis (CTS)**.
