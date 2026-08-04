# Verification Plan

| Field | Value |
|-------|-------|
| Document ID | VTP-001 |
| Project | TWIG – Test & Waveform Interface for GPIO |
| Version | 0.1.0 |
| Status | Draft |
| Author | Mede Gbaguidi |
| Language | English |
| Last Updated | 2026-07-31 |
| Related Documents | [01_PRD.md](01_PRD.md), [02_SRS.md](02_SRS.md), [03_SystemArchitecture.md](03_SystemArchitecture.md), [04_ElectricalArchitecture.md](04_ElectricalArchitecture.md), [05_HardwareDesign.md](05_HardwareDesign.md), [06_UserInterface.md](06_UserInterface.md) |

## Version History

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 0.1.0 | 2026-07-31 | Mede Gbaguidi | Initial Phase 1 verification plan mapped to SRS requirements |

## Table of Contents

1. [Introduction](#1-introduction)
2. [Verification Strategy](#2-verification-strategy)
3. [Equipment and Conditions](#3-equipment-and-conditions)
4. [Inspection Cases](#4-inspection-cases)
5. [Functional Test Cases](#5-functional-test-cases)
6. [Electrical Measurement Cases](#6-electrical-measurement-cases)
7. [Protection and Robustness Cases](#7-protection-and-robustness-cases)
8. [UI / Usability Checks](#8-ui--usability-checks)
9. [Traceability Matrix](#9-traceability-matrix)
10. [Pass / Fail and Records](#10-pass--fail-and-records)
11. [Open Verification Items](#11-open-verification-items)

---

## 1. Introduction

### 1.1 Purpose

This Verification Plan defines **how TWIG Phase 1 will be shown to satisfy**
the SRS (and related architecture constraints).

It answers:

> How do we prove the instrument does what it shall do?

### 1.2 Scope

Phase 1 prototype and release candidates:

- inspection;
- functional demonstration;
- electrical measurement;
- basic robustness checks.

Out of scope for V1 unless later required:

- formal EMC certification campaign;
- metrology accreditation;
- Phase 2 software automation tests.

### 1.3 Methods

| Code | Method |
|------|--------|
| I | Inspection |
| D | Demonstration |
| T | Test / measurement |
| A | Analysis (calculation / design review) |

---

## 2. Verification Strategy

1. Verify documentation identity and labeling first.  
2. Bring up power tree safely.  
3. Validate each family with one golden channel, then replicate across ×4.  
4. Confirm independence (changing CH1 does not force CH2).  
5. Run selected miswire/protection tests last.

Prototype levels:

| Level | Intent |
|-------|--------|
| L0 Smoke | Powers on, no smoke, basic indicators |
| L1 Family bring-up | Each family works on ≥1 channel |
| L2 Full channel matrix | All 4 channels per family |
| L3 Robustness | Limits, shorts, selected faults |
| L4 Acceptance | Traceability matrix complete for Must items |

---

## 3. Equipment and Conditions

### 3.1 Recommended Equipment

- 24 VDC brick and 24 VAC brick (both required for inlet tests);
- DMM (voltage/current/resistance);
- electronic load or resistor box;
- adjustable DC source for AI/DI stimulus;
- oscilloscope (noise/troubleshooting);
- thermometer optional for warm-up notes.

### 3.2 Standard Conditions (provisional)

| Parameter | Value |
|-----------|-------|
| Ambient | 15–30 °C lab |
| Warm-up | TBD minutes before accuracy claims |
| Aux loading | As specified per case |

---

## 4. Inspection Cases

| ID | Check | Method | SRS anchors |
|----|-------|--------|-------------|
| VTP-I-001 | Product labeled TWIG; docs English | I | GEN-001, NFR-001 |
| VTP-I-002 | Families present: AO/AI/RO/DI/DO/RLY ×4 + aux 5/12/24 | I | GEN-006, PSU-001 |
| VTP-I-003 | Terminals segregated by role; no universal multi-role jacks | I | GEN-004/005/008/012 |
| VTP-I-004 | Inlet marked 24 VDC / 24 VAC | I | PWR-006 |
| VTP-I-005 | DI marked DC only (until policy changes) | I | UI / DI policy |
| VTP-I-006 | Channel controls uniform within each family | I | HMI-002, GEN-007 |

---

## 5. Functional Test Cases

### 5.1 Power

| ID | Case | Steps (summary) | Expected | Anchors |
|----|------|-----------------|----------|---------|
| VTP-F-PWR-001 | DC brick boot | Apply 24 VDC brick | Unit powers; PWR LED on | PWR-001/002/004 |
| VTP-F-PWR-002 | AC brick boot | Apply 24 VAC brick | Unit powers; internals DC | PWR-003/005 |
| VTP-F-PWR-003 | Aux presence | Measure aux jacks no/min load | ~5 / ~12 / ~24 VDC present | PSU-001 |
| VTP-F-PWR-004 | No firmware dependency | Operate AO/DI/RO with no MCU/host | All essential functions work | GEN-002, HMI-001/008 |

### 5.2 AO

| ID | Case | Expected | Anchors |
|----|------|----------|---------|
| VTP-F-AO-001 | Each AO channel adjustable independently | CH setpoint changes only that CH | AO-001/019/022 |
| VTP-F-AO-002 | Voltage mode ranges include 0–5 and 0–10; max ~0–25 | Ranges selectable; FS ≈25 V available | AO-010/011 |
| VTP-F-AO-003 | Current mode 0–20 mA class | Output current controllable in class | AO-012 |
| VTP-F-AO-004 | Local indication | Meter tracks setpoint usefully | AO-023 |
| VTP-F-AO-005 | V/I mode selection | Mode switch changes behavior | AO-003/025 |

### 5.3 AI

| ID | Case | Expected | Anchors |
|----|------|----------|---------|
| VTP-F-AI-001 | Voltage measure | Local reading tracks source | AI-002/020 |
| VTP-F-AI-002 | Current measure 0–20 mA class | Local reading tracks source | AI-011/020 |
| VTP-F-AI-003 | No resistance mode offered | No R-measure UI/path | AI-003 |

### 5.4 RO

| ID | Case | Expected | Anchors |
|----|------|----------|---------|
| VTP-F-RO-001 | Range select + coarse/fine | R changes as expected | RO-003/012/020 |
| VTP-F-RO-002 | Local R indication | Display matches DMM within TBD tolerance | RO-021 |
| VTP-F-RO-003 | No temperature modes | Only ohmic ranges | RO-004 |

### 5.5 DI

| ID | Case | Expected | Anchors |
|----|------|----------|---------|
| VTP-F-DI-001 | Threshold steps exist | Selector has 5/10/15/20 V | DI-004/008 |
| VTP-F-DI-002 | Active above threshold | LED on when Vin ≥ Vth | DI-009/003 |
| VTP-F-DI-003 | Inactive below threshold | LED off when Vin < Vth | DI-009 |
| VTP-F-DI-004 | Per-channel selector | Each DI selectable independently | DI-001/008 |

### 5.6 DO / RLY

| ID | Case | Expected | Anchors |
|----|------|----------|---------|
| VTP-F-DO-001 | Manual assert + LED | Output/LED follow switch | DO-002/003 |
| VTP-F-RLY-001 | Contact open/close | Continuity follows switch | RLY-002/003/004 |
| VTP-F-RLY-002 | Dry contact independence from DO | Relay path is contact-based | DO-006 |

---

## 6. Electrical Measurement Cases

Numeric limits remain **TBD** until SRS accuracy fields are frozen. Record
measured values even before limits exist.

| ID | Measurement | Record | Anchors |
|----|-------------|--------|---------|
| VTP-T-AO-001 | AO voltage accuracy vs DMM on each range | err % / mV | AO-014 |
| VTP-T-AO-002 | AO current accuracy | err % / µA | AO-015 |
| VTP-T-AO-003 | Current-mode compliance voltage | V | AO-016 |
| VTP-T-AI-001 | AI voltage/current accuracy | err | AI-012/013 |
| VTP-T-RO-001 | RO accuracy per range | err % / Ω | RO-013 |
| VTP-T-DI-001 | Threshold actual vs nominal 5/10/15/20 | V | DI-010 |
| VTP-T-DI-002 | Hysteresis width | V | DI-009 |
| VTP-T-PSU-001 | Aux voltage vs load | V, A | PSU-002/003 |
| VTP-T-PWR-001 | Inlet tolerance band behavior | boot/no-boot | PWR-007 |

---

## 7. Protection and Robustness Cases

Perform only with current-limited sources and safety awareness.

| ID | Case | Intent | Anchors |
|----|------|--------|---------|
| VTP-R-001 | AO short to return | Survives / current limits | PROT-001/002, AO-018 |
| VTP-R-002 | AUX short | Protection engages; recovers or fails safe | PSU-004/005 |
| VTP-R-003 | DC reverse on inlet (if connector allows) | No damage | PWR-010 |
| VTP-R-004 | DI overvoltage within abs-max TBD | No damage | DI-005 |
| VTP-R-005 | Cross-family fault containment | Fault on one family doesn’t kill another | PROT-003 |
| VTP-R-006 | DI AC exposure (characterization) | Document behavior; not a functional pass for detection | DI open policy |

VTP-R-006 is characterization until an AC survivability requirement is accepted.

---

## 8. UI / Usability Checks

| ID | Check | Expected | Anchors |
|----|-------|----------|---------|
| VTP-U-001 | New operator sets AO without host software | Success using panel only | HMI-001/008 |
| VTP-U-002 | Family grouping understandable | Operator finds AO/AI/DI quickly | HMI-003 |
| VTP-U-003 | DI threshold grammar matches AO-style selector expectation | Operator uses without training beyond labels | HMI-009 |
| VTP-U-004 | Labels prevent AI/AO swap mistakes | Clear role marking | GEN-012 |

---

## 9. Traceability Matrix

> Summary map. Expand to a spreadsheet during execution if desired.

| SRS area | Primary verification IDs |
|----------|--------------------------|
| GEN-* | VTP-I-001..006, VTP-F-PWR-004 |
| AO-* | VTP-F-AO-*, VTP-T-AO-*, VTP-R-001 |
| AI-* | VTP-F-AI-*, VTP-T-AI-* |
| RO-* | VTP-F-RO-*, VTP-T-RO-* |
| DI-* | VTP-F-DI-*, VTP-T-DI-*, VTP-R-004/006 |
| DO-* | VTP-F-DO-001 |
| RLY-* | VTP-F-RLY-* |
| PWR-* | VTP-F-PWR-*, VTP-T-PWR-*, VTP-R-003 |
| PSU-* | VTP-F-PWR-003, VTP-T-PSU-*, VTP-R-002 |
| HMI-* | VTP-U-* |
| ISO/PROT-* | VTP-R-*, plus isolation withstand when rated |
| P2 reserved | Not required for Phase 1 acceptance |

Must-priority Accepted SRS requirements shall have at least one Pass record
before Phase 1 acceptance.

---

## 10. Pass / Fail and Records

### 10.1 Result Codes

| Result | Meaning |
|--------|---------|
| PASS | Meets stated expected behavior / limit |
| FAIL | Does not meet |
| BLOCKED | Cannot run (missing equipment / unsafe) |
| TBD-LIMIT | Executed; limit not yet frozen; value logged |
| WAIVED | Explicit written waiver |

### 10.2 Minimum Record Fields

For each case:

- Case ID  
- Date / operator  
- DUT serial or prototype ID  
- Equipment used  
- Result code  
- Measured data  
- Notes / defects  

### 10.3 Defect Handling

Failures open a defect note referencing:

- SRS ID;
- observed vs expected;
- severity (safety / functional / cosmetic);
- proposed fix.

---

## 11. Open Verification Items

| ID | Item |
|----|------|
| VTP-OPEN-001 | Freeze numeric accuracy limits currently TBD in SRS |
| VTP-OPEN-002 | Warm-up time definition |
| VTP-OPEN-003 | Formal isolation withstand procedure/voltage |
| VTP-OPEN-004 | Whether AC-on-DI survivability becomes a Must test |
| VTP-OPEN-005 | Aux load derating matrix for simultaneous family use |

---

## Document End

**Status:** Draft  
**Acceptance gate:** L4 matrix complete for Phase 1 Must requirements with no open safety FAIL.
