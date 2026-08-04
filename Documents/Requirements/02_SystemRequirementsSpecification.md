# 02 - System Requirements Specification (SRS)

**Project:** TWIG - Test & Waveform Interface for GPIO  
**Document:** System Requirements Specification  
**Version:** 0.2.0  
**Status:** Draft  
**Last Updated:** 2026-08-02  



## Table of Contents

1. [Purpose](#1-purpose)
2. [Scope](#2-scope)
3. [Requirement Identification](#3-requirement-dentification)
4. [Requirement Priority](#4-requirement-pPriority)
5. [Verification Method](#5-verification-method)
6. [Requirements](#6-requirements)
7. [Version History](#version-history)


---

## 1. Purpose

This document defines the system requirements for the TWIG project.

The objective of this document is to provide a single reference containing all functional, performance, safety, and operational requirements that the TWIG system shall satisfy.

These requirements define **what the system shall achieve**, while leaving implementation choices to the architecture and hardware design documents.

The System Requirements Specification shall be used as the reference for:

- System architecture definition
- Electrical design decisions
- Hardware implementation
- Verification and validation activities

---

## 2. Scope

TWIG (Test & Waveform Interface for GPIO) is a standalone laboratory instrument designed to generate, simulate, and validate electrical signals commonly used in embedded systems and industrial automation.

The Phase 1 system focuses on:

- Analog signal generation and measurement
- Digital signal simulation and detection
- Resistive sensor simulation
- Local user interaction
- Manual operation without mandatory computer connection

Future extensions such as automation, computer connectivity, and data logging are not part of the Phase 1 requirements unless explicitly specified.

---

## 3. Requirement Identification

Each requirement is assigned a unique identifier.

The identifier prefix indicates the requirement family.

| Prefix | Requirement Family |
|-|-|
| SYS | General system requirements |
| AO | Analog Output requirements |
| AI | Analog Input requirements |
| DO | Digital Output requirements |
| DI | Digital Input requirements |
| RO | Resistance Output requirements |
| CO | Contact Output |
| PWR | Power requirements |
| SAFE | Safety requirements |
| UI | User Interface requirements |
| CAL | Calibration requirements |
| ENV | Environmental requirements |


---

## 4. Requirement Priority

The following priority levels are used.

| Priority | Meaning |
|-|-|
| Must | Required for Phase 1 functionality |
| Should | Strongly recommended but may be adjusted |
| Could | Optional feature |
| TBD | Requirement not yet defined |

---

## 5. Verification Method

Each requirement shall be verified using one or more methods.

| Method | Description |
|-|-|
| Inspection | Review of design, documentation, or implementation |
| Measurement | Verification using test equipment |
| Functional Test | Operational verification |
| Safety Test | Verification under abnormal conditions |
| Review | Engineering analysis |

---

## 6. Requirements

The following table contains all TWIG system requirements.

Each requirement has a unique identifier. The identifier prefix indicates the related subsystem.

| ID | Requirement | Specification | Priority | Verification | Status |
|-|-|-|-|-|-|
| SYS-001 | TWIG shall operate as a standalone instrument | Basic operation shall not require a computer | Must | Functional Test | Draft |
| SYS-002 | TWIG shall provide local user interaction | Configuration and monitoring shall be possible from the instrument itself | Must | Functional Test | Draft |
| SYS-003 | TWIG shall support independent I/O channels | Failure or incorrect use of one channel should not affect other channels | Must | Functional Test | Draft |
| SYS-004 | Channels from the same family shall behave consistently | Same electrical behavior and user interaction | Must | Inspection | Draft |
| SYS-005 | TWIG shall provide separate signal families | Analog, digital, and resistance interfaces shall be independently defined | Must | Inspection | Draft |
| SYS-006 | TWIG shall provide sufficient channels for practical testing | Minimum 4 channels per I/O family | Should | Inspection | Draft |
| SYS-007 | TWIG shall provide immediate user feedback | Current state or value shall be visible without external tools | Must | Functional Test | Draft |
| AO-001 | TWIG shall generate analog voltage outputs | Output range: 0 to 25 VDC | Must | Measurement | Draft |
| AO-002 | TWIG shall generate analog current outputs | Output range: 0 to 20 mA | Must | Measurement | Draft |
| AO-003 | Analog voltage outputs shall support common industrial ranges | Include 0-5 V and 0-10 V operation | Should | Functional Test | Draft |
| AO-004 | Analog voltage adjustment shall provide sufficient resolution | ≤10 mV adjustment step | Should | Measurement | Draft |
| AO-005 | Analog current adjustment shall provide sufficient resolution | ≤0.01 mA adjustment step | Should | Measurement | Draft |
| AO-006 | Analog outputs shall maintain required accuracy | ±1 % Full Scale | Should | Calibration Test | Draft |
| AO-007 | Analog outputs shall support continuous operation | Output shall remain stable during normal use | Must | Functional Test | Draft |
| AO-008 | Analog outputs shall be protected against short circuits | Accidental short circuit shall not permanently damage TWIG | Must | Safety Test | Draft |
| AO-009 | Analog outputs shall be protected against reverse voltage | External voltage connection shall not permanently damage TWIG | Must | Safety Test | Draft |
| AI-001 | TWIG shall measure external analog voltage signals | Measurement range: ±30 VDC | Must | Measurement | Draft |
| AI-002 | TWIG shall measure external analog current signals | Measurement range: 0 to 25 mA | Must | Measurement | Draft |
| AI-003 | Analog inputs shall provide sufficient voltage resolution | ≤10 mV measurement resolution | Should | Measurement | Draft |
| AI-004 | Analog inputs shall provide sufficient current resolution | ≤0.01 mA measurement resolution | Should | Measurement | Draft |
| AI-005 | Analog inputs shall maintain required measurement accuracy | ±0.5 % Full Scale | Should | Calibration Test | Draft |
| AI-006 | Analog inputs shall present high impedance to the DUT | Input impedance ≥1 MΩ | Should | Measurement | Draft |
| AI-007 | Analog input measurements shall provide user feedback | Measured value shall be displayed locally | Must | Functional Test | Draft |
| AI-008 | Analog inputs shall provide protection against excessive voltage | Protection level shall be defined during electrical architecture design | Must | Safety Test | Draft |
| AI-009 | Analog inputs shall provide reverse polarity protection | Reverse connection shall not permanently damage TWIG | Must | Safety Test | Draft |
| DO-001 | TWIG shall generate digital output signals | Supported voltage levels: 5 V, 12 V, and 24 V | Must | Functional Test | Draft |
| DO-002 | Digital outputs shall support industrial interface testing | Compatible with common embedded and industrial inputs | Should | Functional Test | Draft |
| DO-003 | Digital outputs shall support required load current | Maximum output current: 200 mA | Must | Load Test | Draft |
| DO-004 | Digital outputs shall provide state indication | Output state shall be visible to the user | Must | Inspection | Draft |
| DO-005 | Digital outputs shall be protected against short circuits | Accidental short circuit shall not permanently damage TWIG | Must | Safety Test | Draft |
| DO-006 | Digital outputs shall include thermal protection | Output stage shall prevent damage under overload conditions | Should | Safety Test | Draft |
| DO-007 | Digital outputs shall include reverse voltage protection | External voltage connection shall not permanently damage TWIG | Must | Safety Test | Draft |
| DI-001 | TWIG shall detect external digital signals | Supported input levels: 5 V, 12 V, and 24 V | Must | Functional Test | Draft |
| DI-002 | Digital inputs shall tolerate industrial signal levels | Maximum input voltage: 30 VDC | Must | Measurement | Draft |
| DI-003 | Digital inputs shall provide state indication | Detected logic state shall be visible to the user | Must | Inspection | Draft |
| DI-004 | Digital inputs shall include input protection | Protection against incorrect connection is required | Must | Safety Test | Draft |
| DI-005 | Digital inputs shall support reliable state detection | Detection thresholds shall be defined during electrical architecture design | Must | Review | Draft |
| RO-001 | TWIG shall simulate resistive sensors | Resistance output values shall be configurable | Must | Functional Test | Draft |
| RO-002 | Resistance outputs shall support manual adjustment | User shall be able to modify simulated resistance value | Must | Functional Test | Draft |
| RO-003 | Resistance outputs shall provide user feedback | Selected resistance value shall be displayed or identifiable | Should | Functional Test | Draft |
| RO-004 | Resistance outputs shall support common sensor families | PT100, PT1000, and NTC simulation capability | Should | Functional Test | Cancelled |
| RO-005 | Resistance output accuracy shall be defined | Target accuracy: ±1 % | Should | Measurement | Draft |
| RO-006 | Resistance output resolution shall be defined | Target resolution: 1 Ω | Should | Review | Draft |
| RO-007 | Resistance output operating range shall be defined | Target range: 1 Ω → 1 MΩ | Should | Review | Draft |
| RO-008 | Resistance outputs shall support typical sensor excitation conditions | Compatible with excitation currents up to 5 mA | Should | Measurement | Draft |
| CO-001 | TWIG shall simulate dry contact states | Open and closed contact states | Must | Functional Test | Draft |
| CO-002 | Contact outputs shall be electrically isolated when required | Isolation strategy defined during architecture design | Should | Design Review | Draft |
| CO-003 | Contact outputs shall support external voltage levels | Compatible with typical digital input circuits | Should | Functional Test | Draft |
| PWR-001 | TWIG shall provide all required internal power rails | Internal voltages shall be generated from the selected power source | Must | Measurement | Draft |
| PWR-002 | TWIG shall operate from a defined external power source | Power source specification shall be defined during hardware architecture design | Must | Inspection | Draft |
| PWR-003 | Power input shall include reverse polarity protection | Incorrect power connection shall not permanently damage TWIG | Must | Safety Test | Draft |
| PWR-004 | Power input shall include overcurrent protection | Fault condition shall limit damage risk | Must | Safety Test | Draft |
| PWR-005 | Power consumption shall be documented | Typical and maximum power consumption shall be specified | Should | Measurement | Draft |
| SAFE-001 | TWIG shall minimize the risk of damage caused by incorrect connections | External connection faults shall be considered in the design | Must | Safety Test | Draft |
| SAFE-002 | TWIG shall protect the connected Device Under Test | Output faults shall minimize risk to external equipment | Must | Safety Test | Draft |
| SAFE-003 | TWIG shall protect itself against external electrical faults | External faults shall not permanently damage the system whenever practical | Should | Safety Test | Draft |
| SAFE-004 | Isolation requirements shall be evaluated for each interface family | Galvanic isolation strategy shall be documented | Must | Design Review | Draft |
| SAFE-005 | User-accessible connections shall minimize accidental misuse | Connectors and interfaces shall be clearly identified | Must | Inspection | Draft |
| UI-001 | TWIG shall provide local control of basic functions | Signal selection and configuration shall not require external software | Must | Functional Test | Draft |
| UI-002 | TWIG shall display configured or measured values | User shall be able to identify the current operating value | Must | Functional Test | Draft |
| UI-003 | TWIG shall provide channel identification | Each channel shall have a unique and visible identifier | Must | Inspection | Draft |
| UI-004 | TWIG shall provide status indication for active channels | Channel state shall be visually identifiable | Must | Inspection | Draft |
| UI-005 | User interface design shall prioritize ease of operation | Common operations shall require minimal user interaction | Should | Usability Review | Draft |
| CAL-001 | TWIG shall support calibration of analog functions | Calibration procedure shall exist for analog generation and measurement functions | Must | Functional Test | Draft |
| CAL-002 | Calibration procedure shall be documented | Required steps and equipment shall be defined | Must | Documentation Review | Draft |
| CAL-003 | Calibration shall use known reference values | Calibration references shall have appropriate accuracy | Should | Measurement | Draft |
| CAL-004 | Calibration parameters shall remain stable during normal operation | Drift shall remain within specified performance limits | Should | Long Term Test | Draft |
| ENV-001 | TWIG shall operate in a laboratory environment | Intended use: indoor development and test environment | Must | Inspection | Draft |
| ENV-002 | TWIG shall operate within specified temperature limits | Operating temperature: 10 °C to 40 °C | Should | Environmental Test | Draft |
| ENV-003 | TWIG shall tolerate normal storage conditions | Storage temperature: -20 °C to 60 °C | Should | Environmental Test | Draft |
| ENV-004 | TWIG shall operate under normal laboratory humidity conditions | Relative humidity: 10 % to 90 % non-condensing | Should | Environmental Test | Draft |
| SYS-008 | TWIG design shall support future expansion | Additional communication or automation features should be possible | Should | Design Review | Draft |
| SYS-009 | TWIG documentation shall maintain traceability between requirements and tests | Each requirement shall be linked to a verification method | Must | Documentation Review | Draft |
| SYS-010 | TWIG design decisions shall be documented when they impact requirements | Major architectural decisions shall include justification | Should | Documentation Review | Draft |


---



## Version History


| Version | Date       | Author          | Changes                                                                             |
| ------- | ---------- | --------------- | ----------------------------------------------------------------------------------- |
| 0.2.0   | 2026-08-02 | Arnaud Gbaguidi | Reworked requirement file                                                           |
| 0.1.2   | 2026-07-29 | Arnaud Gbaguidi | DI threshold selection: 5 / 10 / 15 / 20 V (AO-like range approach)                 |
| 0.1.1   | 2026-07-25 | Arnaud Gbaguidi | Freeze AO max voltage ≈0–25 VDC; require 24 VDC / 24 VAC external brick inlet       |
| 0.1.0   | 2026-07-25 | Arnaud Gbaguidi | Initial SRS derived from PRD-001; electrical values marked TBD where not yet frozen |