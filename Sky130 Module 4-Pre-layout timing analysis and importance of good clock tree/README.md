# SKY130 Module 4: Pre-Layout Timing Analysis & Clock Tree Synthesis

## Overview

This module covers the fundamentals of timing analysis and clock-tree design in a SKY130-based RTL-to-GDSII physical design flow.

The main focus areas are:

- Standard-cell libraries and timing characterization
- OpenLane physical-design flow
- Clock buffering and buffer delay
- Power-aware Clock Tree Synthesis (CTS)
- Setup and hold timing analysis
- Ideal-clock and real-clock timing analysis
- Clock skew and insertion delay
- Crosstalk-induced delta delay
- Timing closure

---

## Table of Contents

- [1. SKY130 Standard-Cell Environment](#1-sky130-standard-cell-environment)
- [2. OpenLane Physical Design Flow](#2-openlane-physical-design-flow)
- [3. Power-Aware Clock Tree Synthesis](#3-power-aware-clock-tree-synthesis)
- [4. Clock Buffer Delay Characterization](#4-clock-buffer-delay-characterization)
- [5. Clock Buffer Tree](#5-clock-buffer-tree)
- [6. Timing Analysis](#6-timing-analysis)
- [7. Setup Timing Analysis](#7-setup-timing-analysis)
- [8. Hold Timing Analysis](#8-hold-timing-analysis)
- [9. Clock Tree Synthesis](#9-clock-tree-synthesis)
- [10. Crosstalk Delta Delay and Clock Skew](#10-crosstalk-delta-delay-and-clock-skew)
- [11. Timing Analysis with Real Clocks](#11-timing-analysis-with-real-clocks)
- [12. Timing Equations](#12-timing-equations)
- [13. Timing Closure Flow](#13-timing-closure-flow)
- [14. Key Takeaways](#14-key-takeaways)
- [15. Important Keywords](#15-important-keywords)

---

# 1. SKY130 Standard-Cell Environment

The SKY130 process design kit (PDK) provides the technology files, standard-cell libraries, physical abstracts, and timing models required for digital physical design.

Common files used in the flow include:

| File | Purpose |
|---|---|
| `.lib` | Timing, power, and functional information |
| `.lef` | Physical abstract of cells |
| `.mag` | Magic layout representation |
| `.v` | Verilog netlist/source |
| `.sdc` | Timing constraints |

A typical SKY130 standard-cell library contains cells such as:

- Inverters
- Buffers
- Logic gates
- Flip-flops
- Clock-gating cells
- Tie cells
- Filler cells
- Antenna-related cells

### Example Magic Command

```bash
magic -T sky130A.tech sky130_inv.mag
```

The exact technology file and layout filename depend on the local SKY130 installation.

---

# 2. OpenLane Physical Design Flow

OpenLane provides an automated RTL-to-GDSII implementation flow.

The major stages are:

```text
RTL
 |
 v
Synthesis
 |
 v
Floorplanning
 |
 v
Placement
 |
 v
Clock Tree Synthesis
 |
 v
Routing
 |
 v
Parasitic Extraction
 |
 v
Static Timing Analysis
 |
 v
DRC / LVS / Signoff
 |
 v
GDSII
```
<img width="837" height="517" alt="Screenshot 2026-09-25 162955" src="https://github.com/user-attachments/assets/07675ae6-7f4d-4452-a2c5-7bd5e696875b" />

## Interactive Flow

OpenLane can be operated interactively using its flow script.

Example:

```bash
./flow.tcl -interactive
```

A design can then be prepared before running individual physical-design stages.

Example:

```tcl
prep -design <design_name> -tag <tag_name> -overwrite
```

Typical stages include:

```tcl
run_synthesis
run_floorplan
run_placement
```

CTS and routing are subsequently performed as part of the physical-design flow.

> **Note:** OpenLane commands and configuration variables can vary between OpenLane versions. Use the syntax corresponding to the installed version.

---

# 3. Power-Aware Clock Tree Synthesis

Clock networks are among the major contributors to dynamic power because the clock signal switches continuously and drives a large number of sequential elements.

Power-aware CTS attempts to balance timing requirements with clock-tree power.

Important parameters include:

- Clock load
- Buffer size
- Number of buffers
- Clock slew
- Clock insertion delay
- Clock skew
- Wire capacitance
- Switching activity
- Clock gating


## Clock Gating

Clock gating prevents unnecessary clock switching in inactive portions of a design.

A simplified AND-based clock-gating relationship is:

```text
Y = EN & CLK
```

Truth table:

| EN | CLK | Y |
|---:|---:|---:|
| 0 | 0 | 0 |
| 0 | 1 | 0 |
| 1 | 0 | 0 |
| 1 | 1 | 1 |

In practical ASIC designs, dedicated integrated clock-gating cells are preferred over directly implementing clock gating with arbitrary combinational logic because clock glitches can cause functional problems.

---

# 4. Clock Buffer Delay Characterization

Clock-buffer delay depends mainly on:

1. Input slew
2. Output load capacitance

For example, a characterized buffer may have timing data for:

### Output Load

```text
10 fF
30 fF
50 fF
70 fF
90 fF
110 fF
```

### Input Slew

```text
20 ps
40 ps
60 ps
80 ps
```

## CBUF1 Delay Table

| Input Slew / Output Load | 10 fF | 30 fF | 50 fF | 70 fF | 90 fF | 110 fF |
|---|---:|---:|---:|---:|---:|---:|
| 20 ps | x1 | x2 | x3 | x4 | x5 | x6 |
| 40 ps | x7 | x8 | x9 | x10 | x11 | x12 |
| 60 ps | x13 | x14 | x15 | x16 | x17 | x18 |
| 80 ps | x19 | x20 | x21 | x22 | x23 | x24 |

## CBUF2 Delay Table

| Input Slew / Output Load | 10 fF | 30 fF | 50 fF | 70 fF | 90 fF | 110 fF |
|---|---:|---:|---:|---:|---:|---:|
| 20 ps | y1 | y2 | y3 | y4 | y5 | y6 |
| 40 ps | y7 | y8 | y9 | y10 | y11 | y12 |
| 60 ps | y13 | y14 | y15 | y16 | y17 | y18 |
| 80 ps | y19 | y20 | y21 | y22 | y23 | y24 |

### Important Observation

Increasing the output load generally increases propagation delay.

Similarly, the input transition/slew affects the delay through the cell.

Therefore:

```text
Cell Delay = f(Input Slew, Output Load)
```

---

# 5. Clock Buffer Tree

A clock tree distributes the clock from a source to multiple sequential elements.

A simplified structure is:

```text
                    Clock Source
                         |
                       CBUF
                      /    \
                   CBUF    CBUF
                   /  \    /  \
                  FF  FF  FF  FF
```

For a balanced clock tree, branches should have reasonably similar electrical characteristics.

## Example

Consider:

```text
C1 = C2 = C3 = C4 = 25 fF
```

and:

```text
CBUF1 = CBUF2 = 30 fF
```

The capacitance seen at different nodes can be estimated as:

```text
Node A = 30 fF + 30 fF
       = 60 fF
```

and:

```text
Node B = 25 fF + 25 fF
       = 50 fF

Node C = 25 fF + 25 fF
       = 50 fF
```

Conceptually:

```text
                 A
                / \
            CBUF1 CBUF2
              |     |
              B     C
             / \   / \
           C1  C2 C3  C4
```

The example demonstrates the importance of understanding the load driven by each clock-tree node.

---

# 6. Timing Analysis

Static Timing Analysis (STA) determines whether signals propagate through the design within their required timing windows.

The two primary sequential timing checks are:

- Setup
- Hold

A typical data path is:

```text
Launch FF
    |
    | CLK-Q
    v
Combinational Logic
    |
    | Data Path Delay
    v
Capture FF
```

A more detailed path may contain multiple logic and interconnect stages:

```text
FF1
 |
 | CLK-Q
 v
Wire
 |
Logic / Buffer 1
 |
Wire
 |
Logic / Buffer 2
 |
Wire
 v
FF2
```

---
<img width="1028" height="458" alt="image" src="https://github.com/user-attachments/assets/55908a4a-d039-424e-a0e0-e563b77aa4a5" />

# 7. Setup Timing Analysis

## 7.1 Setup Requirement

Setup timing ensures that data arrives sufficiently early before the active edge of the capture clock.

For a simplified single-clock path:

```text
Data Arrival Time
<
Data Required Time
```

A common ideal-clock setup condition is:

```text
Data Arrival
<
T - Setup - Clock Uncertainty
```

where:

- `T` = clock period
- `Setup` = setup time of the capture flip-flop
- `Clock Uncertainty` = timing margin allocated for clock uncertainty

---

## 7.2 Example

Assume:

```text
T = 1 ns
Setup = 10 ps
Uncertainty = 90 ps
```

Convert to nanoseconds:

```text
Setup = 0.01 ns
Uncertainty = 0.09 ns
```

Maximum allowable data-path delay:

```text
Maximum Delay
= 1 - 0.01 - 0.09
= 0.90 ns
```

Therefore:

```text
Data Arrival < 0.90 ns
```

The corresponding simplified setup slack is:

```text
Setup Slack
= T - Setup - Uncertainty - Data Arrival
```

A positive setup slack indicates that the simplified setup requirement is satisfied.

---

## 7.3 Data Arrival Time

For a multi-stage path:

```text
Data Arrival
=
FF1 CLK-Q
+ Wire Delay 1
+ Cell Delay 1
+ Wire Delay 2
+ Cell Delay 2
+ Wire Delay 3
```

For example:

```text
θ =
FF1(CLK-Q)
+ Wire Delay Estimate 1
+ Delay of Stage 1
+ Wire Delay Estimate 2
+ Delay of Stage 2
+ Wire Delay Estimate 3
```

---

# 8. Hold Timing Analysis

Hold timing ensures that the data launched by the source flip-flop does not reach the capture flip-flop too early.

The simplified hold condition is:

```text
Data Arrival Time
>=
Hold Requirement
```

For a simplified single-clock path:

```text
CLK-Q(min)
+ Minimum Data Path Delay
>=
Hold Time
```

When real clocks are considered, clock skew and uncertainty also influence the hold requirement.

A representative real-clock relationship is:

```text
θ + Δ1 > H + Δ2 + HU
```

where:

- `θ` = launch/data-path timing contribution
- `Δ1` = launch-side delay contribution
- `Δ2` = capture-side clock-path delay contribution
- `H` = hold time
- `HU` = hold uncertainty

The exact equation depends on the timing-path and skew convention being used.

---

# 9. Clock Tree Synthesis

## 9.1 What is CTS?

**Clock Tree Synthesis (CTS)** is the process of creating a physical clock-distribution network between the clock source and sequential elements.

Before CTS:

```text
                 Clock
                   |
                   +------------------+
                   |                  |
                  FF                 FF
```

After CTS:

```text
                     Clock Source
                          |
                       Buffer
                      /      \
                  Buffer    Buffer
                  /   \      /   \
                 FF   FF    FF    FF
```

CTS inserts buffers and creates a physical network to control:

- Clock skew
- Insertion delay
- Clock slew
- Capacitance
- Fanout
- Power

---
<img width="1263" height="567" alt="Screenshot 2026-09-26 205406" src="https://github.com/user-attachments/assets/0485ae77-002b-4cfa-a879-adc579f9a451" />
## 9.2 Clock Skew

Clock skew is the difference between clock arrival times at two sequential elements.

If:

```text
Clock arrival at FF1 = L1
Clock arrival at FF2 = L2
```

then, depending on the chosen convention:

```text
Skew = L1 - L2
```

The important design objective is to control the magnitude and sign of skew so that setup and hold timing remain within their required limits.

---

## 9.3 Clock Insertion Delay

Clock insertion delay is the propagation delay from the clock source to a sequential element.

A clock path can be represented as:

```text
Clock Source
     |
     | Wire RC
     v
   Buffer
     |
     | Wire RC
     v
   Buffer
     |
     | Wire RC
     v
   Flip-Flop
```

Total clock insertion delay is approximately:

```text
Insertion Delay
=
Σ(Wire RC Delays)
+
Σ(Buffer Delays)
```

---

# 10. Crosstalk Delta Delay and Clock Skew

## 10.1 Crosstalk

Crosstalk occurs when neighboring interconnects electrically interact through parasitic coupling capacitance.

A simplified representation is:

```text
Aggressor
------------------------>

          ||
          || Cc
          ||

Victim
------------------------>
```

A transition on the aggressor can affect the voltage and delay characteristics of the victim net.

---

## 10.2 Crosstalk-Induced Delta Delay

Before crosstalk:

```text
Delay = D
```

After crosstalk:

```text
Delay = D + Δ
```

where:

```text
Δ = Crosstalk Delta Delay
```

Thus:

```text
Additional Delay = Δ
```

---

## 10.3 Effect on Clock Skew

Consider two clock paths.

Without the additional delay:

```text
L1 ≈ L2
```

If one path experiences a crosstalk-induced delay:

```text
L1 = L2 + Δ
```

Then the relative timing difference changes by:

```text
Skew Change = Δ
```

Therefore:

```text
Crosstalk
     |
     v
Delta Delay
     |
     v
Clock Arrival-Time Difference
     |
     v
Clock Skew
     |
     v
Setup / Hold Timing Impact
```

Crosstalk must therefore be considered during advanced timing analysis.

---

# 11. Timing Analysis with Real Clocks

## 11.1 Ideal Clock vs. Real Clock

### Ideal Clock

In an ideal-clock analysis:

```text
Clock source
     |
     +----------------------+
     |                      |
    FF1                    FF2
```

Clock propagation through physical clock-tree elements is not explicitly modeled.

### Real Clock

After CTS and physical implementation:

```text
                    +--- Buffer --- FF1
                    |
Clock Source --- Buffer
                    |
                    +--- Buffer --- FF2
```

The two clock paths can have different delays.

---

## 11.2 Real Clock Delay

A real clock path can contain:

```text
Wire RC Delay 1
      +
Buffer Delay 1
      +
Wire RC Delay 2
      +
Buffer Delay 2
      +
Wire RC Delay 3
      +
Buffer Delay 3
      +
...
```

Therefore:

```text
Clock Arrival Time
=
Clock Source Time
+
Total Clock Network Delay
```

---

## 11.3 Real Data-Path Delay

A real data path also contains physical interconnect delay:

```text
FF1
 |
 | CLK-Q
 v
Wire RC
 |
Buffer / Logic
 |
Wire RC
 |
Buffer / Logic
 |
Wire RC
 v
FF2
```

Therefore:

```text
Data Arrival
=
CLK-Q
+
Cell Delays
+
Wire RC Delays
```

Post-layout timing analysis uses extracted parasitic information to obtain more realistic timing values.

---

# 12. Timing Equations

## Clock Frequency

```text
F = 1 / T
```

For:

```text
F = 1 GHz
```

the clock period is:

```text
T = 1 ns
```

---

## Setup Slack

A simplified ideal-clock equation is:

```text
Setup Slack
=
T
- Setup Time
- Clock Uncertainty
- Data Arrival Time
```

For:

```text
T = 1 ns
Setup = 0.01 ns
Uncertainty = 0.09 ns
```

the available data-path time is:

```text
1 - 0.01 - 0.09
= 0.90 ns
```

---

## Hold Check

Simplified:

```text
CLK-Q(min)
+
Minimum Data Delay
>=
Hold Time
```

With clock-path effects:

```text
Launch/Data Arrival Contributions
>=
Capture/Hold Requirement
```

---

## Clock Skew

```text
Skew = Clock Arrival 1 - Clock Arrival 2
```

The sign convention should be defined according to the STA tool and timing analysis being performed.

---

## Crosstalk Delta Delay

```text
Delay_before = D

Delay_after = D + Δ
```

Therefore:

```text
Delta Delay = Δ
```

---

# 13. Timing Closure Flow

A simplified RTL-to-GDSII timing-closure flow is:

```text
                 RTL
                  |
                  v
              Synthesis
                  |
                  v
             Floorplanning
                  |
                  v
              Placement
                  |
                  v
                 CTS
                  |
                  v
               Routing
                  |
                  v
       Parasitic Extraction
                  |
                  v
          Static Timing Analysis
              /           \
             /             \
        Setup Check     Hold Check
             \             /
              \           /
               v         v
             Timing Closure
                  |
                  v
              Signoff
                  |
                  v
                GDSII
```

Timing closure may require iteration between physical implementation and timing analysis.

Typical optimization methods include:

- Cell resizing
- Buffer insertion
- Logic restructuring
- Placement optimization
- Clock-tree optimization
- Routing optimization
- Reducing excessive fanout
- Managing wire length and parasitic capacitance

---

# 14. Key Takeaways

1. **Timing analysis** verifies whether data reaches sequential elements within the required timing window.
2. **Setup analysis** checks whether data arrives early enough before the capture edge.
3. **Hold analysis** checks whether data remains stable for the required time after the capture edge.
4. **CTS** creates the physical clock-distribution network.
5. **Clock skew** is caused by differences in clock arrival time.
6. **Clock insertion delay** is the delay from the clock source to a sequential element.
7. Clock-buffer delay depends on **input slew and output load**.
8. Balanced clock-tree loads help control skew and timing variation.
9. **Power-aware CTS** considers clock power along with timing requirements.
10. **Crosstalk** can introduce additional delta delay into a signal path.
11. Crosstalk-induced delay can change **clock skew**.
12. Ideal-clock analysis is useful for early timing analysis, while real-clock analysis includes physical clock-network effects.
13. Post-layout timing analysis must account for **wire RC parasitics**.
14. Setup and hold violations can require physical-design optimization before signoff.
15. Timing closure is an iterative process connecting synthesis, placement, CTS, routing, extraction, and STA.

---

# 15. Important Keywords

```text
SKY130
PDK
Standard Cell
Liberty
LEF
Magic
OpenLane
RTL-to-GDSII
Synthesis
Floorplanning
Placement
Clock Tree Synthesis
CTS
Clock Buffer
Clock Gating
Power-Aware CTS
Clock Skew
Clock Insertion Delay
Clock Slew
Input Slew
Output Load
Fanout
Setup Time
Hold Time
Clock Uncertainty
Clock-to-Q
Data Arrival Time
Data Required Time
Timing Slack
Static Timing Analysis
STA
Wire RC
Parasitics
Crosstalk
Delta Delay
Timing Closure
Signoff
GDSII
```

---

## Conclusion

Timing is a critical part of ASIC physical design. As a design moves from RTL through synthesis, placement, CTS, and routing, physical effects such as wire resistance, capacitance, clock skew, and crosstalk increasingly influence circuit behavior.

Understanding these effects allows designers to analyze timing violations, optimize the clock network, and achieve reliable timing closure before final GDSII generation.
