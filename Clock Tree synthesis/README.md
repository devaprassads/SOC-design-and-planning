# Clock Tree, Timing Analysis and Routing

This explains the **physical design** part of making a chip (the steps after the logic is designed). It mainly covers:

1. Timing analysis (setup and hold)
2. Clock Tree Synthesis (CTS), including power aware CTS and crosstalk shielding
3. Routing (Lee's algorithm and TritonRoute)
4. The checks after routing (DRC and parasitics extraction)
5. The power layout of a chip

---

## Table of Contents

1. [Key Terms](#Key-terms)
2. [Timing Analysis with Ideal Clocks](#1-timing-analysis-with-ideal-clocks)
3. [Clock Tree Synthesis (CTS)](#2-clock-tree-synthesis-cts)
4. [Power Aware CTS](#3-power-aware-cts)
5. [Crosstalk and Clock Net Shielding](#4-crosstalk-and-clock-net-shielding)
6. [Timing Analysis with Real Clocks](#5-timing-analysis-with-real-clocks)
7. [Routing](#6-routing)
8. [TritonRoute](#7-tritonroute)
9. [DRC and Parasitics Extraction](#8-drc-and-parasitics-extraction)
10. [Power Planning](#9-power-planning)

---

## Key Terms

| Term | Simple meaning |
|---|---|
| Clock (CLK) | A signal that keeps switching 0 → 1 → 0. Flip flops do their work on its rising edge. |
| Flip flop (flop / FF) | A small memory element. It stores the value of `D` at the clock edge and shows it on `Q`. |
| Launch flop | The flop that sends data out. |
| Capture flop | The flop that receives the data. |
| Combinational logic (the cloud) | Gates between two flops. Data takes some time to go through it. |
| Skew | The difference between the times the clock reaches two flops. |
| Setup time | Data must be stable **before** the clock edge for at least this long. |
| Hold time | Data must stay stable **after** the clock edge for at least this long. |
| Slack | How much extra time is left. Positive means pass, negative means violation. |
| Buffer | A cell that repeats a signal with more driving strength. |
| Net | A wire connecting cells. |

---

## 1. Timing Analysis with Ideal Clocks

### Setup analysis, single clock (ideal)

![3](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/Clock%20Tree%20synthesis/images/3.png)

There are two flops, a **launch flop** and a **capture flop**, with some logic in between. Both get the same clock. "Ideal" means the clock reaches both flops at exactly the same time (no delay).

- Data leaves the launch flop at time `0` (clock edge).
- The data takes time **θ (theta)** to travel through the logic.
- The capture flop grabs the data at the next clock edge, at time `T`.
- It needs the data a little early, by the setup time `S`.

So the condition is:

```
θ < T − S
```

**Calculation:**

```
F = 1 GHz
T = 1 / F = 1 / 1 GHz = 1 ns
S = 10 ps = 0.01 ns

θ < T − S
θ < 1 − 0.01
θ < 0.99 ns
```

So the logic delay must be less than 0.99 ns, otherwise the capture flop misses the data.

### Inside a flop (two muxes)

![4](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/Clock%20Tree%20synthesis/images/4.png)

This shows why setup time exists by looking inside a flop. A flop is made of **two muxes (Mux1 and Mux2)**, and each one is controlled by CLK.

- **When CLK = 0:** Mux1 passes `D` to `QM` (so `QM` follows `D`). Mux2 feeds `Q` back to itself, so `Q` stays the same.
- **When CLK = 1:** Mux1 feeds `QM` back to itself, so it holds the old `D` value. Mux2 passes `QM` to `Q`.

So at the rising edge, whatever `D` was just before the edge gets locked in and appears on `Q`. That is why `D` must be steady a bit before the edge (setup time).

---

## 2. Clock Tree Synthesis (CTS)

### Clock skew

![5](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/Clock%20Tree%20synthesis/images/5.png)

CTS is step 4 in the flow. The clock comes in from the pin `CLK1` and has to reach all flops (here FF1 and FF2).

- `t1` = time for the clock to reach FF1
- `t2` = time for the clock to reach FF2

```
Skew = t2 − t1
```

The goal of CTS is to keep skew close to **0 ps**, so all flops get the clock at nearly the same time. The bottom part shows the circuit being used: FF1 → inverter (1) → AND gate (2) → FF2.

### Clock tree with buffering (before)

![6](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/Clock%20Tree%20synthesis/images/6.png)

The left side is the chip layout (floorplan) and the right side is the same circuit as a schematic. The chip has:

- Input pins `Din1–Din4`, output pins `Dout1–Dout4`, clock pins `CLK1`, `CLK2` and `Clk Out`
- Grey blocks (Block a, b, c, DECAP cells) that are already placed
- Four groups of flops, each shown in its own colour (orange, yellow, blue and green)

The coloured thick lines (yellow for CLK1, purple for CLK2) are the clock wires reaching the flops. Wires are long, so the clock arrives at different times at different flops, which means skew.

### Clock tree with buffering (after)

![7](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/Clock%20Tree%20synthesis/images/7.png)

Here **red buffers** (`Buf`) have been added along the clock wires. Buffers strengthen the signal and help balance the delay to every flop, so the skew gets smaller. Compared with the previous picture, the clock wires now go through several buffers before reaching each flop.

---

## 3. Power Aware CTS

### Clock gating cells

![1](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/Clock%20Tree%20synthesis/images/1.png)

To save power, the clock can be switched off for parts of the chip that are not working. This is done with a gate on the clock, using an enable signal `EN`.

**AND gate (left):** `Y = EN AND CLK`

| EN | CLK | Y |
|---|---|---|
| 0 | 0 | 0 |
| 0 | 1 | 0 |
| 1 | 0 | 0 |
| 1 | 1 | 1 |

- When `EN = 1` (blue box), `Y` follows `CLK` (green box). The clock passes.
- When `EN = 0`, `Y` stays at 0. The clock is stopped.

**OR gate (right):** `Y = EN OR CLK`

| EN | CLK | Y |
|---|---|---|
| 0 | 0 | 0 |
| 0 | 1 | 1 |
| 1 | 0 | 1 |
| 1 | 1 | 1 |

- When `EN = 0` (blue box), `Y` follows `CLK` (green box). The clock passes.
- When `EN = 1`, `Y` stays at 1. The clock is stopped.

So AND gating is active when EN is 1 and OR gating is active when EN is 0.

### Delay tables and buffer tree

![2](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/Clock%20Tree%20synthesis/images/2.png)

**Top part: delay tables.** Each buffer type (CBUF 1 and CBUF 2) has a table. The delay of a buffer depends on two things:

- **Input slew** (how slowly the input signal changes): 20, 40, 60, 80 ps
- **Output load** (how much capacitance it drives): 10, 30, 50, 70, 90, 110 fF

Each box (x1 to x24 for CBUF 1, y1 to y24 for CBUF 2) is the delay for one slew and load pair. The tool looks in these tables to find the delay of a buffer.

**Bottom part: buffer tree.** There are 2 levels of buffering:

- Level 1: buffer 1 drives node `A`
- Level 2: two buffers (both "2") drive nodes `B` and `C`, each connected to 2 flops

Observations: at every level, each node drives the same load, and the buffers at the same level are identical. This keeps the tree balanced.

**Calculation:** assume `C1 = C2 = C3 = C4 = 25 fF` (flop input capacitances) and `Cbuf1 = Cbuf2 = 30 fF` (buffer input capacitances).

```
Node B load = C1 + C2             = 25 + 25 = 50 fF
Node C load = C3 + C4             = 25 + 25 = 50 fF
Node A load = Cbuf1 + Cbuf2       = 30 + 30 = 60 fF
```

Using these loads, the delay of each buffer can be picked from its table.

---

## 4. Crosstalk and Clock Net Shielding

### What can go wrong with a glitch

![8](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/Clock%20Tree%20synthesis/images/8.png)

Two wires that run side by side have a small capacitance between them, called `CM` (coupling capacitance). If the top wire (red) switches, it can pull the neighbouring wire (victim, at point `V`) and cause a small unwanted pulse called a **glitch**.

In this example the glitch goes onto the `RST` (reset) wire going to a memory. A wrong reset can **change the data in the memory**, so the chip behaves incorrectly.

### Impact of crosstalk on skew

![10](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/Clock%20Tree%20synthesis/images/10.png)

Crosstalk does not only make glitches, it can also change the **delay** of a wire.

- Before crosstalk the delay of a clock buffer/wire = `D`
- After crosstalk the delay = `D + Δ`

The picture shows a clock tree (H-tree style) on a chip. Path `L1` (blue) is normal. Path `L2` (yellow) has a segment hit by crosstalk, so it becomes `L2 + Δ`.

**Calculation:**

```
Skew = L1 − (L2 + Δ)
```

If `L1 = L2` (a perfectly balanced tree), then:

```
Skew = L2 − (L2 + Δ) = −Δ    (so the skew size is Δ)
```

So even a perfectly balanced tree gets skew because of crosstalk.

### Clock net shielding

![9](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/Clock%20Tree%20synthesis/images/9.png)

To stop crosstalk on the clock, the clock wires are **shielded**. Extra wires (the yellow double lines on both sides of the clock wire, usually connected to power or ground) are placed next to the clock wires. Now the neighbour of a clock wire is a quiet shield wire instead of a switching signal wire, so crosstalk on the clock is much lower. The layout is the same as the buffered clock tree but with shields added.

---

## 5. Timing Analysis with Real Clocks

### Setup analysis with real clocks

![11](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/Clock%20Tree%20synthesis/images/11.png)

In a real chip, the clock goes through buffers (1, 2, 3, 4) and wires, so the launch and capture flops do **not** get the clock at the same time.

- `Δ1` = clock delay to the launch flop
- `Δ2` = clock delay to the capture flop
- `SU` = setup uncertainty (extra safety margin)

Setup condition:

```
(θ + Δ1) < (T + Δ2) − S − SU
```

- Left side = **Data Arrival Time** (when data reaches the capture flop)
- Right side = **Data Required Time** (the latest it is allowed to arrive)

```
Slack = Data Required Time − Data Arrival Time
```

**Values:**

```
T  = 1 ns
S  = 10 ps = 0.01 ns
SU = 90 ps = 0.09 ns
```

Putting them in:

```
θ + Δ1 < 1 + Δ2 − 0.01 − 0.09
θ + Δ1 < Δ2 + 0.90
```

If both flops get the clock at the same time (`Δ1 = Δ2`), then `θ < 0.90 ns`. This is stricter than the ideal case (0.99 ns) because of the uncertainty margin.

**Example:** `θ = 0.85 ns`, `Δ1 = 0.20 ns`, `Δ2 = 0.25 ns`

```
Arrival  = θ + Δ1                 = 0.85 + 0.20 = 1.05 ns
Required = T + Δ2 − S − SU        = 1 + 0.25 − 0.01 − 0.09 = 1.15 ns
Slack    = 1.15 − 1.05            = +0.10 ns   → setup met
```

### Hold analysis with real clocks

![12](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/Clock%20Tree%20synthesis/images/12.png)

Hold checking is about the data **not changing too early**. The new data launched at the clock edge should not race through the logic and reach the capture flop before it has finished capturing the old data.

Hold condition:

```
θ + Δ1 > H + Δ2 + HU
```

- `H` = hold time of the flop
- `HU` = hold uncertainty

**Values:**

```
H  = 10 ps = 0.01 ns
HU = 50 ps = 0.05 ns
```

```
θ + Δ1 > 0.01 + Δ2 + 0.05
θ + Δ1 > Δ2 + 0.06
```

If `Δ1 = Δ2`, then `θ > 0.06 ns` (the logic must not be faster than 0.06 ns).

**Example:** `θ = 0.05 ns`, `Δ1 = 0.20 ns`, `Δ2 = 0.25 ns`

```
Left  = θ + Δ1          = 0.05 + 0.20 = 0.25 ns
Right = H + Δ2 + HU     = 0.01 + 0.25 + 0.05 = 0.31 ns
Slack = 0.25 − 0.31     = −0.06 ns   → hold violated
```

Here the capture clock is late (`Δ2` bigger than `Δ1`), so the data arrives too early compared with the clock. Hold violations are usually fixed by adding delay buffers on the data path.

Note: setup is checked against the **next** clock edge (`T`), but hold is checked against the **same** edge (time 0). This is why changing `T` fixes setup problems but does not fix hold problems.

### Finding Δ1 and Δ2 from the layout

![13](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/Clock%20Tree%20synthesis/images/13.png)

This shows how to find `Δ1` and `Δ2` from the actual layout. Each one is the sum of the delays along the clock path from the clock pin to the flop. The path alternates between wire and buffer:

```
Δ = wire RC delay 1 + buffer delay
  + wire RC delay 2 + buffer delay
  + wire RC delay 3 + buffer delay
  + ... + last wire RC delay
```

- `Δ2` is the clock path to the capture flop (it goes through more buffers, shown in the upper list).
- `Δ1` is the clock path to the launch flop (lower list).
- The two circled groups of flops are the timing paths being checked in this example.

The formula at the bottom left is the same hold check as above.

---

## 6. Routing

### Maze routing, Lee's algorithm

![14](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/Clock%20Tree%20synthesis/images/14.png)

Step 5 of the flow is **Route**, which means drawing the real wires between pins. Lee's algorithm (1961) is a simple method to find the shortest path on a grid.

How it works:

1. Pick a **source** (S) and a **target** (T) on the grid.
2. Starting from the source, label the neighbouring cells 1, then their neighbours 2, then 3, and so on. This spreads out like a wave.
3. Stop when the wave reaches the target.
4. **Backtrace**: go back from the target by always moving to a cell with a number one less, until the source is reached. This is the shortest path (red line).

Cells that are blocked (the grey blocks and DECAPs) are skipped, so the wave goes around them.

---

## 7. TritonRoute

TritonRoute is a **detailed router** (an open-source one). 

### Overview

![18](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/Clock%20Tree%20synthesis/images/18.png)

Main points:

- It does the **initial detailed route**.
- It follows the **preprocessed route guides** (made after the earlier "fast route" step) as much as possible.
- It assumes the guides for each net are properly connected (**inter-guide connectivity**).
- It uses a **MILP** (a type of math optimization) based **panel routing** scheme, with **intra-layer parallel** and **inter-layer sequential** routing.

### Preprocessed route guides

![19](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/Clock%20Tree%20synthesis/images/19.png)

A route guide is like a rough area (a box) telling the router where a net should go. The picture shows how raw guides are cleaned up in steps:

- (a) **Initial route guides**: the first rough boxes from point A to point B
- (b) **Splitting**: big guides are cut into unit-width pieces
- (c) **Merging**: neighbouring pieces going in the same direction are joined
- (d) **Bridging**: extra guides on the other layer are added where the direction changes
- (e) **Preprocessed guides**: the final cleaned result

Blue is the M1 layer and orange/red is the M2 layer. Preferred direction is vertical for M1 and horizontal for M2.

Requirements of preprocessed guides:

- They should have **unit width**.
- They should be in the **preferred direction** of the layer.

### Inter-guide connectivity

![20](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/Clock%20Tree%20synthesis/images/20.png)

Two guides count as connected if:

- they are on the **same metal layer** and their **edges touch**, or
- they are on **neighbouring layers** and **overlap** (with a non-zero overlapping area).

Also, every unconnected pin of a standard cell must be **overlapped by a route guide**, so that the router can reach it.

### Panel routing

![21](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/Clock%20Tree%20synthesis/images/21.png)

The chip is split into long thin strips called **panels** on each metal layer.

- **Intra-layer parallel:** panels on the same layer are routed at the same time (in parallel), alternating even and odd panels so neighbours do not clash.
- **Inter-layer sequential:** layers are routed one after another, going up: first M2, then M3, and so on.

In the picture: (a) all panels on M2 are routed in parallel, (b) then even panels on M3, and (c) then odd panels on M3.

### Problem statement

![22](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/Clock%20Tree%20synthesis/images/22.png)

| | |
|---|---|
| **Inputs** | LEF (cell/library info), DEF (design/placement info), preprocessed route guides |
| **Output** | Detailed routing with optimized wire length and via count (vias are connections between layers) |
| **Constraints** | Follow the route guides, keep nets connected, obey design rules |

### Handling connectivity (access points)

![23](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/Clock%20Tree%20synthesis/images/23.png)

To connect things, the router uses **access points**.

- **Access Point (AP):** a point on the routing grid inside a guide, used to connect to a lower-layer segment, an upper-layer segment, a pin, or an IO port.
- **Access Point Cluster (APC):** the group of all APs that come from the same lower-layer segment, upper-layer guide, pin or IO port.

The picture shows access points (a) to a lower layer segment, (b) to a pin shape and (c) to an upper layer.

### Routing topology algorithm

![24](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/Clock%20Tree%20synthesis/images/24.png)

After the access point clusters are known, the router has to decide **which clusters to join together** with the least wire. The algorithm is:

```
for i = 1 to n-1:
    for j = i+1 to n:
        cost(i,j) = distance(APC_i, APC_j)
T = MST(APCs, COSTs)      # minimum spanning tree
return the edges in T
```

In simple words:

1. Calculate the distance between **every pair** of clusters (this is the cost).
2. Build a **Minimum Spanning Tree (MST)**, which connects all clusters with the smallest total distance and no loops.
3. The edges of this tree are the connections to be routed.

---

## 8. DRC and Parasitics Extraction

### DRC clean (step 6)

![15](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/Clock%20Tree%20synthesis/images/15.png)

After routing, the dark green signal wires are on the chip. **DRC (Design Rule Check)** makes sure the layout follows the manufacturing rules. Some typical design rules apply to a pair of wires, and one of them is shown, the **wire pitch** (the distance between two parallel wires). If wires are too close the chip cannot be made reliably. A layout with no rule violations is called **DRC clean**.

### Parasitics extraction (step 7)

![16](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/Clock%20Tree%20synthesis/images/16.png)

Real wires are not perfect. Every wire has some **resistance (R)** and **capacitance (C)**, called parasitics. In the picture, small resistor and capacitor symbols are drawn on the wires. These values are extracted from the final layout and are then used for timing analysis (like the wire RC delays above), so the timing numbers match the real chip.

---

## 9. Power Planning

### Chip power layout

![17](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/Clock%20Tree%20synthesis/images/17.png)

This image labels the parts of a chip's layout and its power network.

- **Pad frame / I/O and corner pads:** the yellow pads around the edge. Some are for signals and the red/blue ones are for power.
- **VDD (red) and GND (blue):** the power and ground wires. They form a **ring** around the core.
- **Power stripes:** vertical lines that bring power from the ring into the middle of the chip.
- **Standard cell rows:** the green area where the small logic cells (standard cells) sit in rows. Each row has its own thin VDD/GND lines (**standard cell power connections**).
- **Macro cell (RAM):** a big pre-made block with its own **block power ring** and a **block halo** (keep-out space around it).
- **I/O to core spacing:** the gap between the pads and the core, where the rings run.
- **Power pad connections:** link the power pads to the rings.

The power network makes sure every cell gets VDD and GND.

---

## Summary

- **Timing:** setup says data must arrive early enough (`θ + Δ1 < T + Δ2 − S − SU`), hold says it must not arrive too early (`θ + Δ1 > H + Δ2 + HU`).
- **CTS:** builds a buffered clock tree to keep skew near 0. Clock gating saves power and shielding reduces crosstalk.
- **Routing:** Lee's algorithm is the basic idea, and TritonRoute does detailed routing using route guides, access points and an MST.
- **Sign off checks:** DRC (follows the rules?) and parasitics extraction (real RC for timing).
