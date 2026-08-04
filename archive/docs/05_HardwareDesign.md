# Hardware Design Document

| Field | Value |
|-------|-------|
| Document ID | HDD-001 |
| Project | TWIG – Test & Waveform Interface for GPIO |
| Version | 0.1.0 |
| Status | Draft |
| Author | Mede Gbaguidi |
| Language | English |
| Last Updated | 2026-07-31 |
| Related Documents | [02_SRS.md](02_SRS.md), [03_SystemArchitecture.md](03_SystemArchitecture.md), [04_ElectricalArchitecture.md](04_ElectricalArchitecture.md), [06_UserInterface.md](06_UserInterface.md) |

## Version History

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 0.1.0 | 2026-07-31 | Mede Gbaguidi | Initial hardware design framework for Phase 1 schematic/PCB work |

## Table of Contents

1. [Introduction](#1-introduction)
2. [Design Approach](#2-design-approach)
3. [Schematic Organization](#3-schematic-organization)
4. [Implementation Guidance by Family](#4-implementation-guidance-by-family)
5. [PCB and Mechanical Guidance](#5-pcb-and-mechanical-guidance)
6. [BOM and Component Strategy](#6-bom-and-component-strategy)
7. [Phase 2 Hardware Hooks](#7-phase-2-hardware-hooks)
8. [Design Checklist](#8-design-checklist)
9. [Open Hardware Items](#9-open-hardware-items)

---

## 1. Introduction

### 1.1 Purpose

This Hardware Design Document (HDD) defines **how to implement** TWIG Phase 1
in schematics, PCB, and mechanics.

It answers:

> How do we turn architecture into buildable hardware?

### 1.2 Scope

- schematic sheet plan;
- implementation patterns per family;
- PCB partitioning and mechanical envelope guidance;
- BOM philosophy;
- Phase 2 physical hooks.

It does **not** yet freeze a full BOM or completed KiCad project. Those are
project artifacts created under this framework.

### 1.3 Tools (recommended)

| Area | Recommended tool |
|------|------------------|
| Schematics / PCB | KiCad |
| Enclosure / panel | CAD of choice + front-panel drawing from UI doc |
| Documentation | Markdown in this repository |

---

## 2. Design Approach

### 2.1 Principles

1. **Hardware-first** — no MCU required for Phase 1 functions.  
2. **Repeatable channels** — design one channel, instantiate ×4.  
3. **Readable partitions** — schematic hierarchy mirrors SAD blocks.  
4. **Serviceability** — prefer replaceable connectors, fuses, relays.  
5. **Documented provisional values** — use EAD working assumptions until frozen.

### 2.2 Allowed Phase 1 Active Parts

Allowed without violating firmware independence:

- op-amps, comparators, references;
- regulators, isolated DC/DC modules;
- analog panel meters / LED voltmeter modules;
- discrete logic / LED drivers;
- relays, optocouplers, digital isolators;
- digipots **only if** manually controlled or hardwired (prefer pure analog for V1).

Not required for Phase 1:

- MCU/SoC firmware for basic operation;
- USB/Ethernet PHYs (may be unpopulated Phase 2 footprints).

---

## 3. Schematic Organization

### 3.1 Proposed KiCad Sheet Tree

```text
00_Cover_and_Notes
01_Power_Inlet_and_VBUS
02_System_Rails_VSYS_VAN_VREF
03_Aux_Outputs_5_12_24
04_AO_Bank (hierarchical channel ×4)
05_AI_Bank (hierarchical channel ×4)
06_RO_Bank (hierarchical channel ×4)
07_DI_Bank (hierarchical channel ×4)
08_DO_Bank (hierarchical channel ×4)
09_RLY_Bank (hierarchical channel ×4)
10_HMI_Indicators_and_Controls
11_Phase2_Hook_Header
99_Mechanical_Connectors_Map
```

### 3.2 Hierarchical Channel Rule

For each family bank:

- create one `CH_template` sheet;
- instantiate CH1..CH4 with identical nets aside from connector IDs;
- no hidden special behavior on CH1.

### 3.3 Net Naming Conventions

| Pattern | Example | Meaning |
|---------|---------|---------|
| `VBUS` | VBUS | Primary bulk rail |
| `VAN`, `VSYS`, `VREF` | VAN | Core rails |
| `AO1_OUT`, `AI2_IN` | AO1_OUT | Channel terminal nets |
| `DI3_VTH` | DI3_VTH | Selected threshold node |
| `DOM_DI_GND` | DOM_DI_GND | Domain return |
| `P2_*` | P2_UART_TX | Phase 2 hook nets |

---

## 4. Implementation Guidance by Family

### 4.1 Power Inlet & Rails

**Implement:**

- AC/DC capable inlet protection;
- bridge + bulk for VAC;
- reverse protection for VDC;
- VBUS distribution with local decoupling;
- aux regulators for 5/12/24;
- system regulators for VSYS/VAN/VREF.

**Place first in design reviews** because AO 25 V capability and aux loads size the tree.

### 4.2 AO

**Implement pattern:**

- range selector (rotary/slide switch) scaling a primary potentiometer/slider;
- V/I mode switch;
- output power stage capable of ~25 V full scale;
- local meter across output (or calibrated shunt path in current mode);
- short/current limiting.

Reuse one validated channel design four times.

### 4.3 AI

**Implement pattern:**

- input protection;
- V/I path selection;
- scaling to meter/conditioning range;
- local indication.

Keep AI return strategy consistent with CORE domain decisions in EAD.

### 4.4 RO

**Implement pattern:**

- range switching among candidate decades (100 Ω / 1 kΩ / 10 kΩ);
- coarse + fine controls;
- local resistance indication method (meter across calibrated sense strategy or precision display module);
- excitation protection.

Avoid temperature-curve ROMs/lookups in Phase 1.

### 4.5 DI

**Implement pattern:**

- selectable divider/reference network for 5/10/15/20 V thresholds;
- comparator + hysteresis;
- LED indicator;
- isolation barrier at bank or channel level (per EAD);
- clamps sized for abs-max > 20 V.

Mark panel: **DI: DC only** until an AC survivability claim is accepted.

### 4.6 DO

**Provisional V1:** isolated open-drain outputs with status LED and manual switch.

Leave footprints/options if selectable topology is desired later.

### 4.7 RLY

**Implement:**

- coil driver + flyback;
- SPST-NO (or SPDT if panel allows);
- contact terminals brought to front;
- state LED.

Keep coil drivers in CORE; contacts in DOM-RLY.

---

## 5. PCB and Mechanical Guidance

### 5.1 Board Concept

| Item | Guidance |
|------|----------|
| Board count | Prefer 1 main PCB; small meter/adapter PCBs allowed |
| Planning size | ~300 × 200 mm starting point |
| Layer count | 4 layers preferred for mixed analog/power (2-layer only if proven) |
| Partitioning | Match schematic banks geographically |

### 5.2 Suggested Board Zoning

```text
 +------------------------------------------------------+
 | POWER INLET / VBUS / AUX REGS                        |
 +------------------+------------------+----------------+
 | AO bank          | AI bank          | RO bank        |
 +------------------+------------------+----------------+
 | DI isolated      | DO isolated      | RLY            |
 +------------------+------------------+----------------+
 | HMI interconnect / indicators / Phase2 header        |
 +------------------------------------------------------+
```

### 5.3 Creepage / Clearance

Reserve isolation barrier keep-outs for DI/DO domains even while rating is provisional. Do not route unrelated CORE traces through isolator islands.

### 5.4 Mechanical

- bench enclosure with single primary working face;
- connectors and controls accessible without opening the box;
- service access for fuses/relays if practical;
- front-panel artwork controlled by UI document.

---

## 6. BOM and Component Strategy

### 6.1 Selection Priorities

1. Availability (multiple distributors).  
2. Repairability (THT connectors/relays acceptable on panel side).  
3. Repeatability (same op-amp / isolator family across channels).  
4. Cost secondary to clarity in V1 prototype.

### 6.2 Preferred Categories (not frozen PNs)

| Function | Preferred category |
|----------|--------------------|
| Op-amps | Dual/quad precision or industrial RRIO as needed |
| Comparators | Dedicated comparator ICs for DI |
| Isolators | Digital isolators or optocouplers for DI/DO |
| DC/DC | Encapsulated isolated modules for DI/DO banks |
| Relays | Sealed signal/power relays matching contact rating |
| Meters | Pre-made panel volt/amp modules for speed of bring-up |
| Terminals | One consistent family across signal ports |

### 6.3 Calibration / Trim

Phase 1 may use:

- potentiometer trims;
- selected resistors;
- meter zero/span pots.

No metrology certificate required for V1.

---

## 7. Phase 2 Hardware Hooks

Reserve on main PCB:

| Hook | Intent |
|------|--------|
| MCU daughter header | Power + GND + GPIO/ADC/SPI/UART candidates |
| Unpopulated USB/Eth area | Future comms |
| Sense tap testpoints | Setpoint/readback without rewires |
| Control FETs/analog mux footprints (optional) | Electronic override later |

Hooks must not break Phase 1 manual operation if unpopulated.

---

## 8. Design Checklist

Before first prototype fab:

- [ ] Inlet supports 24 VDC and 24 VAC paths  
- [ ] Aux 5/12/24 present and protected  
- [ ] AO ×4 with range + mode + meter  
- [ ] AI ×4 measure-only  
- [ ] RO ×4 ranges + coarse/fine + indication  
- [ ] DI ×4 thresholds 5/10/15/20 + LED  
- [ ] DO ×4 manual + LED  
- [ ] RLY ×4 dry contacts  
- [ ] Family labels unique on silkscreen  
- [ ] Isolation keep-outs drawn  
- [ ] Phase 2 header placed  
- [ ] Fuse accessible or documented  
- [ ] Absolute maximum ratings table drafted  

---

## 9. Open Hardware Items

| ID | Item |
|----|------|
| HDD-OPEN-001 | Final connector series (banana vs terminal block) |
| HDD-OPEN-002 | Exact enclosure model / panel thickness |
| HDD-OPEN-003 | Meter module selection |
| HDD-OPEN-004 | Relay PN and contact form freeze |
| HDD-OPEN-005 | 2-layer vs 4-layer final choice |
| HDD-OPEN-006 | Thermal / fan need after power budget |

---

## Document End

**Status:** Draft  
**Companion UI constraints:** [06_UserInterface.md](06_UserInterface.md)  
**Verification of builds:** [07_VerificationPlan.md](07_VerificationPlan.md)
