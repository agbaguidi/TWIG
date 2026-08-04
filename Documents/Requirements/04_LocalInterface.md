# 04 - Local Interface Specification

**Project:** TWIG - Test & Waveform Interface for GPIO  
**Document:** Local Interface Specification  
**Version:** 0.1.0  
**Status:** Draft  
**Last Updated:** 2026-08-03  



## Table of Contents

1. [Introduction](#1-introduction)
2. [Local Operation Overview](#2-local-operation-overview)
3. [Local Interface Principles](#3-local-interface-principles)
4. [Channel Interaction Model](#4-channel-interaction-model)
5. [Signal Configuration](#5-signal-configuration)
6. [Digital Signal Interaction](#6-digital-signal-interaction)
7. [Contact Output Interaction](#7-contact-output-interaction)
8. [Resistance Output Interaction](#8-resistance-output-interaction)
9. [System Information and Feedback](#9-system-information-and-feedback)
10. [Error and Warning Handling](#10-error-and-warning-handling)
11. [Typical Usage Scenarios](#11-typical-usage-scenarios)
12. [Interface Limitations](#12-interface-limitations)
13. [Summary](#13-summary)
14. [Version History](#version-history)


---

## 1. Introduction

### 1.1 Purpose

This document defines the local manual interaction model of the TWIG system.

The objective is to describe how an operator interacts with TWIG when using the integrated physical interface.

This document defines:

- User interaction principles
- Local configuration capabilities
- Information displayed to the operator
- Feedback mechanisms
- Typical operation workflows

This document does not define:

- Hardware implementation
- Display technology
- Electronic components
- Firmware architecture
- Remote communication interfaces

---

### 1.2 Scope

The scope of this document is limited to the **local manual operation** of TWIG.

The local interface includes:

- Direct interaction with the instrument
- Local configuration of signal functions
- Local monitoring of system states

Future interaction methods such as:

- PC software
- Remote control
- Network interfaces
- Automated test systems

are outside the scope of this document.

---

## 2. Local Operation Overview

### 2.1 General Principle

TWIG shall be designed as a laboratory instrument allowing an operator to configure and observe electrical signals directly.

The local interface shall allow the operator to:

- Select a signal channel
- Configure channel parameters
- Enable or disable signal functions
- Observe current system state
- Identify abnormal conditions

---

### 2.2 Operator Workflow

A typical operation sequence is:

```
+----------------+
| Select Channel |
+-------+--------+
        |
        v
+----------------+
| Configure      |
| Signal         |
+-------+--------+
        |
        v
+----------------+
| Enable Output  |
| or Measurement |
+-------+--------+
        |
        v
+----------------+
| Observe Result |
+----------------+
```

---

## 3. Local Interface Principles

### 3.1 Visibility

The operator shall always be able to identify the current state of TWIG.

The interface shall provide visibility of:

- Selected channel
- Channel type
- Current configuration
- Active/inactive state
- Measured or generated value
- Fault conditions

---

### 3.2 Direct Operation

The local interface shall prioritize direct operation over complex configuration workflows.

Common operations should require a minimal number of user actions.

The operator should be able to:

- Quickly select a channel
- Quickly modify a value
- Quickly identify the active configuration

---

### 3.3 Consistency

Similar functions shall use consistent interaction principles.

Examples:

- All analog outputs shall be configured using the same approach.
- All digital channels shall provide consistent state indication.
- Channel selection shall follow the same logic across signal families.

---

### 3.4 User Feedback

User actions shall generate immediate feedback.

Feedback shall allow the operator to confirm:

- The selected channel
- The modified parameter
- The current value
- The resulting system state

---

## 4. Channel Interaction Model

### 4.1 General Concept

Each TWIG channel shall be independently configurable.

The operator shall interact with channels through a common configuration model.

The general model is:

```
Channel Selection

        |

Channel Configuration

        |

Channel Activation

        |

Signal Monitoring
```

---

### 4.2 Channel Identification

The local interface shall provide a clear identification of the selected channel.

The operator shall be able to determine:

- Channel number
- Signal type
- Current state

Example:

```
AO Channel 2

Voltage Output
0-10V Range
Output: 5.00V
Enabled
```

---

### 4.3 Channel State

Each channel shall have a defined operational state.

Possible states include:

| State | Description |
|-|-|
| Disabled | Channel inactive |
| Configured | Parameters defined but not active |
| Active | Channel operating normally |
| Fault | Channel detected an abnormal condition |

---

## 5. Signal Configuration

### 5.1 General Configuration Principle

The local interface shall expose only parameters required for normal operation.

Advanced parameters or calibration settings should not interfere with normal usage.

---

### 5.2 Analog Output Configuration

The operator shall be able to configure analog outputs.

Configurable elements include:

- Output mode
- Output range
- Desired output value
- Channel activation state

Example:

```
AO Channel 1

Mode:
Voltage

Range:
0-10V

Value:
7.50V

State:
Enabled
```

---

### 5.3 Analog Input Configuration

The operator shall be able to configure analog input measurement parameters.

Possible parameters include:

- Measurement type
- Measurement range
- Channel activation state

The interface shall provide measured values.

Example:

```
AI Channel 1

Mode:
Voltage

Range:
0-10V

Measured:
4.82V
```

---

## 6. Digital Signal Interaction

### 6.1 Digital Output Configuration

The local interface shall allow the operator to configure digital outputs.

Configurable elements include:

- Output state
- Channel activation state

Example:

```
DO Channel 1

State:
ON

Status:
Active
```

---

### 6.2 Digital Input Monitoring

The local interface shall allow the operator to monitor digital inputs.

Displayed information shall include:

- Current input state
- Channel identification
- Channel status

Example:

```
DI Channel 2

State:
HIGH

Status:
Active
```

---

## 7. Contact Output Interaction

### 7.1 General Principle

Contact outputs shall be configurable through the local interface.

The operator shall be able to:

- Select the contact output channel
- Control the contact state
- Observe the current state

Example:

```
CO Channel 1

State:
Closed

Status:
Active
```

---

## 8. Resistance Output Interaction

### 8.1 General Principle

The local interface shall allow the operator to configure resistance outputs.

The operator shall be able to define:

- Desired resistance value
- Channel activation state

Example:

```
RO Channel 1

Resistance:
1000 Ohm

State:
Enabled
```

---

## 9. System Information and Feedback

### 9.1 General Principle

The local interface shall provide sufficient feedback to allow the operator to understand the current system condition.

Feedback information includes:

- Active channels
- Current configurations
- Measured values
- Warning conditions
- Fault conditions

---

### 9.2 Visual Feedback

TWIG shall provide visual feedback allowing the operator to quickly identify system states.

Visual feedback may include:

- Display information
- Indicators
- Status signals

The implementation method is not defined by this document.

---

### 9.3 Active Channel Identification

The operator shall be able to determine which channels are currently active.

The interface shall avoid ambiguity when multiple channels are configured simultaneously.

---

## 10. Error and Warning Handling

### 10.1 General Principle

The local interface shall inform the operator when abnormal conditions occur.

The objective is to allow the operator to:

- Identify the affected channel
- Understand the type of issue
- Take corrective action

---

### 10.2 Error Information

When an error occurs, the interface should provide:

- Error identification
- Affected channel
- Current state

Example:

```
AO Channel 2

Status:
FAULT

Cause:
Output limitation
```

---

### 10.3 Warning Information

Warnings indicate conditions requiring operator attention but not preventing operation.

Examples:

- Output limitation reached
- Configuration conflict
- Measurement outside expected range

---

## 11. Typical Usage Scenarios

### 11.1 Generate an Analog Voltage

Scenario:

The operator wants to generate a voltage signal for a device under test.

Sequence:

1. Select an analog output channel.
2. Select voltage mode.
3. Select required range.
4. Configure desired value.
5. Enable output.
6. Observe channel status.

Expected result:

The operator can confirm that the requested output is active.

---

### 11.2 Measure an Analog Signal

Scenario:

The operator wants to verify a signal generated by an external device.

Sequence:

1. Select an analog input channel.
2. Configure measurement parameters.
3. Activate measurement.
4. Observe measured value.

Expected result:

The operator can identify the measured signal.

---

### 11.3 Configure Multiple Channels

Scenario:

The operator needs several signals simultaneously.

Sequence:

1. Configure each required channel independently.
2. Enable required channels.
3. Monitor active channels.

Expected result:

The operator can understand the complete TWIG state.

---

## 12. Interface Limitations

### 12.1 Scope Limitation

The local interface is intended for direct laboratory operation.

It is not intended to replace:

- Automated test software
- Large-scale configuration tools
- Remote monitoring systems

---

### 12.2 Future Interfaces

Additional interfaces may be developed without modifying the principles defined in this document.

Examples:

- PC software
- Communication protocols
- Automated test integration

Such interfaces shall be documented separately.

---

## 13. Summary

The TWIG local interface shall provide a simple and direct method for an operator to:

- Configure signal channels
- Control generated outputs
- Monitor measured signals
- Understand system state
- Identify abnormal conditions

The interface shall prioritize:

- Visibility
- Simplicity
- Consistency
- Efficient laboratory operation

The implementation of the interface remains independent from this specification.

---

## Version History

| Version | Date | Author | Changes |
|-|-|-|-|
| 0.1.0 | 2026-08-03 | Arnaud Gbaguidi | Baseline local interface specification; document format aligned with SRS |
