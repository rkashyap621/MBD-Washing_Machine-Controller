# MBD Washing Machine Controller

This repository contains a version-wise Model-Based Design project for a Washing Machine Controller developed using MATLAB, Simulink, Stateflow, and Requirements Toolbox.

The project demonstrates how a controller can evolve from a basic sequential Stateflow model into a requirement-linked, fault-aware, and verification-friendly model with diagnostic visibility.

## Project Objective

The objective of this project is to practice requirement-based Model-Based Design using a realistic controller example. Each version adds a new layer of functionality, verification evidence, and documentation maturity.

## Project Versions

| Version | Focus | Folder |
|---|---|---|
| Ver. 01 | Core washing-machine cycle sequence | `WMC-Ver01-CoreCycle/` |
| Ver. 02 | Pause/resume, history junction, fault handling, and timeout fault | `WMC-Ver02-PauseFault/` |
| Ver. 03 | Final model with stage-status display, mutual-exclusion diagnostics, requirements consistency check, and traceability | `WMC-Ver03-Final-Model/` |

## Tools Used
- MATLAB R2026a
- Simulink
- Stateflow
- Requirements Toolbox

## Key Features

- Stateflow-based washing-machine control logic
- Core cycle sequence: Idle, door lock, wash fill, detergent fill, wash, drain, rinse, spin, and cycle complete
- Pause and resume using Stateflow history junction
- Door-safe resume logic
- Detergent-fill timeout fault handling
- Generic fault activation and reset logic
- Top-level stage-status display in Ver. 03
- Mutual-exclusion diagnostic check for wash and spin motors
- Requirements authoring and linking
- Requirements consistency checking
- Traceability matrix generation
- Simulation evidence organized using `images/` and `results/` folders

## Repository Structure

```text
MBD-Washing-Machine-Controller/
|-- WMC-Ver01-CoreCycle/
|-- WMC-Ver02-PauseFault/
|-- WMC-Ver03-Final-Model/
|-- LICENSE
`-- README.md

---

## License

MIT License

---
