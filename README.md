# ELEC2133 Signal Generator

A two-layer through-hole PCB that generates a **square wave**, converts it to a **triangle wave**, and then to an approximately **sine wave**, using four LM741 op-amps. Designed in KiCad 10.

**Author:** Ahab Iqbal
**Date:** July 2026

---

## How it works

The circuit is a chain of four op-amp stages. Each stage feeds the next.

```
 Relaxation      Lossy            Lossy            Inverting
 oscillator  ->  integrator   ->  integrator   ->  amplifier
   (U1)            (U2)             (U3)             (U4)
  square         triangle          ~sine         output stage
```

| Stage | IC | Function | Key components |
|-------|----|----------|----------------|
| 1 | U1 | **Square wave** – astable (relaxation) oscillator with positive feedback | R1 = 100k, C1 = 10 nF (timing); R2 = 100k, R3 = 27k (threshold divider) |
| 2 | U2 | **Triangle wave** – integrates the square wave | R4 = 10k input; C2 = 100 nF with R5 = 100k in parallel as feedback; R6 = 10k bias resistor |
| 3 | U3 | **Sine-like wave** – integrates and smooths the triangle | R7 = 10k input; C3 = 10 nF with R9 = 100k in parallel as feedback; R8 = 10k bias resistor |
| 4 | U4 | **Output amplifier** – inverting gain stage | R10 = 10k input, R12 = 27k feedback (gain ≈ −2.7); R11 = 10k bias resistor |

Each op-amp has a trimmer (RV1–RV4) across its offset-null pins (1 and 5), with the wiper tied to −15 V. This is the standard LM741 offset adjustment.

### Expected frequency

From the component values, the oscillator in stage 1 should run at roughly **1.16 kHz**:

```
beta = R3 / (R2 + R3) = 27k / 127k ≈ 0.213
f    = 1 / (2 · R1 · C1 · ln((1 + beta) / (1 − beta))) ≈ 1.16 kHz
```

This is a theoretical estimate that assumes an ideal op-amp and symmetric output saturation. Measured values will differ because of component tolerances and the 741's slew rate.

---

## Power

The board needs a **dual supply of ±15 V**.

| Connector | Net | Notes |
|-----------|-----|-------|
| J1 | +15 V | 2-pin header (both pins tied together) |
| J2 | GND | 2-pin header (both pins tied together) |
| J3 | −15 V | 2-pin header (both pins tied together) |

C4 (100 nF, +15 V to GND) and C5 (100 nF, GND to −15 V) provide supply decoupling.

---

## Taking measurements

| Waveform | Probe point |
|----------|-------------|
| Square | U1 pin 6 |
| Triangle | U2 pin 6 |
| Sine-like | U3 pin 6 |
| Final output (inverted, amplified sine) | U4 pin 6 |

Use any GND pin on J2 as the reference.

---

## Bill of materials

| Ref | Value | Qty | Footprint |
|-----|-------|-----|-----------|
| U1–U4 | LM741 | 4 | DIP-8 (7.62 mm) |
| R1, R2, R5, R9 | 100k | 4 | Axial DIN0207, 10.16 mm pitch |
| R4, R6, R7, R8, R10, R11 | 10k | 6 | Axial DIN0207, 10.16 mm pitch |
| R3, R12 | 27k | 2 | Axial DIN0207, 10.16 mm pitch |
| C1, C3 | 10 nF (0.01 µF) | 2 | Disc, 5.0 mm dia, 2.5 mm pitch |
| C2 | 100 nF (0.1 µF) | 1 | Disc, 5.0 mm dia, 2.5 mm pitch |
| C4, C5 | 100 nF | 2 | Disc, 5.0 mm dia, 2.5 mm pitch |
| RV1–RV4 | Potentiometer (value not specified in schematic) | 4 | Bourns 3296W vertical |
| J1–J3 | 1×2 pin header | 3 | 2.54 mm, vertical SMD |

---

## Repository contents

```
elec2133_bonus/
├── elec2133_bonus.kicad_pro      KiCad project file
├── elec2133_bonus.kicad_sch      Schematic
├── elec2133_bonus.kicad_pcb      PCB layout
├── elec2133_bonus.kicad_prl      Local project settings
├── ERC.rpt                       Electrical rules check report (0 errors, 0 warnings)
├── elec2133_bonus-*.gbr          Gerber files (copper, mask, paste, silkscreen, edge cuts)
├── elec2133_bonus-PTH.drl        Plated drill file
├── elec2133_bonus-NPTH.drl       Non-plated drill file
├── elec2133_bonus-job.gbrjob     Gerber job file
└── .history/                     KiCad local history (automatic snapshots)
```

## Board specifications

| Property | Value |
|----------|-------|
| Layers | 2 (F.Cu / B.Cu) |
| Thickness | 1.6 mm |
| Size | ≈ 85.9 × 59.6 mm |
| Min track width / clearance | 0.2 mm / 0.2 mm |
