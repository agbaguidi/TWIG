# System Architecture Document

| Field | Value |
|-------|-------|
| Document ID | SAD-001 |
| Project | TWIG – Test & Waveform Interface for GPIO |
| Version | 0.1.0 |
| Status | Draft |
| Author | Mede Gbaguidi |
| Language | English |
| Last Updated | 2026-07-30 |
| Related Documents | [00_ProjectVision.md](00_ProjectVision.md), [01_PRD.md](01_PRD.md), [02_SRS.md](02_SRS.md), [04_ElectricalArchitecture.md](04_ElectricalArchitecture.md) |

## Version History

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 0.1.0 | 2026-07-30 | Mede Gbaguidi | Initial system architecture derived from Vision, PRD, and SRS |

## Table of Contents

1. [Introduction](#1-introduction)
2. [Architectural Goals and Constraints](#2-architectural-goals-and-constraints)
3. [System Context](#3-system-context)
4. [Logical Architecture](#4-logical-architecture)
5. [Subsystem Descriptions](#5-subsystem-descriptions)
6. [Power Architecture](#6-power-architecture)
7. [Isolation and Grounding Architecture](#7-isolation-and-grounding-architecture)
8. [Human Interface Architecture](#8-human-interface-architecture)
9. [Physical Architecture](#9-physical-architecture)
10. [Phase 2 Evolution Hooks](#10-phase-2-evolution-hooks)
11. [Architectural Decisions](#11-architectural-decisions)
12. [Open Architecture Items](#12-open-architecture-items)
13. [Traceability](#13-traceability)
14. [Glossary](#14-glossary)

---

## 1. Introduction

### 1.1 Purpose

This System Architecture Document (SAD) defines **how TWIG is organized** as a
system.

It answers:

> How is the system structured so that the SRS can be realized?

It sits between:

- the **SRS** (what the system shall do), and
- the **Electrical Architecture / Hardware Design** documents (how circuits
  and PCB implementation achieve it).

### 1.2 Scope

This document covers **Phase 1** architecture as the normative baseline, and
records **Phase 2 hooks** so later intelligence can be added without rewriting
I/O family roles.

Out of scope for this document:

- schematic-level topology and part numbers;
- PCB stack-up and routing rules;
- detailed comparator/amplifier calculations;
- enclosure CAD.

Those belong to later design documents.

### 1.3 Document Conventions

- Architecture elements are identified as `SAD-BLK-*` (blocks) or `SAD-DEC-*`
  (decisions).
- “Shall” statements here are architectural constraints that refine the SRS;
  they must not contradict `02_SRS.md`.
- Items still under discussion are marked **Provisional** or **Open**.

---

## 2. Architectural Goals and Constraints

### 2.1 Goals

| ID | Goal |
|----|------|
| SAD-GOAL-001 | Keep Phase 1 fully operable with manual controls and local indication (no firmware dependency). |
| SAD-GOAL-002 | Host all Phase 1 I/O families on one coherent mixed instrument. |
| SAD-GOAL-003 | Keep channels within a family identical and independently usable. |
| SAD-GOAL-004 | Separate I/O roles clearly (AI ≠ AO, DI ≠ DO, RO dedicated). |
| SAD-GOAL-005 | Apply hybrid isolation where industrial robustness justifies it. |
| SAD-GOAL-006 | Preserve a clean attachment point for a future Phase 2 supervisory MCU. |
| SAD-GOAL-007 | Prefer repairable, understandable hardware over early software complexity. |

### 2.2 Hard Constraints (from PRD / SRS)

| Constraint | Source |
|------------|--------|
| Single mixed-I/O instrument for V1 | SRS-REQ-GEN-003 |
| Four channels per AO/AI/RO/DI/DO/RLY | SRS-REQ-GEN-006 |
| No universal multi-role terminals | SRS-REQ-GEN-008 |
| External brick power: 24 VDC or 24 VAC; internals on DC | SRS-REQ-PWR-001..005 |
| AO voltage full-scale up to ≈ 0–25 VDC | SRS-REQ-AO-011 |
| DI selectable thresholds 5 / 10 / 15 / 20 V | SRS-REQ-DI-004 |
| RO = ohmic ranges, not temperature modes | SRS-REQ-RO-003 |
| Aux outputs 5 / 12 / 24 VDC to DUT | SRS-REQ-PSU-001 |

---

## 3. System Context

### 3.1 External Actors

```text
                         Operator
                    (knobs / switches / meters)
                             |
                             v
 +----------------+    +-----------+    +------------------+
 | External brick |--->|   TWIG    |<-->| DUT / field I/O  |
 | 24 VDC/24 VAC  |    | Phase 1   |    | under test       |
 +----------------+    +-----------+    +------------------+
                             ^
                             |
                    Optional reference meters
                    (calibration / debug only)
```

### 3.2 System Responsibility

TWIG Phase 1 is responsible for:

- converting brick power into internal DC rails and DUT aux rails;
- generating AO stimuli;
- measuring AI signals;
- presenting RO resistances;
- sensing DI states against selected thresholds;
- driving DO and RLY contacts;
- exposing all of the above through a front-panel HMI.

TWIG Phase 1 is **not** responsible for:

- interpreting temperature curves;
- host/network automation;
- correct DUT behavior.

---

## 4. Logical Architecture

### 4.1 Top-Level Block Diagram

```text
 +--------------------------------------------------------------------------+
 |                              TWIG Phase 1                                |
 |                                                                          |
 |  +--------------------- Front Panel HMI ---------------------+           |
 |  | selectors, setpoint controls, switches, local indicators  |           |
 |  +---------------------------+-------------------------------+           |
 |                              | control / indication                      |
 |                              v                                           |
 |  +-----------+  +-----------+  +-----------+  +-----------+              |
 |  |  AO x4    |  |  AI x4    |  |  RO x4    |  |  DI x4    |              |
 |  +-----------+  +-----------+  +-----------+  +-----------+              |
 |  +-----------+  +-----------+  +---------------------------+             |
 |  |  DO x4    |  |  RLY x4   |  | Aux PSU 5/12/24 VDC out  |             |
 |  +-----------+  +-----------+  +---------------------------+             |
 |                              ^                                           |
 |                              | rails / references                        |
 |  +---------------------------+-------------------------------+           |
 |  |              Power Input & Distribution                   |           |
 |  |  24 VDC/VAC inlet -> rectify/regulate -> internal DC     |           |
 |  +-----------------------------------------------------------+           |
 |                                                                          |
 |  [Optional Phase 2 Supervisory Hook -- not required for Phase 1]         |
 +--------------------------------------------------------------------------+
```

### 4.2 Logical Subsystems

| ID | Subsystem | Responsibility |
|----|-----------|----------------|
| SAD-BLK-PWR | Power Input & Distribution | Accept 24 VDC/VAC brick; create internal DC; feed aux regulators |
| SAD-BLK-AO | Analog Output Bank | 4 identical AO channels (V/I, range select, indication) |
| SAD-BLK-AI | Analog Input Bank | 4 identical AI channels (V/I measure, indication) |
| SAD-BLK-RO | Resistance Simulator Bank | 4 identical RO channels (ranges, coarse/fine, indication) |
| SAD-BLK-DI | Digital Input Bank | 4 identical DI channels (threshold select, state indication) |
| SAD-BLK-DO | Digital Output Bank | 4 identical DO channels (manual assert, indication) |
| SAD-BLK-RLY | Relay Bank | 4 identical dry-contact channels |
| SAD-BLK-AUX | Auxiliary Power Outputs | DUT-facing 5 / 12 / 24 VDC rails |
| SAD-BLK-HMI | Front-Panel HMI | Physical controls and local indicators |
| SAD-BLK-ISO | Isolation / Domain Fabric | Hybrid isolation boundaries and returns |
| SAD-BLK-P2 | Phase 2 Hook (reserved) | Future supervisory MCU attachment |

### 4.3 Control and Data Philosophy (Phase 1)

Phase 1 uses **direct hardware control paths**:

- operator actuator → channel circuit → DUT terminal;
- channel circuit → local indicator → operator.

There is **no required central digital bus** for Phase 1 operation.
Any future MCU shall observe/override through added interfaces, not replace
these paths.

---

## 5. Subsystem Descriptions

### 5.1 Analog Output Bank (SAD-BLK-AO)

**Role:** Generate DC voltage or current stimuli.

**Architectural shape (per channel):**

```text
 [Range selector] \
                   +--> [Setpoint scaling] --> [V/I output stage] --> AO terminal
 [Primary control]/
                                                              |
                                                      [Local meter/indicator]
```

**Key rules:**

- four identical independent channels;
- voltage and current modes;
- range/multiplier selector + continuous primary control;
- maximum voltage full-scale ≈ 0–25 VDC;
- local indication required;
- output-only terminals.

Detailed analog topology (DAC-less analog path, Howland pump, etc.) belongs
to Electrical Architecture / Hardware Design.

### 5.2 Analog Input Bank (SAD-BLK-AI)

**Role:** Measure DC voltage or current from the DUT.

**Architectural shape (per channel):**

```text
 AI terminal --> [Protection / scaling] --> [Measure path] --> [Local indicator]
                      |
                 mode: V or I
```

**Key rules:**

- four identical independent channels;
- voltage/current measurement only in Phase 1;
- no resistance-measure mode (RO owns resistance simulation);
- local indication required.

### 5.3 Resistance Simulator Bank (SAD-BLK-RO)

**Role:** Present a controllable resistance to an external sense circuit.

**Architectural shape (per channel):**

```text
 [Range select] + [Coarse] + [Fine] --> [Variable R network] <--> RO terminals
                                              |
                                      [Local R indication]
```

**Key rules:**

- four identical channels;
- ohmic ranges only (no PT100/NTC temperature modes);
- coarse + fine adjustment;
- local resistance indication;
- excitation withstand/protection defined later electrically.

### 5.4 Digital Input Bank (SAD-BLK-DI)

**Role:** Compare an incoming DC voltage against a selected threshold and
indicate active/inactive state.

**Architectural shape (per channel):**

```text
 DI terminal --> [Protection] --> [Comparator vs selected Vth] --> [LED/state]
                                      ^
                                      |
                         Threshold selector: 5 / 10 / 15 / 20 V
```

**Key rules:**

- four identical channels;
- selectable thresholds 5 V, 10 V, 15 V, 20 V;
- active when Vin ≥ selected threshold (hysteresis TBD);
- local indication required;
- **DC sensing is the intended use.** AC on DI is not a supported detection
  mode under current requirements; survivability/labeling policy remains an
  open SRS follow-up.

### 5.5 Digital Output Bank (SAD-BLK-DO)

**Role:** Manually assert a digital stimulus toward the DUT.

**Architectural shape (per channel):**

```text
 [Manual switch] --> [Output stage (topology TBD)] --> DO terminal
                           |
                     [Status LED]
```

**Key rules:**

- four identical channels;
- manual Phase 1 control;
- not a substitute for dry contacts (RLY owns that);
- output stage topology still open (push-pull / open-drain / selectable).

### 5.6 Relay Bank (SAD-BLK-RLY)

**Role:** Provide dry contacts for open/closed simulation.

**Architectural shape (per channel):**

```text
 [Manual switch] --> [Relay coil drive] --> [Contact set] --> RLY terminals
                           |
                     [State LED]
```

**Key rules:**

- four identical channels;
- galvanically separated dry contacts;
- contact form/rating still open (working assumption SPST-NO).

### 5.7 Auxiliary Power Outputs (SAD-BLK-AUX)

**Role:** Provide DUT utility rails.

**Key rules:**

- rails: 5 VDC, 12 VDC, 24 VDC;
- distinct from instrument brick inlet;
- no silent raw brick feed-through unless explicitly designed and labeled;
- current ratings still TBD.

---

## 6. Power Architecture

### 6.1 Power Concept

```text
 External brick
 24 VDC  or  24 VAC
        |
        v
 +---------------------------+
 | Inlet protection/labeling |
 +-------------+-------------+
               |
               v
 +---------------------------+
 | AC path: rectify + bulk   |
 | DC path: polarity protect |
 +-------------+-------------+
               |
               v
 +---------------------------+
 | Internal primary DC bus   |
 +------+-------------+------+
        |             |
        v             v
 Internal electronics   Aux regulators
 (I/O + HMI rails)      --> 5 / 12 / 24 VDC to DUT
```

### 6.2 Architectural Rules

| ID | Rule |
|----|------|
| SAD-PWR-001 | Instrument power source is an external brick only for Phase 1. |
| SAD-PWR-002 | Inlet supports both 24 VDC and 24 VAC bricks. |
| SAD-PWR-003 | All internal electronics operate from DC derived inside TWIG. |
| SAD-PWR-004 | One inlet concept shall serve both brick types (no operator hardware swap). |
| SAD-PWR-005 | DUT aux rails are regulated outputs, architecturally separate from the raw inlet. |
| SAD-PWR-006 | Power budget must cover simultaneous use of I/O banks + rated aux loads (exact W TBD). |

### 6.3 Implied Internal Rails (logical)

Exact voltages are design choices, but the architecture expects at least:

- a primary internal DC bus derived from the brick;
- local regulated rails for analog references / indicators / digital helpers as needed;
- dedicated aux output regulators for 5 / 12 / 24 VDC.

Electrical Architecture will freeze rail list, sequencing, and fusing.

---

## 7. Isolation and Grounding Architecture

### 7.1 Hybrid Isolation Strategy

TWIG uses **hybrid isolation**: isolate where industrial coupling and
ground-loop risk justify cost/complexity; do not isolate every block by
default.

### 7.2 Provisional Domain Model

> Provisional — to be finalized in Electrical Architecture.

| Domain (provisional) | Contents | Isolation intent |
|----------------------|----------|------------------|
| DOM-PWR | Brick inlet, primary converters | Protective isolation from operator-accessible DUT domains as required |
| DOM-CORE | Shared analog references, HMI low-level electronics | Common internal domain |
| DOM-AO | AO channels (or AO shared bank) | TBD — may share CORE or be bank-isolated |
| DOM-AI | AI channels | TBD |
| DOM-RO | RO channels | TBD — often DUT-referenced; careful excitation protection |
| DOM-DI | DI channels | Prefer isolation (industrial-facing) |
| DOM-DO | DO channels | Prefer isolation (industrial-facing) |
| DOM-RLY | Relay contacts | Naturally contact-isolated from coil side |
| DOM-AUX | 5/12/24 DUT supplies | TBD bonding to CORE vs floating |

### 7.3 Grounding Rules (architectural)

| ID | Rule |
|----|------|
| SAD-GND-001 | Each domain’s return strategy shall be explicit (common, star, or floating). |
| SAD-GND-002 | Front-panel labeling shall make domain/return relationships understandable enough to reduce miswires. |
| SAD-GND-003 | Isolation ratings and allowed bond options shall be published once domains are frozen. |

---

## 8. Human Interface Architecture

### 8.1 HMI as Primary Control Plane

In Phase 1, the front panel **is** the control plane.

```text
 Operator
    |
    +--> family-grouped controls
    |      AO: primary control + range selector + mode + meter
    |      AI: mode/range as needed + meter
    |      RO: range + coarse + fine + meter
    |      DI: threshold selector + state LED
    |      DO: assert control + state LED
    |      RLY: assert control + state LED
    |      AUX: labeled output terminals (+ protection indication if any)
    |
    +--> power status indication (present / fault TBD)
```

### 8.2 Uniformity Rules

| ID | Rule |
|----|------|
| SAD-HMI-001 | Channels of one family share the same control grammar. |
| SAD-HMI-002 | Families are visually grouped (AO block, AI block, …). |
| SAD-HMI-003 | Terminal roles are unmistakable by label and connector placement. |
| SAD-HMI-004 | No Phase 1 feature requires a host UI. |

Detailed panel layout belongs in the User Interface Design document.

---

## 9. Physical Architecture

### 9.1 Board Strategy

| Decision | Choice |
|----------|--------|
| V1 packaging | Single mixed I/O instrument |
| PCB approach | One main board (or main board + small helper boards if needed for meters/connectors), not one card per family |
| Form factor | Bench-top instrument |
| Planning envelope | ~300 × 200 mm main board discussed earlier (not frozen) |

### 9.2 Physical Partitioning Intent

Even on one board, schematic/layout partitions should remain recognizable:

1. Power inlet & conversion  
2. Aux regulators  
3. AO bank  
4. AI bank  
5. RO bank  
6. DI bank  
7. DO bank  
8. RLY bank  
9. HMI interconnect / indicators  
10. Reserved Phase 2 header / keep-out (recommended)

### 9.3 Connector Architecture (logical)

| Port class | Direction | Notes |
|------------|-----------|-------|
| Brick inlet | Power in | 24 VDC / 24 VAC capable |
| AO terminals | Out | Per channel |
| AI terminals | In | Per channel |
| RO terminals | Bidirectional resistance port | Per channel |
| DI terminals | In | Per channel |
| DO terminals | Out | Per channel |
| RLY terminals | Contact set | Per channel |
| AUX terminals | Out | 5 / 12 / 24 VDC |

Exact connector series (banana, terminal block, etc.) is still open.

---

## 10. Phase 2 Evolution Hooks

Phase 2 adds a supervisory intelligence layer. Architecture must not erase
Phase 1 manual paths.

### 10.1 Reserved Capabilities

- readbacks of setpoints / states;
- optional electronic control of channels;
- presets and calibration coefficients;
- USB/Ethernet host link;
- automated sequences.

### 10.2 Hook Rules

| ID | Rule |
|----|------|
| SAD-P2-001 | Manual front-panel operation of essential functions shall remain possible. |
| SAD-P2-002 | I/O family roles and terminal segregation shall remain stable. |
| SAD-P2-003 | A reserved interconnect (header/bus/keep-out) should be planned so an MCU board can attach later. |
| SAD-P2-004 | Phase 2 may automate the instrument; it shall not redefine AO/AI/RO/DI/DO/RLY as software-only abstractions. |

---

## 11. Architectural Decisions

| ID | Decision | Choice | Rationale |
|----|----------|--------|-----------|
| SAD-DEC-001 | Instrument topology | Single mixed I/O system | Matches personal-lab companion goal; simpler V1 |
| SAD-DEC-002 | Channel count | 4 per family | Usefulness vs panel/PCB congestion |
| SAD-DEC-003 | I/O universality | Rejected | Safety and clarity; specialized families |
| SAD-DEC-004 | Control plane Phase 1 | Pure HMI / hardware | Firmware independence |
| SAD-DEC-005 | Power entry | External 24 VDC or 24 VAC brick | Flexible lab power; internals stay DC |
| SAD-DEC-006 | Aux power | Dedicated 5/12/24 VDC outputs | DUT convenience without raw inlet feed-through |
| SAD-DEC-007 | AO voltage ceiling | ≈ 0–25 VDC | Industrial/embedded stimulus coverage |
| SAD-DEC-008 | DI threshold model | Selectable 5/10/15/20 V | Same range-selector philosophy as AO |
| SAD-DEC-009 | RO model | Ohmic ranges | Universal sensor-path simulation without curves |
| SAD-DEC-010 | Isolation | Hybrid domains | Cost/robustness balance |
| SAD-DEC-011 | Phase 2 | Hooked co-pilot, not replacement | Preserve instrument identity |

---

## 12. Open Architecture Items

These must be resolved in Electrical Architecture / Design (or by SRS
updates where product intent is still missing):

| ID | Topic | Notes |
|----|-------|-------|
| SAD-OPEN-001 | Final isolation domain map | Which banks share returns / which are isolated |
| SAD-OPEN-002 | AO discrete range set | Candidate: 0–5 / 0–10 / 0–15 / 0–25 V |
| SAD-OPEN-003 | RO decade set | Candidate: 0–100 Ω / 0–1 kΩ / 0–10 kΩ |
| SAD-OPEN-004 | DO output topology | Push-pull vs open-drain vs selectable |
| SAD-OPEN-005 | DI AC policy | DC-only labeling vs AC survivability requirement |
| SAD-OPEN-006 | Aux current ratings | e.g. 5 V@2 A, 12 V@1 A, 24 V@0.5 A |
| SAD-OPEN-007 | Brick inlet connector & tolerance | Pinout, polarity, ±% , minimum VA |
| SAD-OPEN-008 | Local meter technology | Per-channel panel meters vs shared display resources |
| SAD-OPEN-009 | Mechanical envelope / enclosure | Freeze after panel study |
| SAD-OPEN-010 | Phase 2 physical interconnect | Header/bus definition |

---

## 13. Traceability

| Architecture element | Primary SRS / PRD anchors |
|----------------------|---------------------------|
| SAD-BLK-PWR | SRS-REQ-PWR-* |
| SAD-BLK-AO | SRS-REQ-AO-*, PRD §9.1 |
| SAD-BLK-AI | SRS-REQ-AI-*, PRD §9.2 |
| SAD-BLK-RO | SRS-REQ-RO-*, PRD §9.3 |
| SAD-BLK-DI | SRS-REQ-DI-*, especially DI-004/008/009 |
| SAD-BLK-DO | SRS-REQ-DO-* |
| SAD-BLK-RLY | SRS-REQ-RLY-* |
| SAD-BLK-AUX | SRS-REQ-PSU-* |
| SAD-BLK-HMI | SRS-REQ-HMI-* |
| SAD-BLK-ISO | SRS-REQ-ISO-*, PROT-* |
| SAD-BLK-P2 | SRS-REQ-P2-* |
| Single mixed instrument | SRS-REQ-GEN-003 |
| Family segregation / ×4 channels | SRS-REQ-GEN-004..007 |

---

## 14. Glossary

| Term | Meaning |
|------|---------|
| SAD | System Architecture Document |
| Bank | Group of identical channels of one family |
| Domain | Isolation/ground region |
| HMI | Front-panel human interface |
| Hook | Reserved provision for future Phase 2 attachment |
| Mixed I/O instrument | One system hosting multiple I/O families together |
| Primary DC bus | Internal DC rail derived from the external brick |

---

## Document End

**Status:** Draft  
**Next expected document:** Electrical Architecture (`04_ElectricalArchitecture.md`) — written  
**Following documents:** Hardware Design, User Interface, Verification Plan
