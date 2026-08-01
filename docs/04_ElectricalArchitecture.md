# Electrical Architecture Document

| Field | Value |
|-------|-------|
| Document ID | EAD-001 |
| Project | TWIG – Test & Waveform Interface for GPIO |
| Version | 0.1.0 |
| Status | Draft |
| Author | Mede Gbaguidi |
| Language | English |
| Last Updated | 2026-07-31 |
| Related Documents | [00_ProjectVision.md](00_ProjectVision.md), [01_PRD.md](01_PRD.md), [02_SRS.md](02_SRS.md), [03_SystemArchitecture.md](03_SystemArchitecture.md) |

## Version History

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 0.1.0 | 2026-07-31 | Mede Gbaguidi | Initial electrical architecture; provisional values marked where not frozen |

## Table of Contents

1. [Introduction](#1-introduction)
2. [Electrical Design Goals](#2-electrical-design-goals)
3. [Power Tree](#3-power-tree)
4. [Isolation Domains](#4-isolation-domains)
5. [Per-Family Electrical Blocks](#5-per-family-electrical-blocks)
6. [Protection Concept](#6-protection-concept)
7. [Signal Integrity and Analog Concerns](#7-signal-integrity-and-analog-concerns)
8. [Connector and Return Strategy](#8-connector-and-return-strategy)
9. [Working Assumptions](#9-working-assumptions)
10. [Open Electrical Items](#10-open-electrical-items)
11. [Traceability](#11-traceability)

---

## 1. Introduction

### 1.1 Purpose

This Electrical Architecture Document (EAD) defines **how electrical energy and
signals flow** inside TWIG.

It answers:

> How are rails, domains, and channel circuits organized electrically?

It refines [03_SystemArchitecture.md](03_SystemArchitecture.md) without freezing
final schematic part numbers (those belong in Hardware Design).

### 1.2 Scope

- power conversion tree;
- isolation / return domains;
- per-family electrical block diagrams;
- protection intent;
- connector/return conventions;
- provisional electrical budgets.

Out of scope:

- final BOM and footprint choices;
- PCB stack-up / routing rules (Hardware Design);
- front-panel cosmetics (UI Design);
- test procedures (Verification Plan).

---

## 2. Electrical Design Goals

| ID | Goal |
|----|------|
| EAD-GOAL-001 | Derive all internal electronics from a 24 VDC or 24 VAC external brick. |
| EAD-GOAL-002 | Keep DUT aux rails (5/12/24 VDC) regulated and distinct from raw inlet. |
| EAD-GOAL-003 | Make the four channels of each family electrically identical. |
| EAD-GOAL-004 | Prefer hybrid isolation: isolate industrial-facing DI/DO paths; avoid over-isolating low-risk blocks. |
| EAD-GOAL-005 | Survive common bench miswires within stated limits without cascading family-to-family damage. |
| EAD-GOAL-006 | Leave measurement taps / sense points usable for Phase 2 readback later. |

---

## 3. Power Tree

### 3.1 Top-Level Power Flow

```text
 External brick
  24 VDC  OR  24 VAC
        |
        v
 +----------------------+
 | Inlet connector      |
 | Fuse / inrush limit  |
 | TVS / EMC filter TBD |
 +----------+-----------+
            |
            +-- if VAC --> bridge rectifier + bulk cap
            |
            v
     Primary DC bus  (~24 VDC bulk, name: VBUS)
            |
            +--> Aux regulators -----> +5V_AUX, +12V_AUX, +24V_AUX (DUT)
            |
            +--> System regulators --> VSYS (logic/HMI helpers)
            |                      --> VAN (analog clean rail(s))
            |                      --> VREF (precision references)
            |
            +--> Isolated DC/DC (as required) --> DI/DO isolated rails
```

### 3.2 Rail Definitions (logical)

| Rail | Nominal | Role | Status |
|------|---------|------|--------|
| VBUS | ~24 VDC bulk | Primary internal bus after inlet conditioning | Accepted concept |
| VSYS | TBD (e.g. 5 V or 3.3 V) | Non-isolated helpers, indicators, low-power logic | Provisional |
| VAN | TBD | Analog front-end / AO driver supplies | Provisional |
| VREF | TBD | Precision references for meters/comparators | Provisional |
| VISO_DI | TBD | Isolated supply for DI bank (if bank-isolated) | Provisional |
| VISO_DO | TBD | Isolated supply for DO bank (if bank-isolated) | Provisional |
| +5V_AUX | 5 VDC | DUT auxiliary output | Accepted |
| +12V_AUX | 12 VDC | DUT auxiliary output | Accepted |
| +24V_AUX | 24 VDC | DUT auxiliary output | Accepted |

### 3.3 Inlet Rules

| ID | Rule |
|----|------|
| EAD-PWR-001 | One inlet shall accept 24 VDC and 24 VAC bricks without hardware swap. |
| EAD-PWR-002 | VAC path shall rectify to VBUS; VDC path shall feed VBUS with reverse-polarity protection. |
| EAD-PWR-003 | Inlet labeling shall state **24 VDC / 24 VAC**. |
| EAD-PWR-004 | Minimum brick rating is **TBD W** (must cover I/O + rated aux simultaneously). |
| EAD-PWR-005 | Working tolerance assumption: **±10%** on 24 V nominal (confirm). |

### 3.4 Auxiliary Output Budget (working assumption)

| Rail | Working current target | Notes |
|------|------------------------|-------|
| 5 V | 2.0 A | Embedded boards / sensors |
| 12 V | 1.0 A | Common industrial intermediate |
| 24 V | 0.5 A | Light 24 V DUT loads |

These are **planning defaults**, not frozen SRS acceptance values until confirmed.

---

## 4. Isolation Domains

### 4.1 Domain Map (working proposal)

```text
 +---------------- DOM-PWR ------------------+
 | Inlet, fuse, rectifier, VBUS creators     |
 +--------------------+----------------------+
                      | regulated / filtered
                      v
 +---------------- DOM-CORE -----------------+
 | VSYS/VAN/VREF, HMI low-level, AO/AI/RO    |
 | shared returns (AGND/DGND strategy TBD)   |
 +------+-------------+-------------+--------+
        |             |             |
        | isol.       | isol.       | contacts
        v             v             v
 +--DOM-DI--+   +--DOM-DO--+   +--DOM-RLY--+
 | DI x4    |   | DO x4    |   | contact   |
 | isolated |   | isolated |   | sets only |
 +----------+   +----------+   +-----------+

 +------------- DOM-AUX ---------------+
 | 5/12/24 DUT outputs (bonding TBD)  |
 +------------------------------------+
```

### 4.2 Domain Rules

| Domain | Isolation intent | Working decision |
|--------|------------------|------------------|
| DOM-PWR | Protective entry stage | Always present |
| DOM-CORE | Shared instrument core | AO, AI, RO share CORE in V1 unless proven otherwise |
| DOM-DI | Prefer galvanic isolation from CORE | **Yes (provisional)** |
| DOM-DO | Prefer galvanic isolation from CORE | **Yes (provisional)** |
| DOM-RLY | Contact side isolated by relay construction | **Yes** |
| DOM-AUX | May share CORE return or be quasi-isolated | **Open** — default share CORE with clear labeling |

### 4.3 Isolation Rating (provisional)

Working target between isolated domains: **1 kVrms / 1 min** functional isolation class for bench use (not a certified product claim yet). Final rating TBD in Hardware Design / safety review.

---

## 5. Per-Family Electrical Blocks

### 5.1 AO Channel Block

```text
 [Mode: V/I] + [Range 0-5/10/15/25] + [Primary pot/slider]
                    |
                    v
            Setpoint conditioner
                    |
        +-----------+-----------+
        | voltage path          | current path
        v                       v
   buffered V out          V-to-I stage
        |                       |
        +-----------+-----------+
                    |
              protection
                    |
                 AO out
                    |
              local meter
```

**Electrical notes:**

- max voltage full-scale ≈ **25 VDC**;
- current modes: 0–20 mA and 4–20 mA class;
- compliance voltage and accuracy TBD;
- preferred implementation family: analog setpoint path (no MCU DAC required in Phase 1).

### 5.2 AI Channel Block

```text
 AI in --> protection --> mode (V/I) --> scaling --> measure/conditioning --> local meter
```

**Electrical notes:**

- spans aligned with AO where practical (including up to ~25 V);
- current measure includes 0–20 mA class;
- input impedance / burden TBD;
- no resistance-measure path.

### 5.3 RO Channel Block

```text
 [Range] + [Coarse] + [Fine] --> switched resistor network / rheostat path <--> RO terminals
                                         |
                                   local ohm indication
```

**Working range set (candidate):**

| Range | Span |
|-------|------|
| R1 | 0–100 Ω |
| R2 | 0–1 kΩ |
| R3 | 0–10 kΩ |

Optional fourth range later if panel space allows.

**Electrical notes:**

- ohmic simulation only;
- excitation current/power withstand TBD (critical);
- prefer dry passive or precision analog network over MCU-controlled digipot unless Phase 2 needs it.

### 5.4 DI Channel Block

```text
 DI in --> protection/clamp --> divider/scaling --> comparator vs Vth --> LED driver
                                              ^
                                              |
                               selector: 5 / 10 / 15 / 20 V
```

**Electrical notes:**

- DC threshold detection only as functional mode;
- hysteresis TBD;
- max safe input > 20 V (working assumption ≥ 24–30 V);
- **AC sensing not supported**; AC survivability policy still open (recommend DC-only label + basic clamp survival).

### 5.5 DO Channel Block

```text
 Manual switch --> level/drive stage --> protection --> DO out
                         |
                      status LED
```

**Working topology options:**

1. open-drain/open-collector (industrial-friendly);
2. push-pull logic;
3. selectable.

**Provisional recommendation for V1:** open-drain with clear voltage/current rating TBD, because it pairs well with industrial pull-ups and isolated banks.

### 5.6 RLY Channel Block

```text
 Manual switch --> coil driver (+ flyback) --> relay coil
                                      |
                                 contact set ---- RLY A/B(/C)
                                      |
                                 state LED (coil or contact-sense)
```

**Working assumption:** SPST-NO, ≥ 30 VDC / 1–2 A class contacts.

### 5.7 AUX Block

```text
 VBUS --> buck/boost/LDO set --> protected outputs --> 5 / 12 / 24 VDC terminals
```

Each rail: regulator + current limit/fuse strategy + polarity labeling.

---

## 6. Protection Concept

| Family | Minimum protection intent |
|--------|---------------------------|
| Inlet | Fuse, reverse polarity (DC), surge/TVS as practical |
| AO | Current limit, short survivability, overvoltage clamp on sense nodes |
| AI | Input clamp, series impedance, overrange survivability |
| RO | Excitation current/power limit or crowbar/fuse concept |
| DI | Series R + clamp to safe window; isolated domain preferred |
| DO | Short protection / current limit; isolated domain preferred |
| RLY | Coil flyback; contact rating observance |
| AUX | Overcurrent foldback/hiccup/fuse; short survival |

Miswire goal: a fault on one channel should not destroy an unrelated family when within stated absolute maximum ratings.

---

## 7. Signal Integrity and Analog Concerns

| Topic | Guidance |
|-------|----------|
| Analog/digital partitioning | Keep AO/AI/RO analog sections away from relay coil bounce and brick rectifier noise |
| Returns | Star or carefully stitched AGND/DGND; document bond points |
| Meter loading | Local meters must not materially corrupt AO setpoint or AI reading |
| Relay EMI | Snubbers / separation from precision analog |
| Isolation barriers | Creepage/clearance reserved in layout even if rating is provisional |

---

## 8. Connector and Return Strategy

### 8.1 Working Connector Classes

| Port | Working preference | Notes |
|------|--------------------|-------|
| Brick inlet | Locked DC barrel **or** 2-pin terminal rated for VAC/VDC | Must be AC-safe if VAC supported |
| Signal I/O | 4 mm banana **or** pluggable terminal blocks | Consistency over mixed styles |
| AUX | Banana or terminal with clear polarity | Color/label mandatory |

Final series frozen in Hardware Design / UI.

### 8.2 Return Conventions (provisional)

- CORE families (AO/AI/RO) share a documented instrument return.
- DI/DO use isolated returns per bank (or per channel if later justified).
- RLY contacts have no forced bond to CORE.
- AUX return bonding default = CORE return unless floated by design.

---

## 9. Working Assumptions

These are defaults for schematic start; change only with documented rationale:

1. AO ranges: **0–5 / 0–10 / 0–15 / 0–25 V** + current modes.  
2. RO ranges: **0–100 Ω / 0–1 kΩ / 0–10 kΩ**.  
3. DI thresholds: **5 / 10 / 15 / 20 V** (frozen).  
4. DO: **open-drain** provisional.  
5. RLY: **SPST-NO**, ~30 VDC / 1–2 A.  
6. Aux currents: **2 A / 1 A / 0.5 A** for 5/12/24.  
7. DI/DO banks isolated from CORE.  
8. AO/AI/RO on CORE.  
9. Phase 1 uses no MCU for channel control.

---

## 10. Open Electrical Items

| ID | Item | Needed to freeze |
|----|------|------------------|
| EAD-OPEN-001 | Brick connector + tolerance + min VA | Safety label + BOM |
| EAD-OPEN-002 | Exact VSYS/VAN/VREF voltages | Regulator tree |
| EAD-OPEN-003 | AO accuracy/noise/compliance numbers | Verification + analog design |
| EAD-OPEN-004 | RO excitation withstand | RO topology |
| EAD-OPEN-005 | DI hysteresis + abs-max input | Comparator design |
| EAD-OPEN-006 | DI AC survivability claim | Clamp energy rating / labeling |
| EAD-OPEN-007 | DO voltage/current rating | Output stage |
| EAD-OPEN-008 | AUX bonding (common vs floating) | Ground map |
| EAD-OPEN-009 | Isolation withstand formal target | Creepage rules |

---

## 11. Traceability

| EAD topic | Anchors |
|-----------|---------|
| Power tree | SRS-REQ-PWR-*, SAD-BLK-PWR |
| Domains | SRS-REQ-ISO-*, SAD §7 |
| AO/AI/RO/DI/DO/RLY/AUX blocks | corresponding SRS family sections |
| Protection | SRS-REQ-PROT-* |
| Working assumptions | SAD-OPEN-* candidates |

---

## Document End

**Status:** Draft  
**Next:** [05_HardwareDesign.md](05_HardwareDesign.md)
