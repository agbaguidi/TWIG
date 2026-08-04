# 03 - System Architecture

**Project:** TWIG - Test & Waveform Interface for GPIO  
**Document:** System Architecture Specification  
**Version:** 0.1.0  
**Status:** Draft  
**Last Updated:** 2026-08-03  



## Table of Contents

1. [Introduction](#1-introduction)
2. [System Overview](#2-system-overview)
3. [Functional Architecture](#3-functional-architecture)
4. [Control Domain Architecture](#4-control-domain-architecture)
5. [Control and Signal Interface](#5-control-and-signal-interface)
6. [Signal Domain Architecture](#6-signal-domain-architecture)
7. [Channel Architecture](#7-channel-architecture)
8. [Input and Output Separation](#8-input-and-output-separation)
9. [Protection Architecture](#9-protection-architecture)
10. [Power Architecture](#10-power-architecture)
11. [PCB Architecture Concept](#11-pcb-architecture-concept)
12. [User Interaction Architecture](#12-user-interaction-architecture)
13. [Architectural Design Constraints](#13-architectural-design-constraints)
14. [Hardware Expansion Strategy](#14-hardware-expansion-strategy)
15. [Design Trade-Off Principles](#15-design-trade-off-principles)
16. [System Architecture Summary](#16-system-architecture-summary)
17. [Architecture Traceability](#17-architecture-traceability)
18. [Open Architecture Decisions](#18-open-architecture-decisions)
19. [Document Status](#19-document-status)
20. [Version History](#version-history)


---

## 1. Introduction

### 1.1 Purpose

This document describes the high-level architecture of the TWIG system.

The objective of this document is to define how the system is structured to satisfy the requirements defined in the System Requirements Specification (SRS).

This document defines:

- Functional blocks
- System boundaries
- Interfaces between subsystems
- Design principles
- Architectural constraints

Implementation details such as component selection and detailed electrical circuits are described in subsequent documents.

---

### 1.2 Relationship with Other Documents

The system architecture is derived from the requirements defined in:

```
02_SystemRequirements.md
```

The architecture defined here provides the foundation for:

```
04_ElectricalArchitecture.md
05_HardwareDesign.md
06_UserInterface.md
07_VerificationPlan.md
```

---

## 2. System Overview

### 2.1 General Description

TWIG is a standalone laboratory instrument designed to generate, measure, and simulate electrical signals commonly used in embedded systems and industrial automation.

The system is composed of several functional domains:

- User Interface Domain
- Control Domain
- Signal Domain
- Power Domain
- Protection Domain

Each domain has a specific responsibility and shall remain conceptually independent.

---

### 2.2 High-Level Architecture

TWIG is composed of three major functional domains:

- Control and User Interface Domain
- Signal Domain
- Power Domain

The Signal Domain contains the electrical functions connected to the DUT. The Control Domain defines the desired behavior of these functions.


```
+------------------------------------------------+
|                    TWIG                        |
|                                                |
|  +------------------------------------------+  |
|  |              Control Domain              |  |
|  |                                          |  |
|  |  User Interface                          |  |
|  |  System Management                       |  |
|  |  Configuration Handling                  |  |
|  +--------------------+---------------------+  |
|                       |                        |
|                       | Control Signals        |
|                       |                        |
|  +--------------------v---------------------+  |
|  |              Signal Domain               |  |
|  |                                          |  |
|  |  Analog Output (AO)                      |  |
|  |  Analog Input (AI)                       |  |
|  |  Digital Output (DO)                     |  |
|  |  Digital Input (DI)                      |  |
|  |  Contact Output (CO)                     |  |
|  |  Resistance Output (RO)                  |  |
|  +------------------------------------------+  |
|                                                |
|  +------------------------------------------+  |
|  |              Power Domain                |  |
|  +------------------------------------------+  |
|                                                |
+------------------------------------------------+
```

---

## 3. Functional Architecture

### 3.1 Functional Domains

The system is divided into the following functional blocks.

| Domain | Purpose |
|-|-|
| User Interface | Allows user interaction and provides feedback |
| Control | Manages configuration and operation of signal functions |
| Analog Output | Generates controlled analog voltage and current signals |
| Analog Input | Measures external analog signals |
| Digital Output | Generates digital voltage signals |
| Digital Input | Detects external digital signals |
| Contact Output | Simulates open/closed electrical contacts |
| Resistance Output | Simulates configurable resistance values |
| Power | Provides required internal energy sources |
| Protection | Protects TWIG and connected equipment |

---

### 3.2 Domain Responsibility

Each domain has a distinct responsibility:

| Domain | Responsibility |
|-|-|
| User Interface Domain | Allows user interaction and provides feedback |
| Control Domain | Manages configuration, commands and system states |
| Signal Domain | Generates, measures or simulates electrical signals |
| Power Domain | Provides energy required by all subsystems |

The Control Domain does not define the electrical implementation of signals. It only defines the requested operating state.

---

## 4. Control Domain Architecture

### 4.1 Purpose

The Control Domain manages the logical operation of TWIG.

Its responsibilities include:

- Managing user configurations
- Maintaining system states
- Sending commands to signal functions
- Processing measurements returned by signal functions
- Managing feedback information

The Control Domain defines the required behavior of signal functions but does not implement the electrical signal generation or measurement.

---

### 4.2 Control and Signal Separation

The architecture separates:

- Decision making
- Signal generation and conditioning

The Control Domain defines:

> "What signal is required"

The Signal Domain implements:

> "How the electrical signal is produced or measured"

This separation allows future evolution of the hardware without changing the system behavior.

---

### 4.3 Control Domain Block Diagram

```
+-----------------------+
|   User Interface      |
+-----------+-----------+
            |
            |
+-----------v-----------+
| System Controller     |
+-----------+-----------+
            |
            |
+-----------v-----------+
| Signal Management     |
+-----------------------+
```

---

## 5. Control and Signal Interface

### 5.1 Purpose

The interface between the Control Domain and the Signal Domain defines the exchange of information required to operate TWIG.

The Control Domain provides:

- Function selection
- Configuration parameters
- Requested output values

The Signal Domain provides:

- Measured values
- Status information
- Fault conditions

Example:

Analog Output configuration:

Control Domain:

- Mode: Voltage
- Range: 0-10 V
- Setpoint: 5 V

Signal Domain:

- Generates the required electrical output
- Applies required conditioning and protection

---

## 6. Signal Domain Architecture

### 6.1 Purpose

The Signal Domain contains all electrical functions directly connected to the Device Under Test (DUT).

The Signal Domain is responsible for:

- Signal generation
- Signal measurement
- Electrical conditioning
- Protection

---

### 6.2 Signal Families

TWIG supports six independent signal families:

| Interface | Function |
|-|-|
| AO | Generate analog voltage and current signals |
| AI | Measure analog voltage and current signals |
| DO | Generate digital voltage levels |
| DI | Detect digital voltage levels |
| CO | Simulate open and closed contacts |
| RO | Generate configurable resistance values |

---

### 6.3 Interface Independence

Each signal family shall be considered an independent functional subsystem.

The architecture shall minimize unwanted interaction between:

- Analog and digital domains
- Input and output domains
- Different user channels

This improves:

- Safety
- Reliability
- Debugging capability
- User understanding

---

## 7. Channel Architecture

### 7.1 Channel Concept

A TWIG channel is considered as an independent functional unit capable of performing a specific signal operation.

Each channel shall contain all required functions to:

- Receive configuration commands
- Generate or acquire electrical signals
- Apply required conditioning
- Protect the system and connected equipment
- Provide status information

A channel shall be designed as a reusable building block whenever practical.

---

### 7.2 Generic Channel Architecture

The general channel architecture is:

```
+----------------------+
| Channel Configuration|
+----------+-----------+
           |
           |
+----------v-----------+
| Signal Function      |
| Generation/Measure   |
+----------+-----------+
           |
           |
+----------v-----------+
| Conditioning         |
+----------+-----------+
           |
           |
+----------v-----------+
| Protection           |
+----------+-----------+
           |
           |
+----------v-----------+
| External Interface   |
+----------------------+
```

---

### 7.3 Channel Replication

Channels belonging to the same signal family shall use the same architectural concept.

Example:

```
Analog Output Domain

+-----------+
| AO CH1    |
+-----------+

+-----------+
| AO CH2    |
+-----------+

+-----------+
| AO CH3    |
+-----------+

+-----------+
| AO CH4    |
+-----------+
```

The number of channels shall be defined according to the requirements:

- 4 Analog Outputs
- 4 Analog Inputs
- 4 Digital Outputs
- 4 Digital Inputs
- 4 Contact Outputs
- 4 Resistance Outputs

---

## 8. Input and Output Separation

### 8.1 Principle

TWIG shall maintain a clear separation between signal generation and signal measurement functions.

The following interfaces shall remain independent:

| Output Interfaces | Input Interfaces |
|-|-|
| AO | AI |
| DO | DI |
| CO | - |
| RO | - |

---

### 8.2 Purpose

This separation provides:

- Improved electrical safety
- Reduced risk of incorrect connection
- Easier troubleshooting
- Clearer user operation
- Simplified verification

---

### 8.3 Device Under Test Interface

The DUT connects directly to the TWIG Signal Domain.

The general signal path is:

```
          TWIG

+-------------------+
| Signal Function   |
+---------+---------+
          |
+---------v---------+
| Protection Layer  |
+---------+---------+
          |
+---------v---------+
| External Connector|
+---------+---------+
          |
          |
          DUT
```

---

## 9. Protection Architecture

### 9.1 Purpose

Protection is considered a fundamental architectural layer rather than an optional addition.

The protection architecture shall reduce the risk caused by:

- Incorrect wiring
- Short circuits
- Reverse polarity
- External voltage faults
- Unexpected DUT behavior

---

### 9.2 Protection Layer Concept

Each external interface shall consider the following layers:

```
External Connection
        |
        v
+----------------+
| Protection     |
+----------------+
        |
        v
+----------------+
| Signal Function|
+----------------+
        |
        v
Internal TWIG
```

---

### 9.3 Protection Responsibility

Protection functions may include:

- Current limiting
- Voltage limiting
- Reverse connection protection
- Fault isolation
- Thermal protection

The exact implementation shall be defined in the Electrical Architecture document.

---

## 10. Power Architecture

### 10.1 Purpose

The Power Domain supplies all functional domains requiring electrical energy:

- User Interface
- Control Domain
- Signal Domain
- Protection circuits

---

### 10.2 Power Domain Concept

```
External Power Input
        |
        v
+------------------+
| Power Protection |
+------------------+
        |
        v
+------------------+
| Power Conversion |
+------------------+
        |
        +----------------+
        |                |
        v                v
 Control Domain   Signal Domain
```

---

### 10.3 Power Isolation Considerations

The architecture shall evaluate whether some signal domains require:

- Shared ground reference
- Local reference domains
- Galvanic isolation

The final decision shall be documented in the Electrical Architecture document.

---

## 11. PCB Architecture Concept

### 11.1 Functional PCB Separation

The TWIG architecture supports separation between:

- Control and User Interface electronics
- Signal processing electronics

A possible implementation consists of two stacked PCBs:

#### Interface PCB

Contains:

- User interface components
- Display
- Controls
- System controller
- Communication interfaces

#### Signal PCB

Contains:

- AO channels
- AI channels
- DO channels
- DI channels
- CO channels
- RO channels
- Signal protection circuits

This separation improves:

- Modularity
- Hardware reuse
- Debugging capability
- Future expansion

---

## 12. User Interaction Architecture

### 12.1 Purpose

The User Interface Domain provides interaction between the operator and the TWIG system.

It shall allow the user to:

- Select a function
- Configure values
- Observe measurements
- Identify active channels

---

### 12.2 User Interface Flow

```
User
 |
 v
+----------------+
| User Interface |
+----------------+
 |
 v
+----------------+
| Control Domain |
+----------------+
 |
 v
+----------------+
| Signal Domain  |
+----------------+
```

---

### 12.3 Feedback Principle

TWIG shall provide immediate feedback between user actions and system state.

Examples:

- Selected output value
- Measured input value
- Active channel state
- Error condition

The exact display technology is defined in the User Interface document.

---

## 13. Architectural Design Constraints

### 13.1 General Principles

The TWIG architecture shall follow the following design principles:

- Clear separation between functional domains
- Reusable channel-based design
- Protection as an integral system layer
- Maintainable and expandable architecture
- Simple debugging and validation process

---

### 13.2 Implementation Independence

The system architecture shall remain independent from specific component choices.

The following elements shall be defined at later design stages:

- Microcontroller selection
- Analog conversion technology
- Signal conditioning circuits
- Protection components
- Communication interfaces

The selected implementation shall satisfy the requirements defined in the SRS.

---

### 13.3 Modularity

TWIG shall be designed using modular functional blocks.

A modification or improvement to one subsystem should minimize impact on unrelated subsystems.

Examples:

- Improving an Analog Output channel should not require redesigning Digital Inputs.
- Changing the user interface should not change the electrical behavior of signal channels.

---

## 14. Hardware Expansion Strategy

### 14.1 Channel Expansion

The architecture shall support increasing the number of channels when practical.

Additional channels should be implemented by replicating existing channel architectures.

---

### 14.2 Feature Expansion

Future versions may introduce additional capabilities, including:

- Computer communication
- Automated test sequences
- Data acquisition
- Remote control
- Advanced calibration features

Future features shall extend the existing architecture rather than replace the fundamental design principles.

---

## 15. Design Trade-Off Principles

During detailed design, decisions shall consider the following priorities:

| Priority | Design Objective |
|-|-|
| 1 | Safety and protection |
| 2 | Functional compliance with SRS |
| 3 | Reliability and repeatability |
| 4 | Ease of use |
| 5 | Maintainability |
| 6 | Cost optimization |

---

## 16. System Architecture Summary

The TWIG system architecture can be summarized as:

```
                                                    USER
                                                     |
                                                     v
  +-----------------------+              +-----------------------+
  |    Power Domain       |------------->|   User Interface      |
  +-----------------------+      |       +-----------+-----------+
                                 |                   |
                                 |                   v
                                 |       +-----------------------+
                                 ------->|   Control Domain      |
                                         +-----------+-----------+
                                                     |
                                                     |
                         --------------------------------------------------------
                         |          |          |          |          |          |
                         v          v          v          v          v          v
                        AO         AI         DO         DI         RO         CO
                         |          |          |          |          |          |
                         --------------------------------------------------------
                                                     |
                                                     v
                                         +-----------------------+
                                         | Protection Layer      |
                                         +-----------+-----------+
                                                     |
                                                     v
                                                    DUT
```

---

## 17. Architecture Traceability

The relationship between architecture and other project documents is:

| Document | Purpose | Relationship |
|-|-|-|
| 01_ProductRequirements.md | Defines product expectations | Source of functional goals |
| 02_SystemRequirements.md | Defines measurable requirements | Primary architecture input |
| 03_SystemArchitecture.md | Defines system organization | Current document |
| 04_ElectricalArchitecture.md | Defines electrical implementation principles | Derived from this architecture |
| 05_HardwareDesign.md | Defines physical implementation | Derived from electrical architecture |
| 06_UserInterface.md | Defines user interaction details | Derived from UI architecture |
| 07_VerificationPlan.md | Defines validation methods | Derived from system requirements |

---

## 18. Open Architecture Decisions

The following topics require detailed design decisions:

| Topic | Document |
|-|-|
| Analog signal generation architecture | 04_ElectricalArchitecture.md |
| Analog measurement architecture | 04_ElectricalArchitecture.md |
| Resistance simulation implementation | 04_ElectricalArchitecture.md |
| Isolation strategy | 04_ElectricalArchitecture.md |
| Power supply architecture | 04_ElectricalArchitecture.md |
| Mechanical integration | 05_HardwareDesign.md |
| User interface technology | 06_UserInterface.md |

---

## 19. Document Status

This document defines the baseline system architecture for the Phase 1 TWIG design.

Future architecture modifications shall maintain consistency with:

- The System Requirements Specification
- The electrical design constraints
- The verification strategy

Major architectural changes shall be documented using Architecture Decision Records (ADR).

---

## Version History

| Version | Date | Author | Changes |
|-|-|-|-|
| 0.1.0 | 2026-08-03 | Arnaud Gbaguidi | Baseline system architecture; document format aligned with SRS |
