# Topic 3: Custom Library Cell Design and SPICE Characterization

This part of the project focuses on designing a CMOS inverter from scratch and preparing it for use in a digital chip design. The layout is created in **Magic** using the **130 nm process**, checked with DRC, and then used for circuit simulation and library generation.

The main aim is to understand how a standard cell is designed, tested, and represented so that physical-design tools can use it.

## Table of Contents

1. [Where this fits in the flow](#1-where-this-fits-in-the-flow)
2. [Tools and basic terms](#2-tools-and-basic-terms)
3. [Step-by-step work](#3-step-by-step-work)
4. [Understanding the timing calculations](#4-understanding-the-timing-calculations)
5. [Why LEF is generated](#5-why-lef-is-generated)
6. [Summary](#6-summary)

---

## 1. Where this fits in the flow

```text
CMOS schematic / transistor design
              ↓
       Layout in Magic
              ↓
       DRC and extraction
              ↓
     SPICE simulation in ngspice
              ↓
   Timing measurements and library files
              ↓
      LEF for physical design
```

A schematic describes the electrical connections. A layout shows the physical shapes that will be manufactured. The layout must follow the process rules, and the extracted circuit is simulated to check its behaviour.

## 2. Tools and basic terms

| Term | Simple meaning |
|---|---|
| **Magic** | Tool used to draw and inspect the physical layout. |
| **DRC** | Design Rule Check; checks whether the layout follows manufacturing rules. |
| **Parasitics** | Unwanted resistance and capacitance introduced by the physical layout. |
| **ngspice** | Circuit simulator used to test the inverter's electrical response. |
| **Rise time** | Time taken by the output to rise from a low level to a high level, using the chosen measurement limits. |
| **Fall time** | Time taken by the output to fall from a high level to a low level, using the chosen measurement limits. |
| **Propagation delay** | Time between an input transition and the corresponding output transition. |
| **LEF** | A simplified description of a cell's size, pins and physical layout information for place-and-route tools. |
| **Liberty (`.lib`)** | File containing timing and electrical information about a cell. |

---

## 3. Design and characterization workflow

The screenshots below follow the original workflow. Each image opens from the corresponding file in the project repository.

### 3.1 Setting up the custom cell

![Custom inverter design screenshot](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/56.png)

This screenshot records the start of this part of the workflow. The aim is to create a custom inverter cell rather than use an already designed cell.

![Custom inverter design screenshot](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/57.png)

This continues the setup for the custom-cell design. The required process and design environment are used for the layout work.

![Custom inverter design screenshot](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/58.png)

The inverter design process continues. The PMOS and NMOS devices must be arranged so that the circuit can produce the inverted version of its input.

### 3.2 Creating and checking the layout

![Custom inverter design screenshot](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/59.png)

This screenshot is part of the layout creation process. In a CMOS inverter, the PMOS connects to the positive supply and the NMOS connects to ground; their gates share the input and their drains form the output.

![Custom inverter design screenshot](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/60.png)

The layout work continues, including the physical shapes and connections required by the cell.

![Custom inverter design screenshot](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/61.png)

This step continues arranging the cell geometry. The layout must connect the transistor terminals correctly while keeping separate nets electrically isolated.

![Custom inverter design screenshot](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/62.png)

The physical layout is refined. Metal layers are used to connect the device terminals and provide the cell's input, output and supply connections.

![Custom inverter design screenshot](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/63.png)

This screenshot belongs to the layout construction and verification stage. Correct connections and process-rule spacing are important before simulation.

![Custom inverter design screenshot](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/64.png)

The cell layout is further checked and adjusted as needed. A layout that looks correct still needs a Design Rule Check.

![Custom inverter design screenshot](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/65.png)

This is another step in completing the inverter layout. The goal is to make the layout both electrically correct and compliant with the 130 nm process rules.

![Custom inverter design screenshot](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/66.png)

The layout verification work continues. DRC helps identify issues such as insufficient spacing or incorrect layer dimensions.

![Custom inverter design screenshot](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/67.png)

This screenshot continues the layout and checking sequence before the circuit is prepared for electrical simulation.

![Custom inverter design screenshot](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/68.png)

The completed physical geometry is used for the next stage. The layout contains parasitic resistance and capacitance, which can affect the real circuit response.

### 3.3 Extracting the circuit and preparing simulation

![Custom inverter design screenshot](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/69.png)

This step moves the work toward extraction and simulation. Extraction produces a circuit representation based on the layout.

![Custom inverter design screenshot](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/70.png)

The extracted circuit is prepared for use in SPICE. Including parasitics makes the simulation closer to the behaviour of the physical layout.

![Custom inverter design screenshot](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/71.png)

This screenshot continues the simulation setup. The inverter is tested by applying an input signal and observing the output.

![Custom inverter design screenshot](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/72.png)

The circuit setup is continued before running or inspecting the transient response in ngspice.

![Custom inverter design screenshot](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/73.png)

This stage is part of checking the inverter's simulated behaviour. For a working inverter, a high input should produce a low output and a low input should produce a high output.

![Custom inverter design screenshot](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/74.png)

The simulation process continues. The input and output waveforms can be compared to measure the switching response.

![Custom inverter design screenshot](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/75.png)

This screenshot is part of the circuit-characterization sequence. Timing measurements are taken from the transient waveform using defined voltage thresholds.

![Custom inverter design screenshot](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/76.png)

The inverter's response is examined further. Rise time, fall time and propagation delay describe different parts of the switching behaviour.

![Custom inverter design screenshot](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/77.png)

This step continues the characterization or its supporting setup. The measured timing values can later be stored in a cell library.

![Custom inverter design screenshot](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/78.png)

The simulation and measurement workflow continues. These results help describe how quickly the custom inverter switches under the tested conditions.

### 3.4 Preparing the cell for design tools

![Custom inverter design screenshot](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/79.png)

This screenshot is part of preparing the custom cell's library information. Physical layout data and timing data serve different purposes, so they are represented in different formats.

![Custom inverter design screenshot](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/80.png)

The cell information is further prepared for use in the digital design flow. The library must identify the cell and its terminals consistently.

![Custom inverter design screenshot](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/81.png)

This step continues the library or layout export process. The physical abstract allows placement and routing tools to understand the cell without loading every internal layout shape.

![Custom inverter design screenshot](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/82.png)

The cell export and verification work continues. It is important that the cell dimensions and pin names agree with the layout used by the flow.

![Custom inverter design screenshot](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/83.png)

This screenshot belongs to the final checks of the custom-cell files. These files allow the inverter to be referenced by later design stages.

![Custom inverter design screenshot](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/84.png)

The workflow continues toward completing the cell deliverables. The physical abstract and electrical/timing model must match the same cell.

![Custom inverter design screenshot](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/85.png)

This stage is part of verifying or organizing the generated cell information before it is used by the larger design flow.

![Custom inverter design screenshot](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/86.png)

The final preparation steps continue. At this point, the aim is to make the custom inverter usable by the physical-design tools.

![Custom inverter design screenshot](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/87.png)

This screenshot shows a later stage in the custom-cell workflow. The generated files are the link between the hand-designed inverter and the automated chip-design flow.

![Custom inverter design screenshot](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/88.png)

This is the last screenshot included for this topic. It marks the end of the documented custom-cell design and characterization sequence.

> **Note:** The screenshot captions above explain the purpose of each stage in the overall flow. The original images are linked directly from the repository; exact command outputs or numerical results should only be added where they are visible in the screenshot.

---

## 4. Understanding the timing calculations

The exact values depend on the waveform and voltage thresholds used in the simulation. The common calculations are:

- **Rise time:**

  `Rise time = time at 90% of output swing − time at 10% of output swing`

- **Fall time:**

  `Fall time = time at 10% of output swing − time at 90% of output swing`

- **Propagation delay:** measured between the input transition and the corresponding output transition, usually at the 50% voltage points.

For example, if the output crosses the 10% level at `2 ns` and the 90% level at `3 ns`, then:

```text
Rise time = 3 ns − 2 ns = 1 ns
```

For an inverter, input and output transitions go in opposite directions. Therefore, the low-to-high output delay and high-to-low output delay are measured separately when required. No specific measured values are stated here because they must be read from the actual simulation waveform.

---

## 5. Why LEF is generated

The full layout contains detailed shapes for diffusion, polysilicon, metal and other layers. Place-and-route tools usually do not need all those details at every step. A **LEF file** provides a simpler physical view of the cell, including its size and pin information.

The timing and electrical behaviour is generally described separately in a **Liberty (`.lib`) file**. Together, these files help the design tools place the cell and estimate its timing.

---

## 6. Summary

- A CMOS inverter is created as a custom layout in Magic.
- DRC is used to check the layout against process rules.
- Parasitic resistance and capacitance are extracted from the layout.
- ngspice is used to simulate the inverter's transient response.
- Rise time, fall time and propagation delay describe the switching performance.
- LEF provides the physical information needed by place-and-route tools, while Liberty files describe timing and electrical characteristics.

This exercise connects transistor-level design with the standard-cell files used in a larger digital chip-design flow.
