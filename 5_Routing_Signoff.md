# 5. Routing and Sign-off Checks

## Purpose

Routing creates the physical metal connections between cells. Later checks evaluate the routed implementation, including the electrical effects of wires and whether timing requirements are met.

## Theory

### Routing

The netlist tells the tool which pins must be electrically connected. Routing chooses physical paths through the available metal layers while following design rules and avoiding conflicts with other wires.

Routing quality affects wire length, congestion, parasitic capacitance and resistance, and signal delay.

### Parasitic extraction

Real wires have resistance and capacitance. Parasitic extraction estimates these properties from the physical implementation. Timing analysis can use this information to estimate signal delays more accurately than idealized pre-route estimates.

### Design-rule and connectivity checks

Physical verification can include checks that wires follow layout rules and that required connections are complete. The exact checks performed depend on the flow and reports available in the project.

### Timing after routing

Post-route timing includes more realistic interconnect effects. A path that looked acceptable earlier may have less slack after routing, so the routed timing reports should be reviewed.

## Project Files and Results

- [Routing folder](./Routing/)
- [Routing results](./results/routing/)
- [Routing reports](./reports/routing/)
- [Full-flow screenshots](./full%20flow/)

## What to Check

- Whether routing completed and connections were made.
- Any routing congestion or design-rule issues shown in reports.
- Extracted parasitic information, where available.
- Post-route setup and hold timing.
- Any sign-off checks actually reported by the flow.

## Sign-off Note

A routed layout and timing report do not automatically mean a chip is ready for manufacturing. Tape-out normally requires all required physical-verification, timing, electrical, and project-specific sign-off checks to pass. Only claim sign-off completion when the corresponding reports confirm it.
