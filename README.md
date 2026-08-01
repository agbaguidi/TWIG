# TWIG – Test & Waveform Interface for GPIO

TWIG is a bench-top laboratory instrument for generating, measuring, and
simulating analog, digital, and resistive signals used in embedded and
industrial electronics development.

## Repository Structure

```text
TWIG/
├── README.md
├── docs/
│   ├── 00_ProjectVision.md
│   ├── 01_PRD.md
│   ├── 02_SRS.md
│   ├── 03_SystemArchitecture.md
│   ├── 04_ElectricalArchitecture.md
│   ├── 05_HardwareDesign.md
│   ├── 06_UserInterface.md
│   └── 07_VerificationPlan.md
└── archive/
```

## Documentation Order

1. [Project Vision](docs/00_ProjectVision.md) — why the product exists  
2. [Product Requirements (PRD)](docs/01_PRD.md) — what we are building and why  
3. [System Requirements (SRS)](docs/02_SRS.md) — what the system shall do  
4. [System Architecture](docs/03_SystemArchitecture.md) — how the system is organized  
5. [Electrical Architecture](docs/04_ElectricalArchitecture.md) — rails, domains, electrical blocks  
6. [Hardware Design](docs/05_HardwareDesign.md) — schematic/PCB implementation framework  
7. [User Interface](docs/06_UserInterface.md) — Phase 1 front-panel design  
8. [Verification Plan](docs/07_VerificationPlan.md) — how compliance is demonstrated  

## Project Phases

- **Phase 1** — Hardware-first instrument with manual controls (no firmware dependency)
- **Phase 2** — Optional intelligent co-pilot (MCU, presets, communication, automation)
