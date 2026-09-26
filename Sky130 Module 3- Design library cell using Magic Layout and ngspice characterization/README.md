# SkyModule 3 – Design Library Cell Using Magic Layout and Ngspice Characterization

## Overview

This module covers the complete flow of designing and characterizing a CMOS inverter standard cell using:

- SPICE / Ngspice simulation
- CMOS inverter VTC analysis
- Static behavior evaluation
- Standard-cell design concepts
- Magic layout
- Sky130 technology
- SPICE netlist extraction
- Ngspice post-layout simulation
- CMOS fabrication process
- Magic DRC

---

# 1. CMOS Inverter Ngspice Simulation

## 1.1 CMOS Inverter

A CMOS inverter consists of:

- PMOS transistor
- NMOS transistor
- VDD supply
- VSS / GND
- Input: `Vin`
- Output: `Vout`
- Load capacitance

### CMOS Inverter Structure

```text
             VDD
              |
             PMOS
              |
Vin ----------|------ Vout
              |
             NMOS
              |
             VSS
```

The PMOS and NMOS gates are connected together to form the input.

The drains of PMOS and NMOS are connected together to form the output.

---

# 2. SPICE Deck

A SPICE deck describes the circuit using text-based netlist information.

It mainly contains:

1. Component connectivity
2. Component values
3. Model information
4. Node identification
5. Simulation commands

## 2.1 Model Description

Example CMOS inverter netlist:

```spice
M1 out in Vdd Vdd pmos W=0.375u L=0.25u
M2 out in 0   0   nmos W=0.375u L=0.25u

Cload out 0 10f

Vdd Vdd 0 2.5
Vin in 0 2.5
```

### Device Dimensions

```text
Channel Length   = 0.25 µm
Channel Width    = 0.375 µm
VDD              = 2.5 V
Load capacitance = 10 fF
```

The transistor sizing is selected such that:

```text
Wp/Lp = Wn/Ln

     = 0.375 / 0.25

     = 1.5
```

---

# 3. SPICE Simulation Commands

## 3.1 Operating Point

```spice
.op
```

The `.op` command is used to calculate the DC operating point of the circuit.

## 3.2 DC Sweep

```spice
.dc Vin 0 2.5 0.05
```

This sweeps the input voltage from:

```text
0 V → 2.5 V
```

with a step size of:

```text
0.05 V
```

---

# 4. CMOS Model Inclusion

The required CMOS model file can be included using:

```spice
.include tsmc_025um_model.mod
```

or the appropriate technology model file.

Example:

```spice
.LIB 'tsmc_025um_model.mod' CMOS_MODELS
```

The netlist should end with:

```spice
.end
```

---

# 5. CMOS Inverter VTC Simulation

## 5.1 Voltage Transfer Characteristic

The Voltage Transfer Characteristic (VTC) represents:

```text
Vout versus Vin
```

for a CMOS inverter.

For an ideal CMOS inverter:

| Vin | Vout |
|---|---|
| 0 V | ≈ VDD |
| VDD | ≈ 0 V |

The transition region occurs around the switching threshold.

---

# 6. VTC for Different PMOS/NMOS Sizing Ratios

The VTC can be studied by changing the transistor sizing ratio.

The important parameter is:

```text
(Wp/Lp) / (Wn/Ln)
```

Example values:

```text
(Wp/Lp) / (Wn/Ln) = 1
(Wp/Lp) / (Wn/Ln) = 2
(Wp/Lp) / (Wn/Ln) = 3
(Wp/Lp) / (Wn/Ln) = 4
(Wp/Lp) / (Wn/Ln) = 8
```

Changing the PMOS/NMOS sizing ratio changes the switching threshold and the shape of the VTC curve.

---

# 7. Static Behavior Evaluation of CMOS Inverter

The static behavior of a CMOS inverter can be evaluated using its VTC.

Important parameters include:

- Switching threshold
- Noise margins
- `VOH`
- `VOL`
- `VIH`
- `VIL`
- Static power
- VTC characteristics

---

# 8. Switching Threshold

The switching threshold is represented by:

```text
Vm
```

It is the input voltage at which:

```text
Vin = Vout
```

Therefore:

```text
Vm = Vin = Vout
```

at the switching point.

## 8.1 Approximate Switching Threshold

The switching threshold depends on the relative strength of the PMOS and NMOS devices.

A general form is:

```text
Vm = R × VDD / (1 + R)
```

where the strength ratio can be represented approximately as:

```text
R = √[(kp' / kn') × (Wp/Lp)/(Wn/Ln)]
```

The exact expression depends on the MOSFET model and assumptions being used.

---

# 9. MOSFET Operating Regions in CMOS Inverter

During the VTC transition, the PMOS and NMOS devices move through different operating regions.

### PMOS

- Cutoff
- Linear
- Saturation

### NMOS

- Cutoff
- Linear
- Saturation

Typical inverter operation:

### When `Vin = 0`

```text
PMOS → ON
NMOS → OFF
Vout ≈ VDD
```

### When `Vin = VDD`

```text
PMOS → OFF
NMOS → ON
Vout ≈ 0
```

During the transition region, both devices conduct.

---

# 10. CMOS Inverter Standard Cell Design

The next step is to design a standard-cell layout.

The target technology is:

```text
Sky130
```

The standard cell can be designed using:

```text
Magic
```

and characterized using:

```text
Ngspice
```

---

# 11. Standard Cell Design Flow

The general flow is:

```text
CMOS Circuit
     ↓
SPICE Simulation
     ↓
VTC Characterization
     ↓
Magic Layout
     ↓
DRC
     ↓
SPICE Extraction
     ↓
Ngspice Post-Layout Simulation
     ↓
Characterization
```

---
<img width="787" height="633" alt="Screenshot 2026-09-12 130248" src="https://github.com/user-attachments/assets/8fc8e9d4-6e71-4009-aea8-7a3335300983" />


# 12. Sky130 Standard Cell Repository

The notes use a Sky130 standard-cell design repository.

Example:

```bash
git clone https://github.com/nickson-jose/vsdstdcell
```

Then:

```bash
ls -ltr
```

Navigate into the standard-cell directory:

```bash
cd vsdstdcell
ls -ltr
```

Check the current directory:

```bash
pwd
```

---

# 13. Magic Layout Setup

The Magic layout tool can be started using:

```bash
magic -T sky130A.tech sky130_inv.mag &
```

The technology file should correspond to the Sky130A process.

Example:

```text
sky130A.tech
```

The inverter layout file can be:

```text
sky130_inv.mag
```

---
<img width="1287" height="772" alt="Screenshot 2026-09-12 125234" src="https://github.com/user-attachments/assets/5e3f319e-f396-49db-9257-ee45c9c210ac" />

# 14. CMOS Inverter Layout

The CMOS inverter layout contains:

- PMOS
- NMOS
- N-well
- P-substrate
- Poly
- Metal
- Contacts
- VDD
- VSS
- Input
- Output

A simplified structure is:

```text
VDD
              |
        ┌───────────┐
        │   PMOS    │
        └─────┬─────┘
              |
            VOUT
              |
        ┌─────┴─────┐
        │   NMOS    │
        └───────────┘
              |
             VSS
```

The PMOS is placed inside the N-well.

The NMOS is placed in the P-type substrate.

---

# 15. CMOS Fabrication Process

## 15.1 16-Mask CMOS Process

The CMOS fabrication process can be understood through a sequence of mask and process steps.

The major steps are:

1. Selecting a substrate
2. Creating active regions
3. N-well and P-well formation
4. Gate formation
5. Lightly doped drain formation
6. Source/drain formation
7. Contact formation
8. Higher-level metal formation

---

# 16. Selecting the Substrate

A P-type silicon substrate is initially selected.

The substrate provides the base material for CMOS fabrication.

---

# 17. Creating Active Regions

The active region is defined using photolithography.

A typical structure contains:

```text
Photoresist
----------------
Si3N4
----------------
SiO2
----------------
P-type Silicon Substrate
```

The wafer is exposed to UV light through a mask.

The exposed photoresist is developed.

The unwanted material is removed by etching.

Silicon nitride acts as a protective layer during oxidation.

---

# 18. LOCOS Field Oxidation

Field oxide is grown to electrically isolate active regions.

This process is called:

```text
LOCOS
```

which stands for:

```text
Local Oxidation of Silicon
```

The field oxide isolates different device regions.

After oxidation, the silicon nitride layer can be stripped using hot phosphoric acid.

---
<img width="1405" height="527" alt="Screenshot 2026-09-12 160302" src="https://github.com/user-attachments/assets/ace0302e-ccbb-4a03-b6d1-7b4c891ab0eb" />

# 19. N-Well and P-Well Formation

N-well and P-well formation is used to create the regions required for PMOS and NMOS devices.

## P-Well

Boron is used as the P-type dopant.

Example:

```text
Boron ion implantation
Energy ≈ 200 keV
```

## N-Well

Phosphorus is used as the N-type dopant.

Example:

```text
Phosphorus ion implantation
Energy ≈ 400 keV
```

Ion implantation introduces dopants into selected areas of the silicon substrate.

---

# 20. Gate Formation

The gate structure is formed above the channel region.

A simplified MOS structure is:

```text
Gate
       ─────────
          Poly
       ─────────
         SiO2
       ─────────
       Silicon
```

The gate controls the formation of the conducting channel between source and drain.

---

# 21. Threshold Voltage

Threshold voltage is an important MOSFET parameter.

The notes use the relationship:

```text
Vt = VFB + 2φF + VSB-related term
```

A general threshold-voltage equation is:

```text
VTH = VFB + 2φF
      + γ(√(2φF + VSB) - √(2φF))
```

where:

```text
VFB = Flat-band voltage
φF  = Fermi potential
VSB = Source-to-body voltage
γ   = Body-effect coefficient
```

---

# 22. Doping and Semiconductor Parameters

Important parameters include:

```text
Si   = Silicon
q    = Electronic charge
NA   = Acceptor concentration
ND   = Donor concentration
εox  = Oxide permittivity
εSi  = Silicon permittivity
```

The Fermi potential and doping concentration affect the threshold voltage.

---

# 23. CMOS Cross-Section

A CMOS structure contains:

```text
PMOS                         NMOS
       ┌────────┐                 ┌────────┐
       │ Source │                 │ Source │
       └────────┘                 └────────┘
          N+                         N+
       ┌────────────────────────────────────┐
       │               N-Well               │
       └────────────────────────────────────┘
                    P-Substrate
```

The actual process structure contains:

- N-well
- P-well / P-substrate
- Source
- Drain
- Gate
- Oxide
- Contacts

---

# 24. Short Channel Effects

As transistor dimensions decrease, short-channel effects become important.

One important effect is:

```text
Drain field penetration into the channel
```

This can influence the electrical behavior of the MOSFET.

---

# 25. Source/Drain Formation

Source and drain regions are formed using ion implantation.

For NMOS:

```text
N+ source
N+ drain
```

For PMOS:

```text
P+ source
P+ drain
```

---

# 26. LDD Formation

LDD stands for:

```text
Lightly Doped Drain
```

The LDD structure helps reduce the electric field near the drain.

A simplified structure is:

```text
             Gate
              │
         ─────┼─────
         LDD  │  LDD
          N-  │  N-
         ───────────
            Channel
```

The side-wall spacer helps define the heavily doped source/drain regions.

---

# 27. Source/Drain Implantation

For NMOS:

```text
Arsenic
```

can be used as an N-type dopant.

Example implantation energy:

```text
≈ 75 keV
```

For PMOS:

```text
Boron
```

is used as a P-type dopant.

Example implantation energy:

```text
≈ 50 keV
```

After implantation, high-temperature annealing is performed to activate the dopants and repair implantation damage.

---

# 28. Source/Drain Formation and Channel Protection

A screen oxide can be used during ion implantation.

The screen oxide helps reduce unwanted effects during implantation.

Ion implantation must be controlled carefully because excessive implantation can damage the silicon lattice.

---

# 29. Contact Formation

Contacts provide electrical connections between:

- Gate
- Source
- Drain
- Metal interconnects

The thin oxide over the contact areas is removed to create openings.

---

# 30. Titanium / TiN Contact Process

Titanium can be deposited to reduce contact resistance.

The notes mention:

```text
Titanium deposition
```

followed by heating in nitrogen ambient.

This can result in the formation of:

```text
TiSi2
```

which provides low-resistance electrical contact to silicon.

TiN can be used as a barrier/local interconnect material.

---

# 31. TiN Etching

TiN can be removed using RCA cleaning chemistry.

Example solution:

```text
De-ionized water (H2O) → 5 parts
Ammonium hydroxide     → 1 part
Hydrogen peroxide      → 1 part
```

This corresponds to an RCA-type cleaning solution.

---

# 32. Higher-Level Metal Formation

Higher-level interconnects are formed after contact formation.

A typical sequence includes:

1. Deposit dielectric
2. Pattern the required regions
3. Deposit metal
4. Pattern metal
5. Planarize the surface
6. Repeat for additional metal layers

---

# 33. Phosphosilicate / Borophosphosilicate Glass

A layer of SiO2 containing phosphorus or boron can be deposited.

Examples:

```text
PSG  → Phosphosilicate Glass
BPSG → Borophosphosilicate Glass
```

These layers can be used as dielectric/interlayer materials.

---

# 34. Chemical Mechanical Polishing

CMP stands for:

```text
Chemical Mechanical Polishing
```

CMP is used to planarize the wafer surface.

Planarization is important before forming additional interconnect layers.

---

# 35. Metal Patterning

Metal layers are patterned using photolithography and etching.

Typical steps include:

```text
Deposit metal
     ↓
Apply photoresist
     ↓
Expose using mask
     ↓
Develop
     ↓
Etch unwanted metal
     ↓
Remove photoresist
```

Aluminium may be used as a metal layer depending on the process.

---

# 36. Multiple Metal Layers

Modern CMOS processes can contain multiple metal layers.

Typical sequence:

```text
Contact
   ↓
Metal 1
   ↓
Via
   ↓
Metal 2
   ↓
Via
   ↓
Higher Metal Layers
```

The final metal layer is used for signal routing and external connections.

---
<img width="953" height="667" alt="Screenshot 2026-09-12 163537" src="https://github.com/user-attachments/assets/ff66238c-88bd-4e10-bf72-56cf0e796e3f" />

# 37. Magic Layout Exploration

Start Magic with:

```bash
magic -T sky130A.tech sky130_inv.mag &
```

Check the directory:

```bash
ls -ltr
```

---

# 38. SPICE Extraction from Magic

After creating the layout, extract the layout into a SPICE netlist.

Open the extracted file:

```bash
vim sky130_inv.spice
```

The extracted SPICE file contains the circuit connectivity and parasitic information obtained from the layout.

---

# 39. Ext2spice

Magic can convert the extracted circuit into a SPICE-compatible netlist using:

```text
ext2spice
```

Typical commands:

```text
:ext2spice cthresh 0 rthresh 0
:ext2spice
```

The extracted SPICE netlist can then be simulated using Ngspice.

---

# 40. Ngspice Post-Layout Simulation

After extraction:

```bash
ngspice sky130_inv.spice
```

The extracted netlist can be simulated to verify that the physical layout behaves correctly.

The post-layout simulation includes parasitic effects extracted from the layout.

---

# 41. Layout Verification

The layout must satisfy the design rules of the target technology.

The main verification step is:

```text
DRC
```

DRC stands for:

```text
Design Rule Check
```

---

# 42. Magic DRC

Magic provides built-in DRC capabilities.

A DRC test setup can be obtained from OpenCircuitDesign:

```text
http://opencircuitdesign.com/magic/
```

A Sky130 DRC test archive may be obtained from the OpenCircuitDesign repository/archive.

---

# 43. DRC Test Setup

Navigate to the DRC test directory:

```bash
cd drc-tests
```

Start Magic:

```bash
magic
```

The Magic layout window will open.

Then open the required layout file.

Example:

```text
File → Open → met3.mag
```

---

# 44. Magic DRC Commands

Inside the Magic console:

```text
tech
```

or technology-specific commands can be used to inspect the technology setup.

Typical DRC-related commands include:

```text
box
select area
goto m3,1
drc why
feed clear
```

Technology and layer information can also be checked using the appropriate Magic commands.

---

# 45. Example DRC Flow

```text
Start Magic
     ↓
Load Sky130 technology
     ↓
Open layout
     ↓
Select required area
     ↓
Run DRC
     ↓
Inspect DRC violations
     ↓
Correct layout
     ↓
Run DRC again
     ↓
DRC clean
```

---

# 46. Important Magic Commands

Some useful commands from the notes are:

```text
%box
%select area
%goto m3,1
%drc why
%feed clear
%ls
%load poly
%tech load sky130A.tech
%drc check
```

The exact command syntax can vary depending on the Magic version and technology setup.

---
<img width="1150" height="628" alt="Screenshot 2026-09-21 221132" src="https://github.com/user-attachments/assets/ae94f5c7-d3f5-4ea4-a2ca-d1445bcc3739" />

# 47. Standard Cell Characterization Flow

The complete characterization flow is:

```text
1. Design CMOS inverter
        ↓
2. Create SPICE netlist
        ↓
3. Run Ngspice simulation
        ↓
4. Generate VTC
        ↓
5. Evaluate switching threshold
        ↓
6. Study PMOS/NMOS sizing
        ↓
7. Create Magic layout
        ↓
8. Perform DRC
        ↓
9. Extract SPICE netlist
        ↓
10. Run post-layout simulation
        ↓
11. Compare pre-layout and post-layout behavior
        ↓
12. Characterize the standard cell
```

---

# 48. Important Parameters to Observe

During characterization, observe:

- Switching threshold (`Vm`)
- `VOH`
- `VOL`
- `VIH`
- `VIL`
- Noise margins
- Propagation delay
- Rise time
- Fall time
- Power consumption
- PMOS/NMOS sizing ratio
- Load capacitance
- Parasitic capacitance
- Post-layout behavior

---

# 49. Pre-Layout vs Post-Layout Simulation

## 49.1 Pre-Layout Simulation

The schematic/netlist is simulated without physical layout parasitics.

```text
Schematic
   ↓
SPICE Netlist
   ↓
Ngspice
   ↓
VTC / Waveform
```

## 49.2 Post-Layout Simulation

The layout is extracted and parasitic components are included.

```text
Layout
   ↓
Extraction
   ↓
SPICE Netlist + Parasitics
   ↓
Ngspice
   ↓
Post-layout waveform
```

Post-layout simulation gives a more realistic representation of the physical circuit.

---

# 50. Key Concepts Learned

This module covers the following concepts:

- CMOS inverter
- SPICE netlist
- Ngspice simulation
- DC sweep
- VTC
- Switching threshold
- PMOS/NMOS sizing
- MOSFET operating regions
- Static behavior
- Standard-cell design
- Sky130 technology
- Magic layout
- CMOS fabrication
- Photolithography
- Oxidation
- LOCOS
- N-well formation
- P-well formation
- Ion implantation
- Threshold voltage
- LDD formation
- Source/drain formation
- Silicide formation
- TiN
- Contact formation
- Metal interconnect
- CMP
- SPICE extraction
- Post-layout simulation
- DRC

---

# 51. Useful Tools

| Tool | Purpose |
|---|---|
| Ngspice | SPICE simulation |
| Magic | IC layout |
| Sky130 | CMOS process design rules |
| SPICE | Circuit characterization |
| Git | Repository management |
| Vim | Editing SPICE/netlist files |
| Linux Terminal | Running EDA tools |

---

# 52. Final Inference

The CMOS inverter standard-cell flow demonstrates the connection between circuit design and physical IC implementation.

The overall process starts with transistor-level circuit design and SPICE simulation, followed by VTC and static behavior analysis. The circuit is then implemented as a physical layout using Magic and the Sky130 technology.

The layout is verified using DRC and converted into a SPICE netlist through extraction. Finally, post-layout Ngspice simulation is performed to verify the electrical behavior while considering layout parasitics.

Thus, the complete flow is:

```text
Circuit Design
      ↓
SPICE Simulation
      ↓
VTC Characterization
      ↓
Standard Cell Layout
      ↓
Magic DRC
      ↓
SPICE Extraction
      ↓
Post-Layout Ngspice
      ↓
Cell Characterization
```

This provides practical exposure to the custom-cell / physical-design flow and helps understand how a CMOS circuit is transformed into a physical IC layout
