# 4. Clock Tree Synthesis and Timing

## Purpose

Clock Tree Synthesis (CTS) builds a network to distribute the clock signal to sequential cells. Timing analysis checks whether data signals arrive within the required time limits.

## Theory

### Why a clock tree is needed

Flip-flops and other sequential elements use a clock to coordinate when they capture data. Because a real chip has many cells spread across an area, the clock needs a physical distribution network.

CTS inserts and arranges clock buffers as needed to drive the clock loads and manage arrival times.

### Clock latency and skew

- **Clock latency** is the time taken for the clock to reach a point in the design.
- **Clock skew** is the difference in clock arrival times between relevant sequential elements.

Excessive skew can make timing harder to meet.

### Static Timing Analysis (STA)

STA checks timing paths using cell timing models, interconnect estimates, and timing constraints. It does not need to simulate every possible input sequence.

Two important checks are:
- **Setup:** Data must arrive early enough before the capturing clock edge.
- **Hold:** Data must remain stable long enough after the relevant clock edge.

### Slack

Slack measures the margin between the required time and the actual arrival time. Positive slack generally means the reported check meets its requirement; negative slack indicates a timing violation for that check. Always interpret slack with the relevant constraints and report stage.

## Project Files and Results

- [CTS Timing folder](./CTS%20Timing/)
- [Clock Tree Synthesis folder](./Clock%20Tree%20synthesis/)
- [CTS results](./results/cts/)
- [Synthesis reports](./reports/synthesis/)
- [Floorplan reports](./reports/floorplan/)
- [Routing reports](./reports/routing/)

## What to Check

- Clock-tree implementation and buffer insertion.
- Clock latency and skew, if reported.
- Setup and hold slack.
- Whether timing results are from synthesis, placement, post-CTS, or routed design.

## Output

CTS produces a clock distribution network. STA reports show which timing paths meet their constraints and which need further investigation.
