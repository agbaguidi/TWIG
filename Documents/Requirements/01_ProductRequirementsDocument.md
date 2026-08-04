# 01 - Product Requirements Document

**Project:** TWIG - Test & Waveform Interface for GPIO  
**Document:** Product Requirements Document  
**Version:** 0.1.1  
**Status:** Draft  
**Last Updated:** 2026-08-03  


## Table of Contents

1. [Introduction](#1-introduction)
2. [Product Overview](#2-product-overview)
3. [Goals](#3-goals)
4. [Non-Goals](#4-non-goals)
5. [Target Users](#5-target-users)
6. [Typical Applications](#6-typical-applications)
7. [Product Philosophy](#7-product-philosophy)
8. [High-Level Requirements](#8-high-level-requirements)
9. [Feature Descriptions](#9-feature-descriptions)
10. [User Interface Philosophy](#10-user-interface-philosophy)
11. [Hardware Philosophy](#11-hardware-philosophy)
12. [Product Constraints](#12-product-constraints)
13. [Success Metrics](#13-success-metrics)
14. [Product Roadmap](#14-product-roadmap)
15. [Open Questions](#15-open-questions)
16. [Glossary](#16-glossary)
17. [Annexes](#17-annexes)
18. [Version History](#version-history)

---


## 1. Introduction


### 1.1 Purpose

This Product Requirements Document (PRD) defines **what TWIG is**, **why it exists**, and **what capabilities it must provide**.

It answers the product question:

> What are we building, and why?

It does **not** define how the system shall implement each function in detail. Those topics belong to:

- the Software / System Requirements Specification (SRS) — *What shall the system do?*
- the System Design Document (SDD) and related hardware design docs — *How will the system do it?*


### 1.2 Scope of This Document

This PRD covers:

- product identity and positioning;
- Phase 1 and Phase 2 intent;
- feature families and channel counts;
- user-facing behavior expectations;
- hardware and documentation philosophy;
- constraints, success criteria, and open questions.


### 1.3 Document Conventions

- Requirements are identified as `PRD-REQ-XXX`.
- Decisions already agreed during product definition are stated as normative for Phase 1 unless marked *Open*.
- “Shall” indicates a binding product intent for the current roadmap.
- “Should” indicates a strong preference that may be refined in the SRS.
- “May” indicates an optional capability.

---


## 2. Product Overview


### 2.1 Product Name

**TWIG – Test & Waveform Interface for GPIO**

The short name **TWIG** is used in the repository, documentation, and product references.

Documentation is written in **English** so the project can be shared, collaborated on, or published openly later, even though design discussions may occur in French.

### 2.2 One-Sentence Definition

TWIG is a bench-top laboratory instrument that generates, measures, and simulates the analog, digital, and resistive signals commonly needed when developing and validating embedded and industrial electronic systems.

### 2.3 Product Positioning

TWIG is primarily a **personal laboratory companion**.

It is developed with professional engineering discipline so that:

- another engineer can understand the project from the documentation;
- the design can evolve into open-source publication or commercial use;
- Phase 2 intelligence can be added without redesigning the instrument
from scratch.

TWIG is **not** positioned as a replacement for a full metrology lab. It is a practical, coherent workbench that reduces the number of separate boxes needed for day-to-day embedded and industrial bring-up.

### 2.4 Core Capability Summary

Phase 1 TWIG provides, on a single mixed I/O instrument:


| Family             | Abbreviation | Channels | Role                                   |
| ------------------ | ------------ | -------- | -------------------------------------- |
| Analog Outputs     | AO           | 4        | Generate voltage or current setpoints  |
| Analog Inputs      | AI           | 4        | Measure voltage or current             |
| Resistance Outputs | RO           | 4        | Simulate selectable resistance ranges  |
| Digital Inputs     | DI           | 4        | Observe logic / contact states         |
| Digital Outputs    | DO           | 4        | Drive logic-level outputs / indicators |
| Relays             | RLY          | 4        | Provide dry contacts                   |
| Auxiliary Power    | PSU          | —        | Provide 5 V, 12 V, and 24 V rails      |


---


## 3. Goals


### 3.1 Primary Goals


| ID           | Goal                                                                                                                      |
| ------------ | ------------------------------------------------------------------------------------------------------------------------- |
| PRD-GOAL-001 | Provide a single instrument that replaces several common lab boxes for embedded/industrial bring-up.                      |
| PRD-GOAL-002 | Deliver a Phase 1 instrument that is fully usable with manual controls and local indication, without firmware dependency. |
| PRD-GOAL-003 | Keep all channels of a given family functionally identical.                                                               |
| PRD-GOAL-004 | Prefer hardware simplicity and repairability over early software complexity.                                              |
| PRD-GOAL-005 | Preserve a clean evolution path to Phase 2 automation without invalidating Phase 1 architecture.                          |
| PRD-GOAL-006 | Produce documentation sufficient for another engineer to understand the product and begin schematic work.                 |


### 3.2 Secondary Goals


| ID           | Goal                                                                                                   |
| ------------ | ------------------------------------------------------------------------------------------------------ |
| PRD-GOAL-007 | Support industrial signal conventions commonly used with PLC, BACnet, Modbus, and STM32-class targets. |
| PRD-GOAL-008 | Provide hybrid galvanic isolation where it meaningfully improves safety and robustness.                |
| PRD-GOAL-009 | Make front-panel interaction discoverable without a software manual.                                   |
| PRD-GOAL-010 | Keep mechanical and electrical design suitable for a bench instrument, not a rack-only product.        |


---


## 4. Non-Goals

The following are explicitly **out of scope for Phase 1** and must not drive architecture decisions unless they preserve a future hook for Phase 2.


| ID            | Non-Goal                                                             |
| ------------- | -------------------------------------------------------------------- |
| PRD-NGOAL-001 | Web user interface                                                   |
| PRD-NGOAL-002 | Mandatory software or MCU control for basic operation                |
| PRD-NGOAL-003 | Network communication (Ethernet, Wi-Fi, BACnet/IP client, etc.)      |
| PRD-NGOAL-004 | Built-in data logging or SCADA integration                           |
| PRD-NGOAL-005 | Fully universal multi-mode terminals that mix AI/AO/DI/DO on one pin |
| PRD-NGOAL-006 | Temperature-mode sensor simulation with built-in PT100/NTC curves    |
| PRD-NGOAL-007 | Metrology-grade calibration certificates as a Phase 1 deliverable    |
| PRD-NGOAL-008 | Modular one-card-per-I/O-family architecture in V1                   |
| PRD-NGOAL-009 | Agile-style user-story backlog as part of the core documentation set |


Phase 2 may revisit some of these non-goals, especially software control, communication, and calibration management.

---


## 5. Target Users


### 5.1 Primary User

An engineer working in a **personal or small lab**, validating embedded boards, industrial interfaces, or control electronics on a bench.

### 5.2 Secondary Users

- automation / PLC engineers validating I/O wiring and logic;
- electronics technicians troubleshooting field devices;
- students and researchers needing a compact multi-signal workbench.


### 5.3 User Assumptions

Users are expected to:

- understand basic electronics and signal polarity;
- know the difference between voltage, current, and dry-contact interfaces;
- use TWIG as a stimulus / measurement aid, not as an unsupervised production tester.

---


## 6. Typical Applications

TWIG should support scenarios such as:

1. **Embedded bring-up** — inject known analog setpoints into an ADC path, observe GPIO, and power a DUT from auxiliary rails.
2. **Industrial I/O validation** — simulate 0–10 V or 4–20 mA transmitters, dry contacts, and digital status lines for PLC or gateway boards.
3. **Sensor-path development** — present a known resistance to a measurement circuit that expects PT100, PT1000, NTC, or similar resistive sensors.
4. **Fault injection / troubleshooting** — force contacts open/closed, set boundary analog values, and verify DUT reaction.
5. **Bench consolidation** — reduce reliance on separate power supplies, current calibrators, relay boards, and LED/switch boxes.

---


## 7. Product Philosophy


### 7.1 Hardware First

Phase 1 shall be a **true laboratory instrument**.

Every essential function shall remain reachable through physical controls (knobs, switches, buttons, selectors) and local indication (meters, LEDs, numeric displays). A microcontroller may appear later as a co-pilot; it must not become a prerequisite for basic use.

### 7.2 Progressive Intelligence

Phase 2 may add:

- MCU supervision;
- saved presets;
- digital calibration;
- USB/Ethernet control;
- automated sequences.

Phase 2 intelligence shall **automate the instrument**, not replace its front-panel identity.

### 7.3 Homogeneous Channels

All channels of the same family shall be identical in function, controls, and protections. Channel number must never imply hidden special behavior.

### 7.4 Specialized Terminals, Not Fake Universality

Inputs and outputs are **separated by role**.

TWIG shall not implement one “do-everything” terminal that can be voltage source, current source, digital I/O, and resistance simulator at once. Instead, it provides clear families:

- AO vs AI;
- DO vs DI;
- RO as a dedicated resistance simulation family.

This improves safety, clarity, and electrical integrity.

### 7.5 Documentation as a Product Asset

Documentation quality is part of the product. Another engineer should be able to clone the repository, read documents in order, understand the intent, and begin KiCad schematic work with minimal verbal explanation.

---


## 8. High-Level Requirements


### 8.1 Product Identity Requirements


| ID          | Requirement                                                                                                    |
| ----------- | -------------------------------------------------------------------------------------------------------------- |
| PRD-REQ-001 | The product name shall be TWIG – Test & Waveform Interface for GPIO.                                           |
| PRD-REQ-002 | Official project documentation shall be maintained in English.                                                 |
| PRD-REQ-003 | The repository documentation shall present TWIG as a coherent product, not a collection of unrelated circuits. |


### 8.2 Architecture Requirements


| ID          | Requirement                                                                                                             |
| ----------- | ----------------------------------------------------------------------------------------------------------------------- |
| PRD-REQ-010 | Phase 1 shall operate as a standalone hardware instrument without mandatory firmware.                                   |
| PRD-REQ-011 | Phase 1 shall use a single mixed I/O board architecture (not one PCB per I/O family).                                   |
| PRD-REQ-012 | The design shall reserve a credible path for Phase 2 MCU integration without requiring a full redesign of I/O families. |
| PRD-REQ-013 | Analog inputs and analog outputs shall be separate families.                                                            |
| PRD-REQ-014 | Digital inputs and digital outputs shall be separate families.                                                          |
| PRD-REQ-015 | Each I/O family listed in Section 2.4 shall provide **four identical channels**.                                        |


### 8.3 Safety and Robustness Requirements


| ID          | Requirement                                                                                                                                                                                          |
| ----------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| PRD-REQ-020 | The product shall use a **hybrid galvanic isolation** strategy: isolate interfaces where industrial coupling risk is high; avoid unnecessary isolation cost where benefit is low.                    |
| PRD-REQ-021 | Channels shall include practical protections appropriate to their role (examples: reverse polarity, overcurrent limiting, overvoltage protection). Exact limits belong in the SRS/electrical design. |
| PRD-REQ-022 | Front-panel labeling and connector assignment shall minimize the chance of misusing AI/AO/DI/DO/RO terminals.                                                                                        |


### 8.4 Usability Requirements


| ID          | Requirement                                                                                |
| ----------- | ------------------------------------------------------------------------------------------ |
| PRD-REQ-030 | A user shall be able to set and observe AO values locally without a computer.              |
| PRD-REQ-031 | A user shall be able to select RO ranges and adjust resistance locally without a computer. |
| PRD-REQ-032 | Digital activity shall be locally visible (for example via LEDs).                          |
| PRD-REQ-033 | Controls for a given family shall follow a uniform interaction pattern across channels.    |


### 8.5 Documentation Requirements


| ID          | Requirement                                                                                                                                                                                                             |
| ----------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| PRD-REQ-040 | Core documents shall use a stable header (ID, version, status, related documents) and version history.                                                                                                                  |
| PRD-REQ-041 | Requirements introduced here shall be refinable into numbered system requirements in a future SRS.                                                                                                                      |
| PRD-REQ-042 | Design choices that are product-defining (channel count, separation of I/O roles, resistance-range approach, isolation strategy, Phase 1 MCU independence) shall remain visible and traceable in the documentation set. |


---


## 9. Feature Descriptions


### 9.1 Analog Outputs (AO)

**Intent:** Generate controllable DC voltage or current stimuli for DUTs.

#### 9.1.1 Channel Count

Four identical AO channels.

#### 9.1.2 Operating Modes

Each AO channel shall support voltage and current generation modes relevant to industrial and embedded testing.

Indicative Phase 1 ranges discussed during product definition:


| Mode    | Indicative Ranges                                             |
| ------- | ------------------------------------------------------------- |
| Voltage | 0–5 V, 0–10 V, up to approximately 0–25 V via range selection |
| Current | 0–20 mA and 4–20 mA class operation                           |


Exact electrical limits, accuracy, compliance voltage, and resolution are deferred to the SRS and electrical design documents.

#### 9.1.3 Control Concept

AO setpoints shall be adjustable using:

- a continuous primary control (slider / potentiometer-style control);
- a range / multiplier selector that scales the primary control.

Example interaction model:

- selector position 1 → 0–5 V;
- selector position 2 → 0–10 V;
- higher positions → higher full-scale voltage, up to the chosen maximum.

This avoids one dedicated pot per range and keeps the panel compact.

#### 9.1.4 Local Indication

Each AO channel should display the applied voltage or current locally so the user can confirm the stimulus without an external meter for routine work.

#### 9.1.5 Product Notes

- AO terminals are **outputs**, not bidirectional universal ports.
- The front-panel “potentiometer” is a **command control**, not a resistance-measurement input.


### 9.2 Analog Inputs (AI)

**Intent:** Measure incoming DC voltage or current from a DUT or sensor path.

#### 9.2.1 Channel Count

Four identical AI channels.

#### 9.2.2 Scope for Phase 1

Phase 1 AI scope is limited to:

- voltage measurement;
- current measurement.

Phase 1 AI does **not** include dedicated resistance-measurement modes.
Resistance simulation belongs to the RO family.

#### 9.2.3 Local Indication

AI channels should provide local readout of the measured quantity, or at minimum a clear path to observe measured values as part of the instrument UI concept defined in later design docs.

### 9.3 Resistance Outputs / Resistance Simulator (RO)

**Intent:** Present a controllable resistance to an external measurement circuit, enabling simulation of resistive sensors.

#### 9.3.1 Important Clarification

RO simulates **resistance**, not temperature.

TWIG shall not encode PT100 / PT1000 / NTC Type 2 / Type 3 curves as first-class Phase 1 modes. The user selects a resistance range and sets an ohmic value. Temperature conversion, if needed, remains the user’s responsibility (or a future software helper in Phase 2).

This keeps Phase 1:

- simpler;
- more universal;
- independent of sensor curve databases;
- easier to implement without an MCU.


#### 9.3.2 Channel Count

Four identical RO channels.

#### 9.3.3 Control Concept

Each RO channel shall provide:

- selectable **resistance ranges** (not sensor-type modes);
- coarse adjustment;
- fine adjustment;
- local indication of the simulated resistance.

The range selector follows the same philosophy as AO multipliers: choose an scale, then adjust within that scale.

#### 9.3.4 Use Cases

RO is intended to exercise circuits expecting:

- RTDs (for example PT100 / PT1000);
- NTCs;
- other resistive transducers;

by presenting the ohmic value those circuits actually measure.

### 9.4 Digital Inputs (DI)

**Intent:** Observe logic levels or contact states coming from a DUT.

#### 9.4.1 Channel Count

Four identical DI channels.

#### 9.4.2 Behavior Expectations

- Clear local indication of active / inactive state (for example LED).
- Electrical characteristics suitable for common industrial/embedded digital sensing conventions, to be detailed in the SRS.
- Isolation applied according to the hybrid isolation strategy.


### 9.5 Digital Outputs (DO)

**Intent:** Drive digital stimuli / indicators toward a DUT.

#### 9.5.1 Channel Count

Four identical DO channels.

#### 9.5.2 Behavior Expectations

- Manual activation from the front panel in Phase 1.
- Local status indication.
- Homogeneous electrical behavior across channels.
- Isolation applied according to the hybrid isolation strategy.


### 9.6 Relays (RLY)

**Intent:** Provide dry-contact open/closed simulation independent from
logic-level DO.

#### 9.6.1 Channel Count

Four identical relay channels.

#### 9.6.2 Behavior Expectations

- Manual control in Phase 1.
- Clear contact-state indication.
- Suitable for simulating open/closed contacts referenced as needed by the DUT wiring (including ground-referenced contact scenarios discussed in product definition).

Relays exist because dry contacts are not the same product function as logic outputs. Keeping both preserves clarity and industrial usefulness.

### 9.7 Auxiliary Power Supplies

**Intent:** Power DUTs or interface circuits directly from TWIG during bench work.

Phase 1 shall provide auxiliary DC rails commonly needed in embedded and industrial labs:

- 5 VDC;
- 12 VDC;
- 24 VDC.

Current capacity, protection, and connector strategy are deferred to the SRS / electrical architecture documents, but availability of these rails is a product requirement.

### 9.8 Feature Summary Matrix


| Feature            | Phase 1  | Notes                                                               |
| ------------------ | -------- | ------------------------------------------------------------------- |
| AO ×4              | Required | Voltage/current, range selector + primary control, local indication |
| AI ×4              | Required | Voltage/current measurement only                                    |
| RO ×4              | Required | Resistance ranges, coarse/fine, local indication                    |
| DI ×4              | Required | Local indication                                                    |
| DO ×4              | Required | Manual control + indication                                         |
| Relays ×4          | Required | Dry contacts                                                        |
| Aux 5/12/24 V      | Required | Bench power                                                         |
| MCU automation     | Excluded | Phase 2                                                             |
| Network / web UI   | Excluded | Phase 2+                                                            |
| Temperature curves | Excluded | Use raw resistance instead                                          |


---


## 10. User Interface Philosophy


### 10.1 Front Panel as Primary UX

In Phase 1, the front panel **is** the user interface.

Software may later mirror or extend it, but must not become the only way to operate core functions.

### 10.2 Uniform Family Layout

Channels should be grouped by family, with consistent ordering:

```text
AO1 AO2 AO3 AO4
AI1 AI2 AI3 AI4
RO1 RO2 RO3 RO4
DI1 DI2 DI3 DI4
DO1 DO2 DO3 DO4
R1  R2  R3  R4
```

A user who learns one AO channel shall immediately understand the others.

### 10.3 Control Patterns

Preferred Phase 1 patterns:


| Family | Primary Controls                             | Indication                         |
| ------ | -------------------------------------------- | ---------------------------------- |
| AO     | Range selector + continuous setpoint control | Numeric / local meter for setpoint |
| AI     | Range / mode as needed                       | Local measured value               |
| RO     | Range selector + coarse + fine               | Local resistance value             |
| DI     | None (observe only)                          | LED / status                       |
| DO     | Manual switch / button                       | LED / status                       |
| Relay  | Manual switch / button                       | LED / status                       |


### 10.4 Discoverability

Labels, connector legends, and range markings shall make the instrument usable without opening a software settings screen.

---


## 11. Hardware Philosophy


### 11.1 Single Mixed Card (V1)

V1 shall implement a **single board (or single main board assembly)** that hosts a mix of all planned I/O families.

Rationale:

- matches the original goal of a universal bench companion;
- reduces early mechanical/modular complexity;
- keeps the first usable product coherent.

A future modular card-per-family approach remains possible later, but is not the V1 architecture.

### 11.2 Why Four Channels

Four channels per family is the product baseline because it balances:

- usefulness for realistic multi-sensor / multi-I/O tests;
- front-panel congestion;
- cost and PCB area;
- common grouping patterns in embedded/industrial systems.

Two channels were judged too limited for the intended workbench role. Eight channels per family was judged unnecessarily large for V1.

### 11.3 Hybrid Isolation

TWIG shall adopt **hybrid galvanic isolation**:

- apply isolation where it protects users/equipment and reduces ground-loop risk on industrial-facing interfaces;
- avoid isolating every internal block if that adds cost/complexity without proportional benefit.

Detailed isolation domains belong in electrical architecture documents. The product rule is: isolation is strategic, not absolutist.

### 11.4 Mechanical Expectations

The instrument should remain a bench-friendly form factor.

An early working estimate discussed during definition was on the order of a large main PCB around **300 × 200 mm**, subject to front-panel layout and connector choices. This is a planning estimate, not a frozen mechanical requirement.

### 11.5 Repairability and Homogeneity

- Prefer repeatable channel circuitry over one-off special channels.
- Prefer maintainable connectors and accessible adjustments.
- Prefer clear separation of power, analog, and digital concerns in layout intent, even on a single board.

---


## 12. Product Constraints


### 12.1 Technical Constraints


| ID          | Constraint                                                                              |
| ----------- | --------------------------------------------------------------------------------------- |
| PRD-CON-001 | Phase 1 must not depend on firmware for core stimulus/measurement/simulation functions. |
| PRD-CON-002 | V1 architecture is a mixed I/O instrument, not a modular card cage.                     |
| PRD-CON-003 | I/O roles are segregated (AI ≠ AO, DI ≠ DO, RO is dedicated).                           |
| PRD-CON-004 | RO is range-based resistance simulation, not temperature-mode simulation.               |


### 12.2 Process Constraints


| ID          | Constraint                                                                                                 |
| ----------- | ---------------------------------------------------------------------------------------------------------- |
| PRD-CON-010 | Documentation remains English-first.                                                                       |
| PRD-CON-011 | Core docs are built in order: Vision → PRD → SRS → architecture/design.                                    |
| PRD-CON-012 | Decision Log / RFC documents are optional later; they are not required to unblock the core set.            |
| PRD-CON-013 | User-story catalogs are not part of the PRD. Use cases may appear later in a dedicated document if needed. |


### 12.3 Business / Positioning Constraints


| ID          | Constraint                                                                                                                |
| ----------- | ------------------------------------------------------------------------------------------------------------------------- |
| PRD-CON-020 | Primary success target is personal laboratory usefulness.                                                                 |
| PRD-CON-021 | Commercial or open-source publication is allowed later, but must not distort Phase 1 into an unfinished platform product. |


---


## 13. Success Metrics

TWIG Phase 1 is successful if the following are true:


| ID          | Metric / Criterion                                                                                                                                                           |
| ----------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| PRD-SUC-001 | A typical DUT bring-up can be performed with fewer external instruments than before TWIG existed.                                                                            |
| PRD-SUC-002 | All Phase 1 families (AO, AI, RO, DI, DO, relays, aux power) are manually operable from the instrument itself.                                                               |
| PRD-SUC-003 | A new engineer can explain the product purpose and Phase 1/Phase 2 split after reading Vision + PRD only.                                                                    |
| PRD-SUC-004 | A hardware engineer can start schematic partitioning from the documentation without unresolved product-intent questions on channel count, family separation, or RO behavior. |
| PRD-SUC-005 | Phase 2 concepts can be proposed as additions (MCU, presets, comms) rather than as a rewrite of I/O families.                                                                |
| PRD-SUC-006 | No Phase 1 feature requires a computer to demonstrate its basic value.                                                                                                       |


Quantitative accuracy, timing, and electrical budgets are intentionally deferred to the SRS and verification plan.

---


## 14. Product Roadmap


### 14.1 Phase 1 — Hardware Instrument

Deliver a usable bench instrument with:

- mixed I/O families listed in this PRD;
- manual controls and local indication;
- hybrid isolation strategy;
- professional documentation foundation;
- no firmware dependency.


### 14.2 Phase 2 — Intelligent Instrument

Add a supervisory intelligence layer that can:

- read/control channels;
- store presets;
- assist calibration;
- communicate over USB/Ethernet;
- run automated test sequences;

while preserving physical operability.

### 14.3 Documentation Roadmap

Suggested document sequence:

```text
00 Vision            → why the product exists
01 PRD               → what we are building and why
02 SRS               → what the system shall do (testable requirements)
03 System Architecture
04 Electrical Architecture
05 Hardware Design
06 User Interface Design
07 Verification Plan
```

Optional later:

- Decision records / ADRs;
- RFCs for major changes;
- dedicated use-case document.

---


## 15. Open Questions

These items are acknowledged but not fully frozen in this PRD revision.


| ID         | Topic                                                         | Status | Notes                                                                                                                      |
| ---------- | ------------------------------------------------------------- | ------ | -------------------------------------------------------------------------------------------------------------------------- |
| PRD-OQ-001 | Final AO voltage full-scale maximum                           | Closed | Frozen in SRS as approximately **0–25 VDC** (nominal 25 VDC full scale)                                                                                |
| PRD-OQ-002 | Exact AO/AI/RO range steps                                    | Open   | Multiplier/range philosophy agreed; discrete steps TBD in SRS                                                                                          |
| PRD-OQ-003 | RO resistance span and decade set                             | Open   | Range-based approach agreed; ohmic bounds TBD                                                                                                          |
| PRD-OQ-004 | Isolation domain diagram                                      | Open   | Hybrid strategy agreed; domain boundaries TBD                                                                                                          |
| PRD-OQ-005 | Auxiliary PSU current ratings                                 | Open   | Rails agreed (5/12/24 V); current TBD                                                                                                                  |
| PRD-OQ-006 | Display technology per family                                 | Open   | Local indication required; meter/LED/module choices TBD                                                                                                |
| PRD-OQ-007 | Enclosure / front-panel mechanical standard                   | Open   | Bench instrument intent agreed; dimensions TBD                                                                                                         |
| PRD-OQ-008 | Whether any tiny non-MCU logic helpers are allowed in Phase 1 | Open   | Phase 1 remains firmware-independent; discrete/logic helpers may be acceptable if they do not create a software dependency                             |
| PRD-OQ-009 | Instrument power inlet                                        | Closed | External brick; inlet supports **24 VDC** and **24 VAC**; internal electronics run on DC                                                               |


---


## 16. Glossary


| Term             | Definition                                                      |
| ---------------- | --------------------------------------------------------------- |
| AO               | Analog Output — generates voltage or current                    |
| AI               | Analog Input — measures voltage or current                      |
| RO               | Resistance Output / resistance simulator                        |
| DI               | Digital Input                                                   |
| DO               | Digital Output                                                  |
| RLY              | Relay / dry-contact channel                                     |
| DUT              | Device Under Test                                               |
| Phase 1          | Hardware-first instrument with manual operation                 |
| Phase 2          | Intelligent evolution with MCU-assisted features                |
| Hybrid isolation | Selective galvanic isolation applied where justified            |
| PRD              | Product Requirements Document — product intent and scope        |
| SRS              | System Requirements Specification — detailed shall-requirements |
| SDD              | System Design Document — design/how                             |


---


## 17. Annexes


### Annex A — Decision Summary Captured in This PRD


| Decision             | Choice                                        |
| -------------------- | --------------------------------------------- |
| Product name         | TWIG – Test & Waveform Interface for GPIO     |
| Doc language         | English                                       |
| Primary audience     | Personal lab tool with professional practices |
| Phase 1 MCU          | Not required for operation                    |
| Phase 2 role         | Optional intelligent co-pilot                 |
| I/O universality     | Rejected; segregated families instead         |
| AI/AO relationship   | Separated                                     |
| DI/DO relationship   | Separated                                     |
| Channel count        | 4 per family                                  |
| Board strategy (V1)  | Single mixed I/O instrument                   |
| Resistance feature   | Ohmic ranges, not temperature modes           |
| AO control           | Primary control + range multiplier            |
| Isolation            | Hybrid                                        |
| User stories in PRD  | Excluded                                      |
| Decision Log/RFC now | Deferred                                      |


### Annex B — Mapping to Future SRS

The following PRD themes are expected to expand into numbered system
requirements later:

- electrical ranges and tolerances for AO/AI/RO;
- digital thresholds and relay contact ratings;
- isolation withstand and domain definitions;
- HMI control/indication requirements per channel;
- EMC, safety, and connector standards;
- verification methods for each requirement.


### Annex C — Relationship to Project Vision

This PRD refines [00_ProjectVision.md](00_ProjectVision.md) by turning
vision-level intent into product scope:

- Vision states the ambition and principles;
- PRD freezes the Phase 1 feature set, non-goals, and philosophy needed to
start requirements and design work.

---

## Version History

| Version | Date | Author | Changes |
|-|-|-|-|
| 0.1.1 | 2026-08-03 | Arnaud Gbaguidi | Document format aligned with SRS |
| 0.1.0 | 2026-07-25 | Arnaud Gbaguidi | Initial PRD capturing decisions from product definition discussions |
