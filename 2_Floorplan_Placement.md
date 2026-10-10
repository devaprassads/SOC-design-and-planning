# 2. Floorplanning and Placement

## Purpose

Floorplanning defines the main layout area and its organization. Placement then assigns physical locations to the standard cells inside that area.

## Theory

### Floorplanning

Floorplanning establishes the core area and the initial structure of the design. Depending on the flow, it can include die/core dimensions, I/O placement, macro placement, and power planning.

The core must have enough space for cells and routing. A very crowded floorplan can make placement and routing difficult; an unnecessarily large floorplan can waste area.

### Utilization

Utilization describes how much of the core area is occupied by standard cells. Higher utilization can reduce area but may increase congestion and make routing harder. Lower utilization leaves more space for wires but may increase the overall core size.

### Placement

Placement decides where each standard cell sits. Tools try to place cells to balance wire length, congestion, timing, and area. The cells are not yet fully connected by final physical wires at this stage.

### Why placement affects timing

Longer wires usually add resistance and capacitance, which can increase signal delay. Placement quality therefore affects how easy it is to route the design and meet timing later.

## Project Files and Results

- [Floorplanning and Placement folder](./Floor%20planning%20and%20Placement/)
- [Floorplan results](./results/floorplan/)
- [Placement results](./results/placement/)
- [Floorplan reports](./reports/floorplan/)
- [Full-flow screenshots](./full%20flow/)

## What to Check

- Core area and utilization.
- Standard-cell placement.
- Congestion or placement warnings, if reported.
- Timing and wire-length estimates available at this stage.

## Output

The output is a floorplanned design with standard cells assigned physical locations. It becomes the basis for clock-tree synthesis and later routing.
