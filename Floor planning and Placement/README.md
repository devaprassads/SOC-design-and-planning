# CMOS Inverter Layout and Timing Characterization

## Contents

1. [Design and layout exploration](#1-design-and-layout-exploration)
2. [Preparing and running the SPICE simulation](#2-preparing-and-running-the-spice-simulation)
3. [Reading the waveform and measuring timing](#3-reading-the-waveform-and-measuring-timing)
4. [Summary of measured values](#4-summary-of-measured-values)

---

## 1. Design and layout exploration

These show the setup and the magic layout view of the picorv32a. 

### 1.1 Setup and initial layout inspection

![Screenshot 1 — setup](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/1.png)

The terminal shows the initial setup commands for the standard-cell design environment.

![Screenshot 2 — setup output](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/2.png)

The terminal output 

![Screenshot 3 — file or directory listing](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/3.png)

![Screenshot 4 — file listing](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/4.png)

![Screenshot 5 — design preparation](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/5.png)

![Screenshot 6 — tabular output](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/6.png)

![Screenshot 7 — terminal output](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/7.png)

### 1.2 Magic Layout of picorv32a

![Screenshot 8 — layout overview](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/8.png)

Cells in picorv32a

![Screenshot 9 — layout objects](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/9.png)

metal layer 3

![Screenshot 10 — layout inspection](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/10.png)

metal layer 2

![Screenshot 11 — layout operation](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/11.png)

Decap and tap cells

![Screenshot 12 — layout view](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/12.png)

Diagonally equidistant Tap cells

![Screenshot 13 — repeated layout structures](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/13.png)

Subcell instance 

![Screenshot 14 — geometry inspection](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/14.png)

screen shot of run_placement

![Screenshot 15 — terminal commands](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/15.png)

Screenshots of floorplan def in magic

![Screenshot 16 — layout overview](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/16.png)

Standard cells legally placed

![Screenshot 17 — layer and connectivity view](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/17.png)

Clone custom inverter standard cell design from github repository

![Screenshot 18 — terminal output](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/18.png)

Screenshot of custom inverter layout in magic

![Screenshot 19 — inverter layout](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/19.png)

NMOS and PMOS identified

![Screenshot 20 — inverter layout inspection](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/20.png)
![Screenshot 21 — inverter geometry](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/21.png)

Output Y connectivity to PMOS and NMOS drain verified

![Screenshot 22 — layout information](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/22.png)

PMOS source connectivity to VDD (here VPWR) verified.

![Screenshot 23 — supply and device regions](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/23.png)

NMOS source connectivity to VSS (here VGND) verified

![Screenshot 24 — layout inspection output](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/24.png)

Deleting necessary layout part to see DRC error

![Screenshot 25 — power rail and device regions](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/25.png)

Spice extraction of inverter in magic

![Screenshot 26 — terminal/editor transition](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/26.png)

Screenshot of created spice file

![Screenshot 27 — command or script view](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/27.png)

Measuring unit distance in layout grid

![Screenshot 28 — zoomed layout](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/28.png)

Final edited spice file ready for ngspice simulation

![Screenshot 29 — simulation preparation](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/29.png)

---

## 2. Preparing and running the SPICE simulation

The following screenshots show the transient simulation of the inverter using `ngspice` and the generated `sky130` inverter SPICE file.

![Screenshot 30 — launching ngspice and plotting output](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/30.png)

The terminal runs `ngspice` on the inverter SPICE file and plots output node `y` against time and input node `a`. The simulation reports 160 data rows.

![Screenshot 31 — transient waveform](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/31.png)

The waveform shows the input and output switching over time. The inverter output changes in the opposite direction to the input, as expected for an inverter.

![Screenshot 32 — zoomed transition](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/32.png)

The waveform is zoomed into a switching edge so cursor measurements can be taken at specific voltage levels.

---

## 3. Reading the waveform and measuring timing

The supply/output swing shown in the simulation is approximately 0–3.3 V. Therefore:

- 20% level: \(0.20 \times 3.3\,V = 0.66\,V\) (approximately 660 mV)
- 80% level: \(0.80 \times 3.3\,V = 2.64\,V\)
- 50% level: \(0.50 \times 3.3\,V = 1.65\,V\)

### 3.1 Rise transition time

![Screenshot 33 — cursor readouts for transition levels](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/33.png)

The cursor readouts identify the rising output transition's 20% and 80% crossing times. Using the readouts visible in the screenshot:

- Output at 20% (about 0.66 V): \(t_{20\%} = 2.18242\,ns\)
- Output at 80% (about 2.65 V): \(t_{80\%} = 2.24731\,ns\)

![Screenshot 34 — zoom into rising edge](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/34.png)

This view focuses on the rising output edge used for the transition-time measurement.

![Screenshot 35 — time-axis detail for rising edge](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/35.png)

The time axis is zoomed to read the cursor positions around the rising edge.

**Calculation — rise transition time**

\[
t_r = t_{80\%} - t_{20\%}
\]

\[
t_r = 2.24731\,ns - 2.18242\,ns = 0.06489\,ns
\]

\[
\boxed{t_r = 64.89\,ps}
\]

### 3.2 Fall transition time

![Screenshot 36 — cursor readouts around switching edges](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/36.png)

The cursor readouts show the voltage crossing times used for the falling output transition and for the cell-delay measurements.

![Screenshot 37 — zoom into falling edge](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/37.png)

This view focuses on the falling output edge.

![Screenshot 38 — terminal and cursor measurements](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/38.png)

The terminal displays cursor measurements associated with the plotted waveform.

![Screenshot 39 — zoomed falling output transition](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/39.png)

The waveform is zoomed around the falling edge to locate the 80% and 20% output crossings.

![Screenshot 40 — cursor readouts for falling transition](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/40.png)

The readouts show the falling output transition at approximately 80% and 20% of the output swing:

- Output at 80% (about 2.65 V): \(t_{80\%} = 4.05220\,ns\)
- Output at 20% (about 0.66 V): \(t_{20\%} = 4.09544\,ns\)

**Calculation — fall transition time**

For a falling edge, measure from the 80% crossing to the later 20% crossing:

\[
t_f = t_{20\%} - t_{80\%}
\]

\[
t_f = 4.09544\,ns - 4.05220\,ns = 0.04324\,ns
\]

\[
\boxed{t_f = 43.24\,ps}
\]

### 3.3 Rise cell propagation delay

![Screenshot 41 — waveform detail for propagation delay](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/41.png)

This view shows the switching edge used to compare the input and output 50% voltage crossings.

![Screenshot 42 — cursor values for propagation delay](https://raw.githubusercontent.com/devaprassads/SOC-design-and-planning/main/full%20flow/42.png)

The cursor readouts give the approximate 50% crossing times used for the rise cell delay:

- Input falling through 50% (about 1.65 V): \(t_{in,50\%} = 2.14962\,ns\)
- Output rising through 50% (about 1.65 V): \(t_{out,50\%} = 2.21038\,ns\)

**Calculation — rise cell delay**

\[
t_{pLH} = t_{out,50\%} - t_{in,50\%}
\]

\[
t_{pLH} = 2.21038\,ns - 2.14962\,ns = 0.06076\,ns
\]

\[
\boxed{t_{pLH} = 60.76\,ps}
\]

### 3.4 Fall cell propagation delay

The same cursor-readout screenshot also provides the 50% crossing values for the falling output edge:

- Input rising through 50% (about 1.65 V): \(t_{in,50\%} = 4.04909\,ns\)
- Output falling through 50% (about 1.65 V): \(t_{out,50\%} = 4.07709\,ns\)

**Calculation — fall cell delay**

\[
t_{pHL} = t_{out,50\%} - t_{in,50\%}
\]

\[
t_{pHL} = 4.07709\,ns - 4.04909\,ns = 0.02800\,ns
\]

\[
\boxed{t_{pHL} = 28.00\,ps}
\]

---

## 4. Summary of measured values

| Measurement | Calculation | Result |
|---|---|---:|
| Rise transition time | \(2.24731 - 2.18242\,ns\) | **64.89 ps** |
| Fall transition time | \(4.09544 - 4.05220\,ns\) | **43.24 ps** |
| Rise cell delay | \(2.21038 - 2.14962\,ns\) | **60.76 ps** |
| Fall cell delay | \(4.07709 - 4.04909\,ns\) | **28.00 ps** |

These values are calculated from the cursor coordinates visible in the supplied archive screenshots. Small differences from previously typed values can occur when a different cursor readout or rounded value is used. For consistency, this README uses the readouts transcribed from the original ZIP images.
