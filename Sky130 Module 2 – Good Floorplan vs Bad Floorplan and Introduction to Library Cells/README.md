# Sky130 Module 2 – Good Floorplan vs Bad Floorplan and Introduction to Library Cells

## Physical Dimension

### 1) Define Width & Height of Core / Die

A **core** is a section of the chip where the fundamental logic of the design is placed.

A **die** is a small semiconductor material specimen on which the fundamental circuit is fabricated.

### Utilization Factor

Utilization factor is the ratio of the area occupied by the netlist to the total area of the core.

**Formula:**

Utilization Factor = Area Occupied by Netlist / Total Area of Core

Example:

Area occupied by netlist = 4 × 1.5 sq. units

Total area of core = 2 × 3 sq. units

Utilization Factor = (4 × 1.5) / (2 × 3)

Utilization Factor = 6 / 6 = 1 = 100%

At 100% utilization, the logical cells occupy the complete core area.

### Aspect Ratio

Aspect Ratio = Height / Width

Example:

Height = 2 units  
Width = 2 units

Aspect Ratio = 2 / 2 = 1

---

## 2) Define Locations of Preplaced Cells

The locations of some cells or blocks are defined before automated placement and routing.

These are called **preplaced cells**.

### Combinational Logic

The combinational logic can be divided into different blocks or modules.

Example:

**Block 1 → Block 2**

The design can be separated into different blocks and treated as separate IPs or modules.

---

## Other IPs / Cells

Similarly, there are other IPs available:

- Memory
- Clock Gating Cell
- Comparator
- MUX

---

## Floorplanning

The arrangement of IPs in a chip is referred to as **Floorplanning**.

- These IPs/blocks have user-defined locations.
- Hence, they are placed in the chip before automated placement and routing.
- These are called **pre-placed cells**.
- Automated placement and routing tools place the remaining logical cells in the design onto the chip.

---

## Power Planning

Power planning is used to provide proper power and ground connections to the cells and blocks.

It helps in:

- Proper power distribution
- Reducing voltage drop
- Improving reliability
- Providing power to different blocks

---

## Complete Design

A typical design contains:

**Din → FF1 → Buffer/Inverter → Combinational Logic → FF2 → Dout**

The connectivity information between gates is coded using **VHDL/Verilog hardware description languages** and is called a **netlist**.

---

## Netlist

A netlist represents the connectivity between the logical cells in a design.

Example:

**FF1 → Buffer → AND Gate → FF2**

The netlist contains information about:

- Cells present in the design
- Connections between cells
- Inputs and outputs

---

## 1) Bind Netlist with Physical Cell

The logical cells in the netlist must be associated with their corresponding physical/library cells.

Examples:

- FF1 → D Flip-Flop cell
- AND → AND gate cell
- BUF → Buffer cell
- INV → Inverter cell

This process is called **binding the netlist with physical cells**.

---

## 2) Placement

Placement is the stage where we determine the physical locations of the cells.

At this stage:

- Wirelength is estimated.
- Capacitance is estimated.
- Timing is considered.
- Input repeaters/buffers are inserted when required.
- Cells are placed in suitable locations.

---
<img width="960" height="608" alt="image" src="https://github.com/user-attachments/assets/3758fc41-2b45-4017-a454-ec0473c9d850" />

# Library Characterization and Modeling

Library characterization provides information about the behavior of standard cells.

### Important Concepts

- NLDM
- CCS Timing
- Power Characterization
- Noise Characterization

### NLDM

**NLDM** stands for **Non-Linear Delay Model**.

It is used to model the delay and timing behavior of library cells.

### CCS

**CCS** stands for **Composite Current Source**.

It provides a more detailed model of cell timing and electrical behavior.

---

# Static Timing Analysis

**Static Timing Analysis (STA)** is used to check whether the design meets its timing requirements.

A typical timing path is:

**Launch Flip-Flop → Combinational Logic → Capture Flip-Flop**

### Launch Flip-Flop

The launch flip-flop launches the data into the combinational logic.

### Capture Flip-Flop

The capture flip-flop captures the data after it propagates through the combinational logic.

### Important Timing Parameters

- Data arrival time
- Data required time
- Clock period
- Clock latency
- Clock skew
- Cell delay
- Net delay
- Setup time
- Hold time

### Basic Timing Relationship

**Data Arrival Time ≤ Data Required Time**

For a setup check, the data must arrive at the capture flip-flop before the required time.

---

# Standard Library Cells

Common standard library cells include:

- AND Gate
- OR Gate
- Buffer
- Inverter
- D Flip-Flop (DFF)
- Latch
- ICG

**ICG** stands for **Integrated Clock Gating Cell**.

---

# Overall Physical Design Flow

RTL

↓

Logic Synthesis

↓

Netlist

↓

Floorplanning

↓

Power Planning

↓

Bind Netlist with Physical Cells

↓

Placement

↓

Clock Tree Synthesis

↓

Routing

↓

Static Timing Analysis

↓

Sign-off

---

# Key Terms

| Term | Meaning |
|---|---|
| Core | Section of the chip where the fundamental logic is placed |
| Die | Semiconductor material on which the fundamental circuit is fabricated |
| Utilization Factor | Area occupied by netlist divided by total core area |
| Aspect Ratio | Height divided by width |
| Floorplanning | Arrangement of IPs/blocks in a chip |
| Preplaced Cells | Cells/blocks with user-defined locations |
| Netlist | Connectivity information between logical cells |
| Placement | Determining physical locations of cells |
| Power Planning | Planning the power and ground distribution |
| DFF | D Flip-Flop |
| ICG | Integrated Clock Gating Cell |
| NLDM | Non-Linear Delay Model |
| CCS | Composite Current Source |
| STA | Static Timing Analysis |
<img width="960" height="607" alt="Screenshot 2026-09-06 171612" src="https://github.com/user-attachments/assets/8d932a59-1380-4bb0-84f2-e0c8ab9e6a08" />
# Standard Cell Design Flow

##  Cell Design Flow

The standard cell design flow consists of three major stages:

1. Inputs
2. Design Steps
3. Outputs

### Inputs

The important inputs required for standard cell design are:

- Process Design Kits (PDKs)
- DRC rules
- LVS rules
- SPICE models
- Libraries
- User-defined specifications

### Design Steps

The major design steps are:

1. Circuit Design
2. Layout Design
3. Characterization
<img width="918" height="630" alt="Screenshot 2026-09-06 203229" src="https://github.com/user-attachments/assets/bfab483c-424b-48b8-95df-523ba7c56cd0" />

### Outputs

The main outputs obtained from the standard cell design flow are:

- CDL (Circuit Description Language)
- GDSII layout
- Extracted SPICE netlist (`.cir`)
- Timing characterization
- Noise characterization
- Power characterization
- Library files
- Function characterization

---

#  CMOS Inverter Basics

A CMOS inverter consists of:

- PMOS transistor
- NMOS transistor
- VDD supply
- GND connection
- Input
- Output

The PMOS and NMOS transistors work together to produce the inverted output.

### Operation

When the input is LOW:

- PMOS is ON
- NMOS is OFF
- Output is HIGH

When the input is HIGH:

- PMOS is OFF
- NMOS is ON
- Output is LOW

Therefore,


Input = 0  →  Output = 1
Input = 1  →  Output = 0
MOSFET Threshold Voltage to Standard Cell Design Flow

 MOSFET Threshold Voltage

The threshold voltage including body effect can be expressed as:

VT = VT0 + γ(√(VT + 2φF + VSB) - √(2φF))

The threshold-voltage relationship helps understand how source-to-body voltage affects transistor threshold.

Where:

- "VT0" = Zero-bias threshold voltage
- "γ" = Body-effect coefficient
- "VSB" = Source-to-body voltage
- "φF" = Fermi potential

---

 MOSFET Linear Region

For a MOSFET operating in the linear region:

ID = Kn[(VGS - VT)VDS - VDS²/2]

Where:

- "ID" = Drain current
- "Kn" = Process transconductance parameter
- "VGS" = Gate-to-source voltage
- "VT" = Threshold voltage
- "VDS" = Drain-to-source voltage

The linear-region equation is useful when the MOSFET operates with a small drain-to-source voltage.

---

MOSFET Saturation Region

For a MOSFET operating in the saturation region:

ID = (Kn/2)(W/L)(VGS - VT)²(1 + λVDS)

Where:

- "ID" = Drain current
- "Kn" = Process transconductance parameter
- "W" = Channel width
- "L" = Channel length
- "VGS" = Gate-to-source voltage
- "VT" = Threshold voltage
- "VDS" = Drain-to-source voltage
- "λ" = Channel-length modulation parameter

The term:

(1 + λVDS)

represents the effect of channel-length modulation.

---

 Standard Cell Design Steps

The complete design process is:

Circuit Design
      ↓
Layout Design
      ↓
DRC / LVS Verification
      ↓
Parasitic Extraction
      ↓
SPICE Simulation
      ↓
Characterization

The characterization stage includes:

- Functional characterization
- Timing characterization
- Noise characterization
- Power characterization

---

 Layout Design

After circuit design, the transistor-level circuit is converted into a physical layout.

The layout uses physical layers such as:

- Diffusion
- Polysilicon
- Metal
- Contacts
- Well regions

The layout must follow the rules supplied by the PDK (Process Design Kit).

---

 Stick Diagram

A stick diagram is a simplified representation of the physical layout.

It shows:

- Transistor regions
- Polysilicon lines
- Metal connections
- Source and drain connections
- VDD and GND rails
- Input and output connections

Example Transistor Input Ordering

A - C - E - F - Q - B

The stick diagram is created before the detailed mask/layout representation.

---

 CMOS Network Graph

A CMOS network graph represents the pull-up and pull-down transistor networks.

It contains:

- PMOS network graph
- NMOS network graph

The network graph helps convert a Boolean expression into a transistor-level CMOS implementation.

---

 Boolean Expression Example

The Boolean expression used in the layout example is:

Fn = (B + D) · (A + C) + E · F

It contains:

- AND operation between "(B + D)" and "(A + C)"
- AND operation between "E" and "F"
- OR operation between the resulting terms

Where:

+ = OR
· = AND

---

 CMOS Implementation of the Boolean Function

For:

Fn = (B + D) · (A + C) + E · F

The CMOS design is obtained by constructing complementary:

- Pull-Up Network (PMOS)
- Pull-Down Network (NMOS)

The PMOS network implements the complementary condition, while the NMOS network implements the pull-down condition.

Transistor Ordering

Transistor ordering is selected to:

- Reduce layout complexity
- Allow diffusion sharing
- Reduce area
- Reduce parasitic effects

---

 Layout Representation
The layout example contains:

- VDD rail
- GND rail
- PMOS region
- NMOS region
- Polysilicon input lines
- Metal interconnections
- Contacts
- Output connection

Input Lines

A   C   E   F   D   B

The layout demonstrates how the Boolean function is physically implemented using CMOS transistors.

---

DRC

DRC = Design Rule Check

DRC checks whether the physical layout satisfies the manufacturing rules specified by the PDK.

Typical DRC Checks

- Minimum width
- Minimum spacing
- Minimum enclosure
- Minimum overlap
- Contact dimensions
- Metal spacing
- Poly spacing

The layout should be DRC-clean before proceeding to the next verification stages.

---

 LVS

LVS = Layout Versus Schematic

LVS compares the extracted layout netlist with the original circuit/schematic netlist.

LVS Checks

- Number of devices
- Device types
- Device connections
- Transistor dimensions
- Input connections
- Output connections
- Power connections
- Ground connections

A successful LVS indicates that the physical layout correctly represents the intended circuit.

---

 Parasitic Extraction

Parasitic extraction is performed after layout verification.

It obtains unwanted parasitic elements caused by physical interconnections.

Important Parasitic Elements

- Resistance
- Capacitance

The extracted circuit can be represented as an extracted SPICE netlist:

Extracted SPICE Netlist
          ↓
        .cir

The extracted netlist is used for post-layout SPICE simulation.

---

 SPICE Simulation

SPICE is used for transistor-level simulation and electrical characterization.

The simulation setup may include:

- DC power supply
- Pulse input source
- Cell under test
- Output load capacitor
- Ground
- Input measurement point
- Output measurement point

The purpose is to observe input/output waveforms and calculate timing parameters.

---

 Timing Characterization

Timing characterization determines how quickly a cell responds to an input transition.

Important Timing Parameters

- Input rise time
- Input fall time
- Output rise time
- Output fall time
- Rise delay
- Fall delay
- Propagation delay
- Slew

---

 Timing Characterization Testbench

The testbench contains:

- Pulse source at the input
- First cell/buffer stage
- Second cell/buffer stage
- DC supply
- Output load capacitor
- Ground

Simplified Representation

                    VDD
                     |
                     |
IN → Pulse → [Cell 1] → [Cell 2] → OUT
                                  |
                                  C1
                                  |
                                 GND

The input signal is applied through a pulse source.

The output is connected to a load capacitor representing the load seen by the cell.

The output waveform is observed using a plot or measurement point.

---

 Example SPICE Testbench

The circuit contains:

VDD = 5 V

Input

Pulse source → Cell 1 → Cell 2 → Output

Output

OUT → C1 → GND

The supply is connected between the cell supply and ground.

The input pulse creates rising and falling transitions.

The output capacitor represents the load capacitance.

---

 Timing Threshold Definitions

Timing measurements use predefined voltage thresholds.

Parameter| Threshold
"slew_low_rise_thr"| 20%
"slew_high_rise_thr"| 80%
"slew_low_fall_thr"| 20%
"slew_high_fall_thr"| 80%
"in_rise_thr"| 50%
"in_fall_thr"| 50%
"out_rise_thr"| 50%
"out_fall_thr"| 50%

For supply voltage "VDD":

V20 = 0.2 × VDD
V50 = 0.5 × VDD
V80 = 0.8 × VDD

For "VDD = 5 V":

20% = 1 V
50% = 2.5 V
80% = 4 V

---

 Rise Slew

Rise slew is the time required for a rising signal to move from 20% to 80%.

Slew_rise = Time(80%) - Time(20%)

Therefore:

Rise slew =
Time(slew_high_rise_thr)
-
Time(slew_low_rise_thr)

---

 Fall Slew

Fall slew is the time required for a falling signal to move from 80% to 20%.

Slew_fall = Time(20%) - Time(80%)

Therefore:

Fall slew =
Time(slew_low_fall_thr)
-
Time(slew_high_fall_thr)

---

 Propagation Delay

Propagation delay is the time difference between an input transition and the corresponding output transition.

General Expression

Propagation Delay =
Time(out_*_thr) - Time(in_*_thr)

Rising Transition

tpd,rise =
Time(out_rise_thr) - Time(in_rise_thr)

Falling Transition

tpd,fall =
Time(out_fall_thr) - Time(in_fall_thr)

The output transition normally occurs later than the input transition.

---

 Input and Output Thresholds

Input and output timing thresholds are defined at 50% of the supply voltage.

in_rise_thr  = 50%
in_fall_thr  = 50%

out_rise_thr = 50%
out_fall_thr = 50%

These values are used to calculate propagation delay.

---

 Timing Waveform

A timing waveform can be interpreted as:

Voltage
  |
VDD|              _________
   |             /
80%|------------/
   |           /
50%|----------/---------- Timing reference
   |         /
20%|--------/
   |       /
  0|_______
   +--------------------------> Time

For a rising waveform:

20% → 80%

is used for rise slew.

For a falling waveform:

80% → 20%

is used for fall slew.

The 50% point is used for input/output delay measurements.

---

Transition Time

Transition time describes how quickly a signal changes from one logic level to another.

Rising Transition

20% ───────────────→ 80%

Falling Transition

80% ───────────────→ 20%

A smaller transition time generally indicates a faster signal transition.

---

 Timing Characterization Flow

The timing characterization procedure is:

Create Testbench
       ↓
Apply Input Pulse
       ↓
Run SPICE Simulation
       ↓
Observe Input Waveform
       ↓
Observe Output Waveform
       ↓
Find 20%, 50%, 80% Crossing Times
       ↓
Calculate Slew
       ↓
Calculate Propagation Delay
       ↓
Store Characterization Data

---

 Power Characterization

Power characterization determines the power consumed by the cell.

Important Power Components

- Dynamic power
- Switching power
- Leakage power

Power characterization is performed for different input transitions and operating conditions.

---

 Noise Characterization

Noise characterization determines how the cell behaves in the presence of unwanted voltage/current disturbances.

It helps evaluate:

- Noise sensitivity
- Noise margin
- Output response to noise
- Signal integrity

---

 Functional Characterization

Functional characterization verifies the logic function of the cell.

Procedure

Apply Input Combinations
        ↓
Run SPICE Simulation
        ↓
Observe Output
        ↓
Compare With Expected Logic
        ↓
Verify Function

---

 Cell Characterization Outputs

The characterization process produces information for the standard-cell library.

Main Outputs

- Functional Data
- Timing Data
- Power Data
- Noise Data

This information is used to create the standard-cell Liberty file.

---

 Liberty File

The Liberty file has the extension:

.lib

It contains information required by digital design and synthesis tools.

Typical Information

- Cell name
- Pin information
- Logic function
- Timing arcs
- Delay tables
- Slew tables
- Power information
- Operating conditions

Representation

Standard Cell
    |
    +-- Function
    |
    +-- Timing
    |
    +-- Power
    |
    +-- Pin information
    |
    +-- Characterization tables

---
<img width="1122" height="626" alt="Screenshot 2026-09-06 204037" src="https://github.com/user-attachments/assets/31645eae-ae8e-48b1-8408-d383ba0bcb0f" />

CDL

CDL = Circuit Description Language

CDL represents the circuit/netlist information of the designed cell.

It contains:

- Transistors
- Connections
- Device terminals
- Device parameters
- Power and ground connections

---

 GDSII

GDSII is the physical layout database format used for integrated-circuit layouts.

It represents:

- Physical layers
- Shapes
- Transistors
- Contacts
- Metal interconnects
- Cell boundaries

The final physical design can be exported as a GDSII file.

---

 Complete Standard Cell Flow

                    PDK
                     |
       +-------------+-------------+
       |             |             |
      DRC           LVS       SPICE Models
       |             |             |
       +-------------+-------------+
                     |
                     ↓
             Circuit Design
                     |
                     ↓
              SPICE Simulation
                     |
                     ↓
               Layout Design
                     |
                     ↓
                    DRC
                     |
                     ↓
                    LVS
                     |
                     ↓
          Parasitic Extraction
                     |
                     ↓
          Extracted SPICE Netlist
                     |
                     ↓
          Post-layout Simulation
                     |
                     ↓
              Characterization
             /       |       \
            /        |        \
      Functional   Timing     Power
                       |
                     Noise
                       |
                       ↓
                 Liberty (.lib)
                       +
                  GDSII / CDL

---

 Final Outputs

The final outputs of the standard-cell design flow are:

Output| Description
CDL| Circuit description/netlist
GDSII| Physical layout database
Extracted SPICE Netlist (.cir)| Post-layout circuit representation
Liberty (.lib)| Standard-cell timing, power and functional information
Functional Characterization| Logic behavior of the cell
Timing Characterization| Delay and slew information
Power Characterization| Power consumption information
Noise Characterization| Noise behavior information

---

 Important Terms

Term| Meaning
PDK| Process Design Kit
SPICE| Simulation Program with Integrated Circuit Emphasis
DRC| Design Rule Check
LVS| Layout Versus Schematic
CDL| Circuit Description Language
GDSII| Graphic Design System II
PVT| Process, Voltage and Temperature
VDD| Positive supply voltage
GND| Ground
ID| Drain current
VGS| Gate-to-source voltage
VDS| Drain-to-source voltage
VT| Threshold voltage
W| MOSFET channel width
L| MOSFET channel length
λ| Channel-length modulation parameter

---

 Summary

The standard-cell design process begins with:

- PDK
- SPICE models
- DRC/LVS rules
- Design specifications

The circuit is first designed and simulated.

It is then converted into a physical layout and checked using:

- DRC
- LVS

After verification:

Parasitic Extraction
        ↓
Post-Layout SPICE Simulation
        ↓
Characterization

The cell is characterized for:

- Function
- Timing
- Power
- Noise

The final characterization information is stored in the Liberty (".lib") file.

The physical layout is delivered as GDSII, while circuit information can be delivered as CDL and extracted SPICE netlists.

---

Complete Standard Cell Flow

PDK
 ↓
Circuit Design
 ↓
SPICE Simulation
 ↓
Layout Design
 ↓
DRC
 ↓
LVS
 ↓
Parasitic Extraction
 ↓
Extracted SPICE Netlist
 ↓
Post-Layout Simulation
 ↓
Functional Characterization
 ↓
Timing Characterization
 ↓
Power Characterization
 ↓
Noise Characterization
 ↓
Liberty (.lib)
 ↓
GDSII / CDL

---

Key Takeaway

Design → Simulate → Layout → Verify → Extract → Re-Simulate → Characterize → Generate Library

A standard cell is therefore not only a transistor circuit. It requires circuit design, physical layout, verification, parasitic analysis, simulation, and characterization before it can be used reliably in a digital VLSI design flow.
