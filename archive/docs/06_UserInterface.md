# User Interface Design Document

| Field | Value |
|-------|-------|
| Document ID | UID-001 |
| Project | TWIG – Test & Waveform Interface for GPIO |
| Version | 0.1.0 |
| Status | Draft |
| Author | Mede Gbaguidi |
| Language | English |
| Last Updated | 2026-07-31 |
| Related Documents | [01_PRD.md](01_PRD.md), [02_SRS.md](02_SRS.md), [03_SystemArchitecture.md](03_SystemArchitecture.md), [05_HardwareDesign.md](05_HardwareDesign.md) |

## Version History

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 0.1.0 | 2026-07-31 | Mede Gbaguidi | Initial Phase 1 front-panel UI design |

## Table of Contents

1. [Introduction](#1-introduction)
2. [UI Goals and Principles](#2-ui-goals-and-principles)
3. [Operator Workflows](#3-operator-workflows)
4. [Front-Panel Organization](#4-front-panel-organization)
5. [Per-Family Control Grammar](#5-per-family-control-grammar)
6. [Indication and Labeling](#6-indication-and-labeling)
7. [Panel Layout Proposal](#7-panel-layout-proposal)
8. [Accessibility and Ergonomics](#8-accessibility-and-ergonomics)
9. [Out of Scope for Phase 1 UI](#9-out-of-scope-for-phase-1-ui)
10. [Open UI Items](#10-open-ui-items)

---

## 1. Introduction

### 1.1 Purpose

This User Interface Design Document defines the **Phase 1 front-panel experience**
for TWIG.

It answers:

> How does an operator control and understand the instrument without software?

### 1.2 Scope

- panel organization;
- control grammar per family;
- labeling and indication rules;
- primary operator workflows.

No web UI, host GUI, or touchscreen is in Phase 1 scope.

---

## 2. UI Goals and Principles

| ID | Principle |
|----|-----------|
| UID-GOAL-001 | Discoverable without a software manual for basic use. |
| UID-GOAL-002 | Uniform controls within each family. |
| UID-GOAL-003 | Impossible-to-miss terminal role labels (AO vs AI vs DI…). |
| UID-GOAL-004 | Local indication for every active channel function. |
| UID-GOAL-005 | One-handed bench operation where practical. |
| UID-GOAL-006 | Phase 2 may add screens later; it must not be required now. |

### 2.1 Design Language

- Family blocks with clear separators.
- Channel index always visible (`AO1`…`AO4`).
- Range/threshold selectors use the same interaction metaphor across AO/DI/RO.
- Status LEDs share common active = illuminated semantics.

---

## 3. Operator Workflows

### 3.1 Power Up

1. Connect 24 VDC or 24 VAC brick to inlet.  
2. Confirm power presence indicator.  
3. Verify aux rails only if needed for DUT.  
4. Connect DUT cables to the intended family terminals.

### 3.2 Generate Analog Stimulus (AO)

1. Select channel.  
2. Select V or I mode.  
3. Select range (e.g. 0–5 / 0–10 / 0–15 / 0–25).  
4. Adjust primary control.  
5. Confirm value on local meter.  
6. Apply to DUT.

### 3.3 Measure Analog Signal (AI)

1. Select channel / mode.  
2. Connect DUT signal.  
3. Read local meter.

### 3.4 Simulate Resistance (RO)

1. Select range decade.  
2. Adjust coarse, then fine.  
3. Confirm indicated ohms.  
4. Connect to DUT sense circuit.

### 3.5 Observe Digital Input (DI)

1. Select threshold (5 / 10 / 15 / 20 V).  
2. Connect DC signal.  
3. Observe active/inactive LED.  
4. Reminder: **DC only** for correct detection.

### 3.6 Assert DO / Relay

1. Toggle channel control.  
2. Confirm LED.  
3. Verify DUT response.

---

## 4. Front-Panel Organization

### 4.1 Primary Zones

| Zone | Content |
|------|---------|
| Power zone | Brick inlet, power LED, fuse access if any |
| Aux zone | 5 / 12 / 24 VDC outputs + polarity marks |
| AO zone | 4 channel strips |
| AI zone | 4 channel strips |
| RO zone | 4 channel strips |
| DI zone | 4 channel strips |
| DO zone | 4 channel strips |
| RLY zone | 4 channel strips |
| Legend / warnings | DC-only DI note, isolation notes, rating plate |

### 4.2 Channel Strip Rule

Every channel strip contains, left-to-right (or top-to-bottom consistently):

1. channel ID  
2. mode/range controls (as applicable)  
3. primary adjustment  
4. indicator/meter  
5. terminal pair  

---

## 5. Per-Family Control Grammar

### 5.1 AO

| Control | Type | Function |
|---------|------|----------|
| Mode | Switch | Voltage / Current |
| Range | Selector | Full-scale multiplier (incl. up to ~25 V) |
| Setpoint | Slider or multiturn pot | Continuous command |
| Meter | Numeric / panel meter | Local V or I indication |
| Output jacks | Pair | AO+ / return |

### 5.2 AI

| Control | Type | Function |
|---------|------|----------|
| Mode | Switch | Voltage / Current |
| Range | Selector (if needed) | Measurement span |
| Meter | Numeric / panel meter | Measured value |
| Input jacks | Pair | AI+ / return |

### 5.3 RO

| Control | Type | Function |
|---------|------|----------|
| Range | Selector | Decade / ohmic span |
| Coarse | Pot/slider | Large R change |
| Fine | Pot | Small R change |
| Meter | Resistance indication | Local ohms |
| Sense jacks | Pair | RO network |

### 5.4 DI

| Control | Type | Function |
|---------|------|----------|
| Threshold | Selector | **5 / 10 / 15 / 20 V** |
| Active LED | Indicator | Vin ≥ threshold |
| Input jacks | Pair | DI signal / return |
| Label | Text | `DI: DC only` |

### 5.5 DO

| Control | Type | Function |
|---------|------|----------|
| Assert | Switch/button | Output active |
| Status LED | Indicator | Asserted state |
| Output jacks | Pair | DO / return |

### 5.6 RLY

| Control | Type | Function |
|---------|------|----------|
| Assert | Switch | Energize coil |
| Status LED | Indicator | Coil/contact state |
| Contact jacks | 2 (NO) or 3 (SPDT) | Dry contact |

### 5.7 AUX / Power

| Control | Type | Function |
|---------|------|----------|
| Power LED | Indicator | Brick power present |
| Aux jacks | Labeled outputs | 5 / 12 / 24 VDC + polarity |
| Inlet mark | Text | `24 VDC / 24 VAC` |

---

## 6. Indication and Labeling

### 6.1 Mandatory Labels

- Family and channel IDs on every strip.  
- Polarity for all DC power and polarized signal ports.  
- Inlet: `24 VDC / 24 VAC`.  
- DI: `DC only` (until AC policy changes).  
- Ratings plate: model name **TWIG**, document revision pointer optional.

### 6.2 LED Semantics

| State | Meaning |
|-------|---------|
| Off | Inactive / deasserted / below threshold |
| On | Active / asserted / above threshold |
| Blink | Reserved (fault) — optional later |

Avoid using blink for normal Phase 1 states.

### 6.3 Meter Semantics

- AO meter shows commanded/applied quantity in selected mode.  
- AI meter shows measured quantity.  
- RO meter shows simulated resistance.  
- Units must be printed beside each meter (`V`, `mA`, `Ω`).

---

## 7. Panel Layout Proposal

Logical layout (not final CAD):

```text
+-----------------------------------------------------------------------+
| TWIG                          [POWER IN 24VDC/VAC] [PWR LED] [FUSE]   |
| AUX: [5V] [12V] [24V]                                                 |
+-----------------------------------------------------------------------+
| AO1 .... AO2 .... AO3 .... AO4                                        |
| AI1 .... AI2 .... AI3 .... AI4                                        |
| RO1 .... RO2 .... RO3 .... RO4                                        |
+-----------------------------------------------------------------------+
| DI1 .... DI2 .... DI3 .... DI4     (threshold selectors + LEDs)       |
| DO1 .... DO2 .... DO3 .... DO4                                        |
| RLY1 ... RLY2 ... RLY3 ... RLY4                                       |
+-----------------------------------------------------------------------+
| Notes: DI = DC only | Hybrid isolation | See docs                     |
+-----------------------------------------------------------------------+
```

Keep analog families together and digital/relay families together to reduce
operator confusion and wiring mistakes.

---

## 8. Accessibility and Ergonomics

| Topic | Guidance |
|-------|----------|
| Control spacing | Enough for adult fingers with banana plugs inserted |
| Contrast | High-contrast engraved or printed labels |
| Detents | Selectors for ranges/thresholds should detent clearly |
| Accidental bump | Avoid placing aux high-current jacks where elbows rest |
| Service | Fuse and critical markings readable without opening enclosure |

---

## 9. Out of Scope for Phase 1 UI

- web/mobile apps;
- touchscreen menus;
- temperature-mode RO UI;
- multi-page configuration wizards;
- mandatory host software.

---

## 10. Open UI Items

| ID | Item |
|----|------|
| UID-OPEN-001 | Final choice: slider vs multiturn pot for AO setpoint |
| UID-OPEN-002 | Meter technology (discrete panel modules vs shared display) |
| UID-OPEN-003 | Exact AO range labeling set |
| UID-OPEN-004 | Banana vs terminal block visual standard |
| UID-OPEN-005 | Enclosure color / print method |
| UID-OPEN-006 | Whether SPDT relay needs 3 jacks on panel |

---

## Document End

**Status:** Draft  
**Implements:** SRS HMI requirements and SAD HMI architecture  
**Next related:** panel drawing in mechanical/CAD package during hardware build
