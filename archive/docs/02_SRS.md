# System Requirements Specification


| Field             | Value                                                              |
| ----------------- | ------------------------------------------------------------------ |
| Document ID       | SRS-001                                                            |
| Project           | TWIG – Test & Waveform Interface for GPIO                          |
| Version           | 0.1.2                                                              |
| Status            | Draft                                                              |
| Author            | Mede Gbaguidi                                                      |
| Language          | English                                                            |
| Last Updated      | 2026-07-29                                                         |
| Related Documents | [00_ProjectVision.md](00_ProjectVision.md), [01_PRD.md](01_PRD.md), [03_SystemArchitecture.md](03_SystemArchitecture.md) |




## Version History


| Version | Date       | Author          | Changes                                                                             |
| ------- | ---------- | --------------- | ----------------------------------------------------------------------------------- |
| 0.1.2   | 2026-07-29 | Mede Gbaguidi   | DI threshold selection: 5 / 10 / 15 / 20 V (AO-like range approach)                 |
| 0.1.1   | 2026-07-25 | Arnaud Gbaguidi | Freeze AO max voltage ≈0–25 VDC; require 24 VDC / 24 VAC external brick inlet       |
| 0.1.0   | 2026-07-25 | Arnaud Gbaguidi | Initial SRS derived from PRD-001; electrical values marked TBD where not yet frozen |




## Table of Contents

1. [Introduction](#1-introduction)
2. [System Context](#2-system-context)
3. [Requirement Conventions](#3-requirement-conventions)
4. [General System Requirements](#4-general-system-requirements)
5. [Analog Output Requirements (AO)](#5-analog-output-requirements-ao)
6. [Analog Input Requirements (AI)](#6-analog-input-requirements-ai)
7. [Resistance Simulator Requirements (RO)](#7-resistance-simulator-requirements-ro)
8. [Digital Input Requirements (DI)](#8-digital-input-requirements-di)
9. [Digital Output Requirements (DO)](#9-digital-output-requirements-do)
10. [Relay Requirements (RLY)](#10-relay-requirements-rly)
11. [Power Requirements (Instrument Inlet + Auxiliary Outputs)](#11-power-requirements-instrument-inlet--auxiliary-outputs)
12. [Human Interface Requirements](#12-human-interface-requirements)
13. [Isolation, Grounding, and Protection](#13-isolation-grounding-and-protection)
14. [Environmental and Mechanical Requirements](#14-environmental-and-mechanical-requirements)
15. [Non-Functional Requirements](#15-non-functional-requirements)
16. [Phase 2 Reserved Requirements](#16-phase-2-reserved-requirements)
17. [Requirements Explicitly Out of Scope (Phase 1)](#17-requirements-explicitly-out-of-scope-phase-1)
18. [Verification Overview](#18-verification-overview)
19. [Open Items and Working Assumptions](#19-open-items-and-working-assumptions)
20. [Traceability to PRD](#20-traceability-to-prd)
21. [Glossary](#21-glossary)

---



## 1. Introduction



### 1.1 Purpose

This System Requirements Specification (SRS) defines **what the TWIG
system shall do** in terms that can guide design and verification.

It answers:

> What shall the system do?

It does **not** define schematic topology, component selection, PCB
layout, or firmware architecture. Those belong to architecture and design
documents.

### 1.2 Scope

This SRS covers **Phase 1** of TWIG as the normative baseline.

Phase 2 capabilities are listed only as **reserved** requirements so the
Phase 1 design does not accidentally block them.

### 1.3 Relationship to Other Documents


| Document                | Role                                 |
| ----------------------- | ------------------------------------ |
| Vision                  | Why TWIG exists                      |
| PRD                     | What product we are building and why |
| **SRS (this document)** | **What the system shall do**         |
| Architecture / Design   | How the system will do it            |
| Verification Plan       | How compliance will be demonstrated  |




### 1.4 System Overview

TWIG Phase 1 is a bench-top, manually operated instrumentation system
providing:

- 4 Analog Outputs (AO);
- 4 Analog Inputs (AI);
- 4 Resistance Simulator channels (RO);
- 4 Digital Inputs (DI);
- 4 Digital Outputs (DO);
- 4 Relay / dry-contact channels (RLY);
- Auxiliary DC power rails at 5 V, 12 V, and 24 V.

---



## 2. System Context



### 2.1 Actors and External Entities


| Entity                     | Interaction with TWIG                                                           |
| -------------------------- | ------------------------------------------------------------------------------- |
| Operator                   | Sets controls, reads local indicators, connects cables                          |
| DUT                        | Receives stimuli and/or presents signals to TWIG                                |
| External power brick       | Powers TWIG via **24 VDC** or **24 VAC** brick (internal electronics run on DC) |
| External meters (optional) | May be used for calibration checks; not required for routine Phase 1 use        |




### 2.2 Context Diagram (logical)

```text
                  +---------------------------+
                  |         Operator          |
                  |  (manual controls / HMI)  |
                  +-------------+-------------+
                                |
                                v
 +---------------+   +----------+----------+   +-------------+
 | External brick|-->|         TWIG        |<->|     DUT     |
 | 24 VDC or     |   |  AO AI RO DI DO RLY |   | (under test)|
 | 24 VAC        |   |  Aux 5/12/24 VDC    |   +-------------+
 +---------------+   +---------------------+
```



### 2.3 System Boundary

**Inside TWIG Phase 1:**

- I/O families listed above;
- front-panel controls and local indication;
- instrument input stage accepting 24 VDC or 24 VAC from an external brick, with internal DC conversion/distribution;
- protective features defined in this SRS.

**Outside TWIG Phase 1:**

- DUT behavior;
- external calibration laboratory services;
- PC software, MCU supervision, network services;
- temperature-curve databases for RTD/NTC conversion.

---



## 3. Requirement Conventions



### 3.1 Identifiers

Requirements use the form:

```text
SRS-REQ-<DOMAIN>-<NNN>
```

Examples: `SRS-REQ-GEN-001`, `SRS-REQ-AO-010`.

### 3.2 Priority


| Priority | Meaning                                                       |
| -------- | ------------------------------------------------------------- |
| Must     | Binding for Phase 1 acceptance                                |
| Should   | Strong design target; deviation requires documented rationale |
| May      | Optional; not required for Phase 1 acceptance                 |




### 3.3 Status


| Status      | Meaning                             |
| ----------- | ----------------------------------- |
| Accepted    | Derived from frozen PRD decisions   |
| Provisional | Working assumption pending decision |
| TBD         | Value or behavior not yet defined   |




### 3.4 Language

- **Shall** = Must
- **Should** = Should
- **May** = May

Where a numeric value is unknown, the requirement remains and the value
is marked **TBD** or given as a **provisional working assumption**.

---



## 4. General System Requirements


| ID              | Priority | Status   | Requirement                                                                                                                                                                   | PRD Trace                     |
| --------------- | -------- | -------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------- |
| SRS-REQ-GEN-001 | Must     | Accepted | The system shall be identified as TWIG – Test & Waveform Interface for GPIO.                                                                                                  | PRD-REQ-001                   |
| SRS-REQ-GEN-002 | Must     | Accepted | Phase 1 shall provide all essential stimulus, measurement, simulation, and indication functions without requiring firmware, a host PC, or network connectivity.               | PRD-REQ-010, PRD-REQ-030..032 |
| SRS-REQ-GEN-003 | Must     | Accepted | Phase 1 shall implement a single mixed-I/O instrument architecture (not one board per I/O family).                                                                            | PRD-REQ-011                   |
| SRS-REQ-GEN-004 | Must     | Accepted | Analog Inputs and Analog Outputs shall be separate terminal families.                                                                                                         | PRD-REQ-013                   |
| SRS-REQ-GEN-005 | Must     | Accepted | Digital Inputs and Digital Outputs shall be separate terminal families.                                                                                                       | PRD-REQ-014                   |
| SRS-REQ-GEN-006 | Must     | Accepted | The system shall provide exactly four (4) identical channels for each of: AO, AI, RO, DI, DO, and RLY.                                                                        | PRD-REQ-015                   |
| SRS-REQ-GEN-007 | Must     | Accepted | All channels within a family shall be functionally identical in controls, electrical role, indication, and protection intent. Channel index shall not imply special behavior. | PRD-GOAL-003                  |
| SRS-REQ-GEN-008 | Must     | Accepted | The system shall not implement universal multi-mode terminals that combine AI/AO/DI/DO/RO roles on one connector pin.                                                         | PRD-NGOAL-005                 |
| SRS-REQ-GEN-009 | Must     | Accepted | Resistance simulation shall be provided only by the RO family, not by AI.                                                                                                     | PRD §9.2 / §9.3               |
| SRS-REQ-GEN-010 | Must     | Accepted | Phase 1 shall not require built-in temperature-mode sensor curves (PT100/PT1000/NTC modes).                                                                                   | PRD-NGOAL-006                 |
| SRS-REQ-GEN-011 | Should   | Accepted | The Phase 1 design shall preserve a credible integration path for a future Phase 2 supervisory MCU without redesigning I/O family roles.                                      | PRD-REQ-012                   |
| SRS-REQ-GEN-012 | Must     | Accepted | Front-panel labeling and connector assignment shall distinguish AI, AO, DI, DO, RO, RLY, and PSU terminals clearly enough to reduce misconnection risk.                       | PRD-REQ-022                   |


---



## 5. Analog Output Requirements (AO)



### 5.1 Function


| ID             | Priority | Status   | Requirement                                                                            | PRD Trace   |
| -------------- | -------- | -------- | -------------------------------------------------------------------------------------- | ----------- |
| SRS-REQ-AO-001 | Must     | Accepted | The system shall provide four identical Analog Output channels (AO1–AO4).              | PRD-REQ-015 |
| SRS-REQ-AO-002 | Must     | Accepted | Each AO channel shall generate a controllable DC stimulus intended for DUT excitation. | PRD §9.1    |
| SRS-REQ-AO-003 | Must     | Accepted | Each AO channel shall support a voltage-output mode and a current-output mode.         | PRD §9.1.2  |
| SRS-REQ-AO-004 | Must     | Accepted | AO terminals shall be output-only; they shall not be used as analog inputs.            | PRD §9.1.5  |




### 5.2 Ranges and Electrical Behavior


| ID             | Priority | Status      | Requirement                                                                                                                                              | Notes                          |
| -------------- | -------- | ----------- | -------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------ |
| SRS-REQ-AO-010 | Must     | Accepted    | In voltage mode, each AO channel shall provide selectable full-scale ranges including at least **0–5 V** and **0–10 V**.                                 | From PRD indicative ranges     |
| SRS-REQ-AO-011 | Must     | Accepted    | In voltage mode, the maximum selectable full-scale range shall be approximately **0–25 VDC** (nominal target **25 VDC** full scale).                     | PRD-OQ-001 (closed)            |
| SRS-REQ-AO-012 | Must     | Provisional | In current mode, each AO channel shall support **0–20 mA** class operation.                                                                              |                                |
| SRS-REQ-AO-013 | May      | Provisional | In current mode, each AO channel shall support **4–20 mA** class operation (as a usable operating region and/or dedicated mode — exact UI encoding TBD). |                                |
| SRS-REQ-AO-014 | Must     | TBD         | AO voltage accuracy shall be within **TBD %** of reading or **TBD mV**, whichever is greater, after warm-up.                                             | Lab-grade, not metrology-grade |
| SRS-REQ-AO-015 | Must     | TBD         | AO current accuracy shall be within **TBD %** of reading or **TBD µA**, whichever is greater, after warm-up.                                             |                                |
| SRS-REQ-AO-016 | Must     | TBD         | In current mode, minimum compliance voltage shall be **TBD V**.                                                                                          |                                |
| SRS-REQ-AO-017 | Should   | TBD         | AO noise / ripple under nominal load shall be ≤ **TBD mVpp** (voltage mode) or ≤ **TBD µApp** (current mode).                                            |                                |
| SRS-REQ-AO-018 | Must     | TBD         | Maximum AO source current in voltage mode shall be limited to **TBD mA** for protection.                                                                 |                                |
| SRS-REQ-AO-019 | Must     | Accepted    | Each AO channel shall remain independently adjustable; changing one channel shall not be required to alter another channel’s setpoint.                   | Homogeneity / independence     |




### 5.3 Control and Indication


| ID             | Priority | Status      | Requirement                                                                                                                                            | PRD Trace                          |
| -------------- | -------- | ----------- | ------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------------------------------- |
| SRS-REQ-AO-020 | Must     | Accepted    | Each AO channel shall provide a continuous primary setpoint control (slider or equivalent potentiometric control).                                     | PRD §9.1.3                         |
| SRS-REQ-AO-021 | Must     | Accepted    | Each AO channel shall provide a range/multiplier selector that scales the primary control full-scale value.                                            | PRD §9.1.3                         |
| SRS-REQ-AO-022 | Must     | Accepted    | The operator shall be able to set AO output without a computer.                                                                                        | PRD-REQ-030                        |
| SRS-REQ-AO-023 | Must     | Accepted    | Each AO channel shall provide local indication of the commanded/applied voltage or current sufficient for routine bench use without an external meter. | PRD-REQ-030, §9.1.4                |
| SRS-REQ-AO-024 | Should   | TBD         | Local AO indication resolution shall be at least **TBD** display counts / digits.                                                                      |                                    |
| SRS-REQ-AO-025 | Must     | Provisional | Mode selection between voltage and current shall be available per channel.                                                                             | Exact switch arrangement is design |


---



## 6. Analog Input Requirements (AI)



### 6.1 Function


| ID             | Priority | Status   | Requirement                                                                              | PRD Trace           |
| -------------- | -------- | -------- | ---------------------------------------------------------------------------------------- | ------------------- |
| SRS-REQ-AI-001 | Must     | Accepted | The system shall provide four identical Analog Input channels (AI1–AI4).                 | PRD-REQ-015         |
| SRS-REQ-AI-002 | Must     | Accepted | Each AI channel shall measure DC voltage or DC current presented by an external circuit. | PRD §9.2            |
| SRS-REQ-AI-003 | Must     | Accepted | AI channels shall not provide resistance-measurement modes in Phase 1.                   | PRD §9.2.2          |
| SRS-REQ-AI-004 | Must     | Accepted | AI terminals shall be input-only; they shall not source AO-class stimuli.                | Separation of roles |




### 6.2 Ranges and Electrical Behavior


| ID             | Priority | Status   | Requirement                                                                                                                                                                                      | Notes                         |
| -------------- | -------- | -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ----------------------------- |
| SRS-REQ-AI-010 | Must     | Accepted | Voltage measurement ranges shall include at least the spans needed to observe common embedded/industrial signals (working assumption: spans covering **0–5 V**, **0–10 V**, and up to 25 **V**). | Align with AO where practical |
| SRS-REQ-AI-011 | Must     | Accepted | Current measurement capability shall include **0–20 mA** class signal.                                                                                                                           |                               |
| SRS-REQ-AI-012 | Must     | TBD      | AI voltage accuracy shall be within **TBD %** / **TBD mV**.                                                                                                                                      |                               |
| SRS-REQ-AI-013 | Must     | TBD      | AI current accuracy shall be within **TBD %** / **TBD µA**.                                                                                                                                      |                               |
| SRS-REQ-AI-014 | Must     | TBD      | AI input impedance (voltage mode) shall be ≥ **TBD MΩ** or otherwise specified to avoid material loading of typical DUT outputs.                                                                 |                               |
| SRS-REQ-AI-015 | Must     | TBD      | AI burden voltage / shunt drop (current mode) shall be ≤ **TBD mV** at 20 mA.                                                                                                                    |                               |
| SRS-REQ-AI-016 | Must     | TBD      | Maximum safe input voltage/current without damage shall be defined as **TBD** and labeled.                                                                                                       | Protection                    |




### 6.3 Indication


| ID             | Priority | Status   | Requirement                                                                          | PRD Trace  |
| -------------- | -------- | -------- | ------------------------------------------------------------------------------------ | ---------- |
| SRS-REQ-AI-020 | Must     | Accepted | The operator shall be able to observe AI measured values locally without a computer. | PRD §9.2.3 |
| SRS-REQ-AI-021 | Should   | TBD      | Local AI indication resolution shall be at least **TBD** display counts / digits.    |            |


---



## 7. Resistance Simulator Requirements (RO)



### 7.1 Function


| ID             | Priority | Status   | Requirement                                                                                                | PRD Trace           |
| -------------- | -------- | -------- | ---------------------------------------------------------------------------------------------------------- | ------------------- |
| SRS-REQ-RO-001 | Must     | Accepted | The system shall provide four identical Resistance Simulator channels (RO1–RO4).                           | PRD-REQ-015         |
| SRS-REQ-RO-002 | Must     | Accepted | Each RO channel shall present a controllable resistance to an external measurement circuit.                | PRD §9.3            |
| SRS-REQ-RO-003 | Must     | Accepted | RO channels shall be controlled by selectable **resistance ranges**, not by sensor-type/temperature modes. | PRD §9.3.1 / §9.3.3 |
| SRS-REQ-RO-004 | Must     | Accepted | Phase 1 shall not require built-in conversion from temperature to resistance for PT100/PT1000/NTC curves.  | PRD-NGOAL-006       |




### 7.2 Ranges and Electrical Behavior


| ID             | Priority | Status   | Requirement                                                                                                                                                                                           | Notes                |
| -------------- | -------- | -------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------- |
| SRS-REQ-RO-010 | Must     | TBD      | Each RO channel shall provide multiple selectable ohmic ranges covering at least the practical span needed for common RTD/NTC simulation. Working assumption: from approximately **0 Ω** to 1 **MΩ**. | PRD-OQ-003           |
| SRS-REQ-RO-011 | Must     | TBD      | Within a selected range, the operator shall be able to set resistance continuously (or in fine steps no coarser than **TBD**).                                                                        |                      |
| SRS-REQ-RO-012 | Must     | Accepted | Each RO channel shall provide coarse and fine adjustment controls.                                                                                                                                    | PRD §9.3.3           |
| SRS-REQ-RO-013 | Must     | TBD      | RO absolute accuracy shall be within **TBD %** or **TBD Ω**, whichever is greater.                                                                                                                    | Lab-use target       |
| SRS-REQ-RO-014 | Must     | TBD      | Maximum excitation current and/or power that an RO channel can safely accept from a DUT measurement circuit shall be **TBD** and protected/limited.                                                   | Critical safety item |
| SRS-REQ-RO-015 | Should   | TBD      | RO residual series/parasitic resistance and contact effects shall be characterized and documented as **TBD**.                                                                                         |                      |




### 7.3 Indication


| ID             | Priority | Status   | Requirement                                                                                                                          | PRD Trace   |
| -------------- | -------- | -------- | ------------------------------------------------------------------------------------------------------------------------------------ | ----------- |
| SRS-REQ-RO-020 | Must     | Accepted | The operator shall be able to select RO range and adjust resistance without a computer.                                              | PRD-REQ-031 |
| SRS-REQ-RO-021 | Must     | Accepted | Each RO channel shall provide local indication of the simulated resistance value.                                                    | PRD §9.3.3  |
| SRS-REQ-RO-022 | Should   | TBD      | Local RO indication resolution shall be sufficient to distinguish fine-adjustment changes of at least **TBD Ω** on the lowest range. |             |


---



## 8. Digital Input Requirements (DI)


| ID             | Priority | Status      | Requirement                                                                                                                                                                                                       | Notes / Trace    |
| -------------- | -------- | ----------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------- |
| SRS-REQ-DI-001 | Must     | Accepted    | The system shall provide four identical Digital Input channels (DI1–DI4).                                                                                                                                         | PRD-REQ-015      |
| SRS-REQ-DI-002 | Must     | Accepted    | Each DI channel shall indicate the logical/active state of an external digital or contact-like signal.                                                                                                            | PRD §9.4         |
| SRS-REQ-DI-003 | Must     | Accepted    | Each DI channel shall provide local visual indication of active/inactive state (e.g., LED).                                                                                                                       | PRD-REQ-032      |
| SRS-REQ-DI-004 | Must     | Accepted    | Each DI channel shall provide a selectable detection threshold with the following choices: **5 V**, **10 V**, **15 V**, and **20 V** (same range-selector philosophy as AO).                                       | Closed OPEN-005  |
| SRS-REQ-DI-005 | Must     | TBD         | Maximum DI input voltage without damage shall be **TBD V** (shall be strictly greater than the highest selectable threshold of 20 V; working assumption ≥ **24 V** or **30 V**).                                    |                  |
| SRS-REQ-DI-006 | Should   | Provisional | DI channels should support sensing of dry-contact or open-collector style signals where practical, subject to the selected input topology.                                                                        | Detail in design |
| SRS-REQ-DI-007 | Must     | Accepted    | DI electrical interface design shall follow the hybrid isolation strategy in Section 13.                                                                                                                          | PRD-REQ-020      |
| SRS-REQ-DI-008 | Must     | Accepted    | Each DI channel shall provide a local threshold/range selector operable without a computer.                                                                                                                       | AO-like HMI      |
| SRS-REQ-DI-009 | Must     | Accepted    | A DI channel shall be indicated as active when the input voltage meets or exceeds the selected threshold, and inactive when it is below that threshold (exact comparator hysteresis **TBD**).                      |                  |
| SRS-REQ-DI-010 | Should   | TBD         | Threshold selection accuracy / tolerance around each nominal step (5/10/15/20 V) shall be within **TBD %** or **TBD V**.                                                                                           |                  |


---



## 9. Digital Output Requirements (DO)


| ID             | Priority | Status   | Requirement                                                                                                                              | Notes / Trace        |
| -------------- | -------- | -------- | ---------------------------------------------------------------------------------------------------------------------------------------- | -------------------- |
| SRS-REQ-DO-001 | Must     | Accepted | The system shall provide four identical Digital Output channels (DO1–DO4).                                                               | PRD-REQ-015          |
| SRS-REQ-DO-002 | Must     | Accepted | Each DO channel shall be manually assertable from the front panel in Phase 1.                                                            | PRD §9.5             |
| SRS-REQ-DO-003 | Must     | Accepted | Each DO channel shall provide local status indication.                                                                                   | PRD-REQ-032          |
| SRS-REQ-DO-004 | Must     | TBD      | DO electrical output type shall be defined (working options: logic-level push-pull, open-drain/open-collector, or selectable — **TBD**). | Needs product choice |
| SRS-REQ-DO-005 | Must     | TBD      | DO voltage and current drive capability shall be **TBD**.                                                                                |                      |
| SRS-REQ-DO-006 | Must     | Accepted | DO channels shall not be relied upon as dry-contact replacements; dry contacts are provided by RLY.                                      | PRD §9.6             |
| SRS-REQ-DO-007 | Must     | Accepted | DO interface design shall follow the hybrid isolation strategy in Section 13.                                                            | PRD-REQ-020          |


---



## 10. Relay Requirements (RLY)


| ID              | Priority | Status   | Requirement                                                                                                                     | Notes / Trace             |
| --------------- | -------- | -------- | ------------------------------------------------------------------------------------------------------------------------------- | ------------------------- |
| SRS-REQ-RLY-001 | Must     | Accepted | The system shall provide four identical relay/dry-contact channels (RLY1–RLY4).                                                 | PRD-REQ-015               |
| SRS-REQ-RLY-002 | Must     | Accepted | Each RLY channel shall provide a galvanically separated dry contact usable to simulate open/closed wiring conditions for a DUT. | PRD §9.6                  |
| SRS-REQ-RLY-003 | Must     | Accepted | Each RLY channel shall be manually controllable in Phase 1.                                                                     | PRD §9.6                  |
| SRS-REQ-RLY-004 | Must     | Accepted | Each RLY channel shall provide local contact-state indication.                                                                  | PRD §9.6                  |
| SRS-REQ-RLY-005 | Must     | TBD      | Contact form shall be **TBD** (working assumption: **SPST-NO** minimum; SPDT optional if panel space allows).                   |                           |
| SRS-REQ-RLY-006 | Must     | TBD      | Contact rating shall be at least **TBD V** / **TBD A** DC (and AC if claimed).                                                  | Bench-safe ratings needed |
| SRS-REQ-RLY-007 | Should   | TBD      | Expected mechanical/electrical endurance shall be documented as **TBD** operations.                                             |                           |


---



## 11. Power Requirements (Instrument Inlet + Auxiliary Outputs)



### 11.1 Instrument Input Power

TWIG Phase 1 is powered by an **external power brick**. Internal electronics
shall run on **DC**. The inlet shall accept either a **24 VDC** brick or a
**24 VAC** brick; when 24 VAC is supplied, TWIG shall rectify/convert it to
the internal DC rails required by the instrument.


| ID              | Priority | Status      | Requirement                                                                                                                                                            | Notes / Trace              |
| --------------- | -------- | ----------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------- |
| SRS-REQ-PWR-001 | Must     | Accepted    | The instrument shall be powered from an external power brick (no mandatory internal mains AC-DC PSU for Phase 1).                                                      | Replaces open inlet debate |
| SRS-REQ-PWR-002 | Must     | Accepted    | The instrument input shall accept a nominal **24 VDC** external brick.                                                                                                 |                            |
| SRS-REQ-PWR-003 | Must     | Accepted    | The instrument input shall accept a nominal **24 VAC** external brick.                                                                                                 |                            |
| SRS-REQ-PWR-004 | Must     | Accepted    | Regardless of whether the external brick supplies 24 VDC or 24 VAC, internal instrument electronics shall operate from internally derived **DC** rails.                |                            |
| SRS-REQ-PWR-005 | Must     | Accepted    | When powered from 24 VAC, the system shall include the conversion needed to produce the required internal DC supplies (e.g. rectification and regulation as designed). | Design detail later        |
| SRS-REQ-PWR-006 | Must     | Provisional | Nominal input rating shall be labeled on the instrument as supporting **24 VDC / 24 VAC** brick input.                                                                 | Silkscreen / plate         |
| SRS-REQ-PWR-007 | Must     | TBD         | Allowed input tolerance bands shall be defined (working assumption: approximately **±10%** on 24 V nominal — confirm as **TBD**).                                      | Brick compatibility        |
| SRS-REQ-PWR-008 | Must     | TBD         | Minimum input current / power rating of the accepted brick shall be **TBD W** (or **TBD A** at 24 V), sized for full instrument + aux-rail loading.                    |                            |
| SRS-REQ-PWR-009 | Must     | TBD         | Input connector polarity (for DC), pinout, and AC-safe connector choice shall be defined and labeled (**TBD**).                                                        | Safety / usability         |
| SRS-REQ-PWR-010 | Should   | Provisional | Reverse-polarity protection should be provided for DC-brick connection.                                                                                                |                            |
| SRS-REQ-PWR-011 | Must     | Provisional | The Phase 1 design shall not require the operator to install different inlet hardware to switch between 24 VDC and 24 VAC bricks (one compatible inlet concept).       |                            |




### 11.2 Auxiliary Output Rails (to DUT)


| ID              | Priority | Status   | Requirement                                                                                                                                                                                                            | Notes / Trace      |
| --------------- | -------- | -------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------ |
| SRS-REQ-PSU-001 | Must     | Accepted | The system shall provide auxiliary DC output rails of **5 V**, **12 V**, and **24 V**.                                                                                                                                 | PRD §9.7           |
| SRS-REQ-PSU-002 | Must     | TBD      | Continuous current capacity shall be at least: 5 V = **TBD A**, 12 V = **TBD A**, 24 V = **TBD A**.                                                                                                                    | PRD-OQ-005         |
| SRS-REQ-PSU-003 | Must     | TBD      | Output voltage tolerance under rated load shall be within **TBD %**.                                                                                                                                                   |                    |
| SRS-REQ-PSU-004 | Must     | TBD      | Each aux rail shall include overcurrent protection / current limiting with threshold **TBD**.                                                                                                                          |                    |
| SRS-REQ-PSU-005 | Should   | TBD      | Each aux rail should include reverse-connection and short-circuit survivability as specified in design (**TBD** behavior: hiccup / foldback / fuse).                                                                   |                    |
| SRS-REQ-PSU-006 | Must     | TBD      | Aux rail connectors shall be clearly labeled with voltage and polarity.                                                                                                                                                | Safety / usability |
| SRS-REQ-PSU-007 | Must     | TBD      | Whether aux rails share a common ground with specific I/O domains shall be defined in the isolation/grounding model (**TBD**).                                                                                         | Section 13         |
| SRS-REQ-PSU-008 | Must     | Accepted | Auxiliary output rails are distinct from the instrument input brick supply; DUT aux power shall be the regulated 5/12/24 VDC outputs, not the raw brick feed-through unless explicitly designed and labeled otherwise. | Clarification      |


---



## 12. Human Interface Requirements


| ID              | Priority | Status   | Requirement                                                                                                                                                                  | PRD Trace         |
| --------------- | -------- | -------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------- |
| SRS-REQ-HMI-001 | Must     | Accepted | Phase 1 essential functions shall be operable solely via physical controls and local indicators.                                                                             | PRD §7.1 / §10    |
| SRS-REQ-HMI-002 | Must     | Accepted | Controls for channels of the same family shall follow a uniform interaction pattern.                                                                                         | PRD-REQ-033       |
| SRS-REQ-HMI-003 | Should   | Accepted | Channels should be visually grouped by family on the operator interface.                                                                                                     | PRD §10.2         |
| SRS-REQ-HMI-004 | Must     | Accepted | AO control pattern shall include primary continuous control + range selector.                                                                                                | PRD §9.1.3        |
| SRS-REQ-HMI-005 | Must     | Accepted | RO control pattern shall include range selector + coarse + fine adjustment.                                                                                                  | PRD §9.3.3        |
| SRS-REQ-HMI-006 | Must     | Accepted | DI, DO, and RLY activity/state shall be locally visible.                                                                                                                     | PRD-REQ-032       |
| SRS-REQ-HMI-007 | Should   | TBD      | Display technology (panel meter, LED bar, module display, etc.) may differ by family but shall meet the indication requirements of each family; exact technology is **TBD**. | PRD-OQ-006        |
| SRS-REQ-HMI-008 | Must     | Accepted | No Phase 1 feature shall require a web UI or host application to demonstrate basic value.                                                                                    | PRD-NGOAL-001/002 |
| SRS-REQ-HMI-009 | Must     | Accepted | DI control pattern shall include a per-channel threshold selector with steps **5 V / 10 V / 15 V / 20 V**.                                                                   | DI-004 / DI-008   |


---



## 13. Isolation, Grounding, and Protection



### 13.1 Isolation Strategy


| ID              | Priority | Status   | Requirement                                                                                                                                                                                                                     | PRD Trace   |
| --------------- | -------- | -------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------- |
| SRS-REQ-ISO-001 | Must     | Accepted | The system shall implement a **hybrid galvanic isolation** strategy: isolate interfaces where industrial coupling and ground-loop risk justify it; avoid isolating every block by default.                                      | PRD-REQ-020 |
| SRS-REQ-ISO-002 | Must     | TBD      | Isolation domains shall be explicitly defined in architecture docs. Working assumption to validate: prioritize isolation for **DI, DO, current interfaces, and other industrial-facing paths**; finalize domain map as **TBD**. | PRD-OQ-004  |
| SRS-REQ-ISO-003 | Must     | TBD      | Minimum isolation rating between isolated domains shall be **TBD Vrms** (or equivalent DC withstand).                                                                                                                           |             |
| SRS-REQ-ISO-004 | Must     | TBD      | Allowed / forbidden inter-domain bonding options shall be documented for operators.                                                                                                                                             |             |




### 13.2 Protection


| ID               | Priority | Status   | Requirement                                                                                                                                                                                    | PRD Trace       |
| ---------------- | -------- | -------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------- |
| SRS-REQ-PROT-001 | Must     | Accepted | Each I/O family shall include practical protections appropriate to its role (examples: reverse polarity, overcurrent limiting, overvoltage clamping), with exact mechanisms defined in design. | PRD-REQ-021     |
| SRS-REQ-PROT-002 | Must     | TBD      | Fault behavior (output disable, current limit, self-recovery) shall be defined per family as **TBD**.                                                                                          |                 |
| SRS-REQ-PROT-003 | Should   | TBD      | The system should survive common bench miswires to the extent practical without cascading damage across unrelated families.                                                                    | Robustness goal |


---



## 14. Environmental and Mechanical Requirements

> These were not fully specified in the PRD. They are introduced here as
> suggested system requirements so architecture/mechanical design has a
> target. Values are provisional.


| ID               | Priority | Status      | Requirement                                                                                                                        | Notes          |
| ---------------- | -------- | ----------- | ---------------------------------------------------------------------------------------------------------------------------------- | -------------- |
| SRS-REQ-ENV-001  | Should   | Provisional | Operating ambient temperature: **0 °C to 40 °C** (lab use).                                                                        | Suggested      |
| SRS-REQ-ENV-002  | Should   | Provisional | Storage temperature: **-20 °C to 60 °C**.                                                                                          | Suggested      |
| SRS-REQ-ENV-003  | Should   | Provisional | Operating relative humidity: **10% to 80% non-condensing**.                                                                        | Suggested      |
| SRS-REQ-MECH-001 | Must     | Accepted    | The system shall be designed as a bench-top instrument (not rack-only).                                                            | PRD-GOAL-010   |
| SRS-REQ-MECH-002 | Should   | TBD         | Target main-board envelope remains a planning estimate only (**~300 × 200 mm** discussed); final enclosure dimensions are **TBD**. | PRD-OQ-007     |
| SRS-REQ-MECH-003 | Should   | Provisional | Operator controls and connectors shall be accessible from the primary working face without opening the enclosure.                  | Usability      |
| SRS-REQ-MECH-004 | May      | TBD         | Provision for future Phase 2 MCU board / daughtercard mounting may be reserved mechanically.                                       | Evolution hook |


---



## 15. Non-Functional Requirements


| ID              | Priority | Status      | Requirement                                                                                                                                             | Notes                    |
| --------------- | -------- | ----------- | ------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------ |
| SRS-REQ-NFR-001 | Must     | Accepted    | Official requirements/design documentation shall be maintained in English.                                                                              | PRD-REQ-002              |
| SRS-REQ-NFR-002 | Should   | Accepted    | Another engineer shall be able to understand system intent from Vision + PRD + SRS sufficiently to begin schematic partitioning.                        | PRD-GOAL-006             |
| SRS-REQ-NFR-003 | Should   | Provisional | Maintainability: channel circuits of a family should be repeatable and replaceable in concept (no one-off special channels).                            | PRD philosophy           |
| SRS-REQ-NFR-004 | Should   | TBD         | EMC emissions/immunity targets for a lab instrument shall be defined as **TBD** (even if formal certification is not a Phase 1 commercial goal).        | Suggested addition       |
| SRS-REQ-NFR-005 | Should   | Provisional | Electrical safety approach for the 24 VDC/24 VAC brick inlet and SELV-class internal domains shall be defined in architecture/design (**TBD** details). | Brick-powered instrument |
| SRS-REQ-NFR-006 | May      | Provisional | Phase 1 may use factory trim / manual calibration; certified metrology calibration is not required.                                                     | PRD-NGOAL-007            |


---



## 16. Phase 2 Reserved Requirements

These are **not** Phase 1 acceptance criteria. They exist to prevent
architecture dead-ends.


| ID             | Priority | Status   | Reserved Capability                                                                                                 |
| -------------- | -------- | -------- | ------------------------------------------------------------------------------------------------------------------- |
| SRS-REQ-P2-001 | May      | Reserved | Supervisory MCU may read channel states and setpoints.                                                              |
| SRS-REQ-P2-002 | May      | Reserved | Host communication (USB and/or Ethernet) may be added.                                                              |
| SRS-REQ-P2-003 | May      | Reserved | Saved presets / profiles may be supported.                                                                          |
| SRS-REQ-P2-004 | May      | Reserved | Digital calibration coefficients may be supported.                                                                  |
| SRS-REQ-P2-005 | May      | Reserved | Automated test sequences may be supported.                                                                          |
| SRS-REQ-P2-006 | Must     | Reserved | Phase 2 features shall not remove the ability to operate essential Phase 1 functions manually from the front panel. |


---



## 17. Requirements Explicitly Out of Scope (Phase 1)

The system **shall not** be required, in Phase 1, to provide:


| ID          | Out-of-scope item                           | Trace         |
| ----------- | ------------------------------------------- | ------------- |
| SRS-OOS-001 | Web user interface                          | PRD-NGOAL-001 |
| SRS-OOS-002 | Mandatory MCU/software control              | PRD-NGOAL-002 |
| SRS-OOS-003 | Network communication services              | PRD-NGOAL-003 |
| SRS-OOS-004 | Built-in data logging / SCADA               | PRD-NGOAL-004 |
| SRS-OOS-005 | Universal multi-role terminals              | PRD-NGOAL-005 |
| SRS-OOS-006 | Built-in temperature sensor modes/curves    | PRD-NGOAL-006 |
| SRS-OOS-007 | Metrology-grade certification package       | PRD-NGOAL-007 |
| SRS-OOS-008 | Modular one-card-per-family V1 architecture | PRD-NGOAL-008 |


---



## 18. Verification Overview

Detailed procedures belong in a future Verification Plan. This section
only assigns an expected verification method class per domain.


| Domain        | Typical methods                                               |
| ------------- | ------------------------------------------------------------- |
| GEN / HMI     | Inspection, demonstration                                     |
| AO / AI / RO  | Measurement with reference instruments                        |
| DI / DO / RLY | Functional exercise with known stimuli/loads                  |
| PSU / PWR     | Inlet brick DC+AC functional check; aux load/protection tests |
| ISO / PROT    | Insulation test, fault-injection / miswire checks where safe  |
| ENV / MECH    | Inspection; environmental tests only if later required        |


Each `SRS-REQ-*` with status Accepted or Provisional shall eventually map
to at least one verification case.

---



## 19. Open Items and Working Assumptions



### 19.1 Open Items Requiring Decision


| ID           | Topic                         | Status / suggested default                         | Blocks                                |
| ------------ | ----------------------------- | -------------------------------------------------- | ------------------------------------- |
| SRS-OPEN-001 | AO max voltage full-scale     | **Closed** — ≈0–25 VDC                             | —                                     |
| SRS-OPEN-002 | Discrete AO voltage ranges    | 0–5 / 0–10 / 0–15 / 0–25 V                         | Front panel + analog chain            |
| SRS-OPEN-003 | RO ohmic span and decades     | e.g. 0–100 Ω, 0–1 kΩ, 0–10 kΩ (confirm)            | RO architecture                       |
| SRS-OPEN-004 | DO output topology            | open-drain vs push-pull vs selectable              | DO stage                              |
| SRS-OPEN-005 | DI threshold family           | **Closed** — selectable **5 / 10 / 15 / 20 V**     | —                                     |
| SRS-OPEN-006 | RLY contact form & rating     | SPST-NO, 30 VDC / 1–2 A class                      | Relay selection                       |
| SRS-OPEN-007 | Aux rail current ratings      | e.g. 5 V@2 A, 12 V@1 A, 24 V@0.5 A                 | Power architecture                    |
| SRS-OPEN-008 | Isolation domain map          | hybrid: isolate DI/DO/current paths first          | Electrical architecture               |
| SRS-OPEN-009 | Local display technology      | cheap panel meters vs shared display bus           | HMI / BOM                             |
| SRS-OPEN-010 | Instrument power inlet        | **Closed** — external brick, **24 VDC or 24 VAC**  | Input tolerance / connector still TBD |
| SRS-OPEN-011 | Brick input tolerance / power | ±10% / minimum VA or W rating                      | PWR-007, PWR-008                      |




### 19.2 Suggested Additional Requirement Areas

The following were only lightly covered in the PRD and are proposed as
SRS extensions (some already stubbed above):

1. **Grounding model** — common returns, star points, banana-jack grounds.
2. **Simultaneous loading** — all channels active at once vs derating rules.
3. **Warm-up time** — time after power-on before accuracy claims apply.
4. **EMC / ESD** — even for a personal lab instrument.
5. **Cable / connector standard** — terminal blocks, bananas, pluggables.
6. **Interlocks** — e.g. disable AO when range/mode is being changed.
7. **Firmware-less helpers** — whether discrete logic / LED drivers / meter ICs are explicitly allowed in Phase 1.
8. **Naming & silkscreen rules** — channel IDs, polarity marks, warning marks.
9. **Serviceability** — fuse access, calibration access points.
10. **Acoustic / thermal** — fan allowed or forced passive cooling.

---



## 20. Traceability to PRD


| PRD item                    | SRS coverage                                     |
| --------------------------- | ------------------------------------------------ |
| PRD-REQ-001..003            | GEN-001, NFR-001                                 |
| PRD-REQ-010..015            | GEN-002..007, family *-001 requirements          |
| PRD-REQ-020..022            | ISO-*, PROT-*, GEN-012                           |
| PRD-REQ-030..033            | AO/RO/HMI indication and uniformity requirements |
| PRD §9 feature descriptions | Sections 5–11                                    |
| PRD-NGOAL-*                 | Section 17                                       |
| PRD-OQ-*                    | Section 19 + TBD fields                          |


---



## 21. Glossary


| Term             | Meaning in this SRS                                  |
| ---------------- | ---------------------------------------------------- |
| Shall / Must     | Binding Phase 1 requirement                          |
| Provisional      | Working assumption pending confirmation              |
| TBD              | Value intentionally unset                            |
| Reserved         | Phase 2 intent; not Phase 1 acceptance               |
| DUT              | Device Under Test                                    |
| Hybrid isolation | Selective galvanic isolation by domain               |
| Local indication | Operator-visible indication on the instrument itself |


---



## Document End

**Status:** Draft  
**Next expected document:** System / Electrical Architecture (`03_...`)  
**Immediate next step:** Resolve remaining high-impact open items in §19.1 (especially RO decades, DO topology, aux-rail current ratings, and brick input tolerance/connector).