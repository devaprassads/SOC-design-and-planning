# 3. Custom Cell Design

## Purpose

This section documents the custom CMOS inverter work in the project. A custom cell is a logic component designed at transistor and layout level rather than used only as an abstract logic symbol.

> **Flow note:** Custom-cell design is normally completed before synthesis if the cell is intended to be included in the synthesis library. It is listed as section 3 here to match the requested documentation order; conceptually, its library and timing information can be prerequisites for synthesis.

## Theory

### CMOS inverter

A CMOS inverter uses PMOS and NMOS transistors to reverse a digital input:
- Input LOW produces output HIGH.
- Input HIGH produces output LOW.

It is a fundamental logic cell and a useful example for learning transistor-level design and physical layout.

### Layout and design-rule checking

A layout describes the physical shapes used to create transistors and interconnects. Design Rule Checking (DRC) checks whether these shapes follow the manufacturing geometry rules. Passing DRC does not by itself prove that the circuit behaves correctly.

### Extraction and SPICE simulation

Parasitic extraction estimates electrical effects such as resistance and capacitance from the layout. SPICE simulation can then be used to study the circuit's electrical response, including output transitions and delay.

### Timing and cell views

A cell may need different representations for different tools, such as a physical layout view and timing information. The exact files needed depend on how the cell is integrated into the design flow.

## Project Files

- [Custom Cell folder](./Custom%20Cell/)
- [Custom-cell README](./Custom%20Cell/README.md)
- [Custom-cell files](./Custom%20Cell/files/)

## What to Check

- The inverter's input/output behaviour.
- DRC results.
- Simulation waveforms and measured delay.
- Extracted parasitic effects and the cell files produced.

## Why It Matters

Standard-cell libraries are made from characterized logic cells. Understanding a custom inverter gives a practical link between transistor-level CMOS design and the cells used by synthesis and physical-design tools.
