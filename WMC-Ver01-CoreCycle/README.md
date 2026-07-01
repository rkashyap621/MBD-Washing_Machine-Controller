# Washing Machine Controller, Ver. 01: Core Cycle Control

## Project Overview

This project implements a **core washing-machine cycle controller** using **Stateflow** in MATLAB/Simulink.

Ver. 01 focuses on the nominal washing-machine sequence: start, door-lock activation, wash water fill, detergent fill, wash, drain, rinse water fill, rinse, drain, spin, and cycle completion.

The main objective of this version is to build a clean Stateflow-based controller and verify it using requirement authoring, requirement linking, traceability, and simulation evidence.

This version does not include pause/resume handling, stage-status display, mutual-exclusion diagnostics, or detailed fault handling. Those features are planned and implemented in later versions.

---

## Version Scope

Ver. 01 covers:

- Core washing-machine sequence control
- Door safety before start
- Door-lock activation before the active cycle
- Water valve control during wash and rinse filling
- Detergent valve control during detergent fill
- Wash motor control during wash and rinse stages
- Drain pump control during wash-drain and rinse-drain stages
- Spin motor control during spin stage
- Cycle-complete indication after successful completion
- Requirement authoring and implementation linking
- Requirements consistency checking
- Traceability matrix generation
- Nominal simulation evidence

---

## Tools Used

- MATLAB R2026a
- Simulink
- Stateflow
- Requirements Toolbox

---

## Repository Structure

```text
WMC_Ver01_CoreCycle/
├── images/
│   ├── WMC_Ver01_Chart.png
│   ├── WMC_Ver01_Chart_with_Links.png
│   ├── WMC_Ver01_CoreCycle_Requirements_Consistency_Check.png
│   ├── WMC_Ver01_CoreCycle_Requirements_Consistency_Check_Report.png
│   ├── WMC_Ver01_CoreCycle_Requirements_Links_Part1.png
│   ├── WMC_Ver01_CoreCycle_Requirements_Links_Part2.png
│   ├── WMC_Ver01_CoreCycle_Requirements_Traceability_Matrix.png
│   ├── WMC_Ver01_Cycle_Complete.png
│   ├── WMC_Ver01_Cycle_Complete_to_Idle.png
│   └── WMC_Ver01_Idle_Start.png
│   └── WMC_Ver01_Rinse_Cycle.png
│   └── WMC_Ver01_Scope_Result.png
│   └── WMC_Ver01_Spin_Cycle.png
│   └── WMC_Ver01_Symbols_Pane.png
│   └── WMC_Ver01_Top_Level_Model.png
│   └── WMC_Ver01_Wash_Cycle.png
├── model/
│   └── WMC_Ver01_CoreCycle.slx
├── requirements/
│   ├── WMC_Ver01_CoreCycle_Requirements.xlsx
│   ├── WMC_Ver01_CoreCycle_Requirements.pdf
│   ├── WMC_Ver01_CoreCycle_Requirements.slreqx
│   └── WMC_Ver_01_CoreCycle~mdl.slmx
├── results/
│   ├── WMC_Ver01_CoreCycle_Requirements.pdf
│   └── WMC_Ver01_CoreCycle_Requirements_Consistency_Check.html
│   └── WMC_Ver01_CoreCycle_Traceability_Matrix.html
│   └── WMC_Ver01_Scope_Result.png
└── README.md
```

---

## Controller Inputs

| Signal | Description |
|---|---|
| `start_button` | Starts the washing cycle when the door is closed |
| `pause_button` | Reserved for later versions |
| `resume_button` | Reserved for later versions |
| `reset_button` | Reserved for later versions |
| `door_closed` | Indicates whether the washing-machine door is closed |
| `water_level_ok` | Indicates that the required water level has been reached |
| `detergent_level_ok` | Indicates that detergent filling is complete |
| `fault_signal` | Reserved for later versions |

Although pause, resume, reset, and fault input signals are available at the top level, their full control behavior is not part of Ver. 01. They are included to support progressive development in later versions.

---

## Controller Outputs

| Signal | Description |
|---|---|
| `door_lock` | Locks the door during active cycle operation |
| `water_valve` | Controls water filling during wash and rinse fill stages |
| `detergent_valve` | Controls detergent filling |
| `drain_pump` | Controls water draining after wash and rinse stages |
| `wash_motor` | Runs during wash and rinse stages |
| `spin_motor` | Runs during spin stage |
| `cycle_complete` | Indicates completion of the full washing cycle |
| `fault_indicator` | Reserved for later versions |

---

## Stateflow Design

The controller is implemented as a sequential Stateflow chart. The chart begins in the `Idle` state and transitions through the washing-machine stages when the required input conditions are satisfied.

![Stateflow Chart](images/WMC_Ver01_Chart.png)

### Main States

| State | Purpose |
|---|---|
| `Idle` | Default safe state. All actuators are OFF. |
| `Door_Lock_Activation` | Locks the door before the active washing cycle begins. |
| `Water_Fill_Wash_Cycle` | Turns ON the water valve until the required water level is reached. |
| `Detergent_Fill` | Turns ON the detergent valve until detergent filling is complete. |
| `Wash` | Runs the wash motor for the configured wash duration. |
| `Drain_Wash_Cycle` | Turns ON the drain pump after the wash stage. |
| `Water_Fill_Rinse_Cycle` | Turns ON the water valve for the rinse-water filling stage. |
| `Rinse` | Runs the wash motor for the configured rinse duration. |
| `Drain_Rinse_Cycle` | Turns ON the drain pump after the rinse stage. |
| `Spin` | Runs the spin motor for the configured spin duration. |
| `Cycle_Complete` | Turns OFF all actuators, unlocks the door, and sets `cycle_complete` to 1. |

---

## Top-Level Simulink Model

The top-level model contains dashboard-style input controls, the Stateflow controller block, and actuator output visualization.

![Top-Level Model](images/WMC_Ver01_Top_Level_Model.png)

The top-level design keeps the controller interface simple and readable. Input signals are provided on the left, the Stateflow controller is placed in the center, and output signals are displayed on the right.

---

## Parameters Used

| Parameter | Purpose |
|---|---|
| `door_lock_delay` | Delay before entering the wash water-fill stage after door locking |
| `wash_duration` | Time duration for the wash stage |
| `drain_duration` | Time duration for wash-drain and rinse-drain stages |
| `rinse_duration` | Time duration for the rinse stage |
| `spin_duration` | Time duration for the spin stage |

The symbols pane confirms the configured inputs, outputs, and parameters used in the controller.

![Symbols Pane](images/WMC_Ver01_Symbols_Pane.png)

---

## Requirements

The Ver. 01 requirement set defines the nominal behavior of the core washing-machine cycle.

| Requirement ID | Requirement Name | Summary | Status |
|---|---|---|---|
| WMC-REQ-001 | System Initialization | Controller shall enter Idle with all actuators OFF and cycle indicators reset. | ✅ Pass |
| WMC-REQ-002 | Start Condition | Controller shall start the washing cycle when `start_button` is 1 and `door_closed` is 1. | ✅ Pass |
| WMC-REQ-003 | Door Safety Before Start | Controller shall remain in Idle if the start command is given while the door is open. | ✅ Pass |
| WMC-REQ-004 | Door Lock Activation | Controller shall activate `door_lock` and wait for the configured delay before water filling. | ✅ Pass |
| WMC-REQ-005 | Wash-Cycle Water Fill | Controller shall turn ON `water_valve` during wash water filling until `water_level_ok` is 1. | ✅ Pass |
| WMC-REQ-006 | Detergent Fill Stage | Controller shall turn ON `detergent_valve` until `detergent_level_ok` is 1. | ✅ Pass |
| WMC-REQ-007 | Wash Stage | Controller shall turn ON `wash_motor` for the configured wash duration. | ✅ Pass |
| WMC-REQ-008 | Wash-Cycle Drain | Controller shall turn ON `drain_pump` for the configured drain duration. | ✅ Pass |
| WMC-REQ-009 | Rinse-Cycle Water Fill | Controller shall turn ON `water_valve` during rinse water filling until `water_level_ok` is 1. | ✅ Pass |
| WMC-REQ-010 | Rinse Stage | Controller shall turn ON `wash_motor` for the configured rinse duration. | ✅ Pass |
| WMC-REQ-011 | Rinse-Cycle Drain | Controller shall turn ON `drain_pump` for the configured drain duration. | ✅ Pass |
| WMC-REQ-012 | Spin Stage | Controller shall turn ON `spin_motor` for the configured spin duration. | ✅ Pass |
| WMC-REQ-013 | Cycle Completion | Controller shall turn OFF all actuators, unlock the door, and set `cycle_complete` to 1. | ✅ Pass |

---

## Requirements Linking

Each requirement was linked to its corresponding Stateflow implementation element. The linked elements include states, transition conditions, and timing-based transitions.

![Requirements Links Part 1](images/WMC_Ver01_CoreCycle_Requirements_Links_Part1.png)

![Requirements Links Part 2](images/WMC_Ver01_CoreCycle_Requirements_Links_Part2.png)

The Stateflow chart with visible requirement-link indicators is shown below.

![Stateflow Chart with Requirement Links](images/WMC_Ver01_Chart_with_Links.png)

---

## Traceability Matrix

A traceability matrix was generated to verify that the authored requirements are mapped to the implemented Stateflow elements.

![Traceability Matrix](images/WMC_Ver01_CoreCycle_Requirements_Traceability_Matrix.png)

The traceability matrix confirms that the Ver. 01 requirements are linked to implementation elements in the model.

---

## Requirements Consistency Check

The Requirements Toolbox consistency check was executed after linking the requirement set to the model.

![Requirements Consistency Check](images/WMC_Ver01_CoreCycle_Requirements_Consistency_Check.png)

The detailed consistency check report shows that all checks passed.

![Requirements Consistency Check Report](images/WMC_Ver01_CoreCycle_Requirements_Consistency_Check_Report.png)

---

## Simulation Verification

The nominal cycle was simulated and verified using scope outputs. The simulation confirms that the controller transitions through the expected actuator sequence.

![Scope Result](images/WMC_Ver01_Scope_Result.png)

### Observed Output Sequence

- `door_lock` turns ON after the valid start condition and remains ON during the active cycle.
- `water_valve` turns ON during wash water filling and rinse water filling.
- `detergent_valve` turns ON during detergent filling.
- `wash_motor` turns ON during wash and rinse stages.
- `drain_pump` turns ON during wash-drain and rinse-drain stages.
- `spin_motor` turns ON during the spin stage.
- `cycle_complete` turns ON after the spin stage is completed.
- `fault_indicator` remains OFF throughout the nominal cycle.

---

## Key State Evidence

### Idle State

The controller starts from the Idle state with all actuators OFF.

![Idle State](images/WMC_Ver01_Idle_Start.png)

### Wash State

The wash motor is activated during the Wash stage.

![Wash State](images/WMC_Ver01_Wash_Cycle.png)

### Rinse State

The wash motor is also used during the Rinse stage.

![Rinse State](images/WMC_Ver01_Rinse_Cycle.png)

### Spin State

The spin motor is activated during the Spin stage.

![Spin State](images/WMC_Ver01_Spin_Cycle.png)

### Cycle Complete State

At the end of the cycle, all actuators are turned OFF, the door is unlocked, and `cycle_complete` is set to 1.

![Cycle Complete State](images/WMC_Ver01_Cycle_Complete.png)

The controller then returns to Idle after the configured post-completion delay.

![Cycle Complete to Idle](images/WMC_Ver01_Cycle_Complete_to_Idle.png)

---

## Verification Summary

| Verification Item | Status |
|---|---|
| Stateflow core cycle sequence implemented | Passed |
| Door safety before start implemented | Passed |
| Door lock activation implemented | Passed |
| Wash water-fill stage implemented | Passed |
| Detergent-fill stage implemented | Passed |
| Wash stage implemented | Passed |
| Wash-drain stage implemented | Passed |
| Rinse water-fill stage implemented | Passed |
| Rinse stage implemented | Passed |
| Rinse-drain stage implemented | Passed |
| Spin stage implemented | Passed |
| Cycle-complete behavior implemented | Passed |
| Requirement links created | Passed |
| Requirements consistency check | Passed |
| Traceability matrix generated | Passed |
| Nominal simulation completed | Passed |

---

## Learning Outcomes

Through this version, the following concepts were practiced:

- Sequential control logic using Stateflow
- State-based actuator control
- Conditional transitions using sensor inputs
- Time-based transitions using `after()` logic
- Requirement authoring using Requirements Toolbox
- Linking requirements to Stateflow implementation elements
- Requirements consistency checking
- Traceability matrix generation
- Simulation-based evidence collection
- Version-wise controller development for portfolio documentation

---

## Limitations of Ver. 01

Ver. 01 is intentionally limited to the nominal core cycle. It does not include:

- Pause and resume behavior
- History junction-based resume logic
- Stage-status diagnostic output
- Mutual-exclusion violation monitoring
- Fault detection and reset behavior
- Detergent-fill timeout fault handling

These features are implemented in later versions of the project.

---

## Planned Version Progression

| Version | Focus |
|---|---|
| Ver. 01 | Core washing-machine cycle sequence with requirements and traceability |
| Ver. 02 | Pause/resume behavior and basic fault handling |
| Ver. 03 | Stage-status display, mutual-exclusion diagnostics, refined verification evidence |

---

## Conclusion

WMC Ver. 01 establishes the foundation of the washing-machine controller. It demonstrates a requirement-based Stateflow workflow, where system behavior is authored as requirements, implemented as Stateflow logic, linked to model elements, checked for consistency, traced using a matrix, and validated through simulation evidence.

This version forms the baseline for later enhancements such as pause/resume control, fault handling, stage-status monitoring, and diagnostic verification.

---

## License

MIT License

---
