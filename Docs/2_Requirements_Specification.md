# H1 – Requirements Specification

## 1. Introduction

### 1.1 Purpose

This document defines the **requirements for the H1 Main Robotics Processing & Communications Unit** of the humanoid robot system. H1 is the central node responsible for:

- Running high-compute perception, planning and control software.
- Coordinating H2 (sensing/perception), H3 (mobility/manipulation) and H4 (power/actuation).
- Linking the robot to external networks and cloud services.
- Implementing supervision, health monitoring and safety-related functions.

The requirements here describe **what H1 shall do**, independently of any specific hardware platform or SOM.

### 1.2 Scope

In scope:

- H1 **electronics** (compute module, carrier/board-set, power tree, interfaces).
- H1 **safety & supervision** at the hardware/platform level.

Out of scope:

- Detailed mechanical design.
- Application-level software algorithms (vision, control, AI models).
- Cloud back-end design.
- Actuator/sensor electronics (covered by H2, H3, H4).

### 1.3 Definitions & Abbreviations

- **H1** – Main Robotics Processing & Communications Unit (this element).
- **H2** – Sensor & Perception Management Unit.
- **H3** – Mobility & Manipulation Controller.
- **H4** – Power / Actuation Distribution or other controller.
- **Compute Module** – High-performance CPU/GPU/SOC module used as the main processing element on H1.
- **MCU** – Microcontroller (safety/supervisor).
- **RTM** – Requirements Traceability Matrix

---

## 2. Requirement ID Scheme

- **H1-SYS-xxx** – System-level functional requirements.  
- **H1-CPU-xxx** – Compute & memory platform requirements.  
- **H1-IF-xxx** – Interface & connectivity requirements.  
- **H1-PWR-xxx** – Power architecture requirements.  
- **H1-SAF-xxx** – Safety & supervision requirements.  
- **H1-ENV-xxx** – Environmental & reliability requirements.  
- **H1-VER-xxx** – Verification & process requirements.

All requirements use the keyword shall to indicate mandatory compliance.

---

## 3. System-Level Functional Requirements

**H1-SYS-001**  
H1 **shall act as the central orchestration node** of the humanoid robot, coordinating perception, planning and control functions across H2, H3 and H4.

**H1-SYS-002**  
H1 shall provide the hardware and low-level control mechanisms necessary to support a graceful shutdown and restart of its compute module when commanded by system
software or an external controller. At minimum, H1 shall:

- accept shutdown / restart requests via defined local and remote control inputs
- control power and reset to the compute module in a way that allows the operating system to complete its own shutdown sequence before power is removed.

**H1-SYS-003**
H1 shall provide deterministic communication pathways between all robot subsystems with defined QoS and timing guarantees.

---

## 4. Compute & Memory Requirements

**H1-CPU-001**  
H1 **shall provide a compute platform** with a peak AI compute capability of **at least 200 TOPS (INT8 or equivalent)** to support multi-camera perception and advanced control algorithms.

**H1-CPU-002**  
H1 **shall provide at least 32 GB of system memory** for concurrent perception, planning, mapping and logging workloads.

**H1-CPU-003**  
H1 **shall support hardware-accelerated image and video processing**, including parallel processing of multiple camera streams at ≥30 fps for primary perception cameras.

**H1-CPU-004**  
H1 **shall support at least one hardware-accelerated AI runtime** (e.g. TensorRT, ONNX Runtime, or equivalent) compatible with deep-learning workloads used in the robot.

---

## 5. Interface and Connectivity Requirements

**H1-IF-001**  
H1 **shall provide at least one high-bandwidth Ethernet interface** (≥1 Gbps) for internal communication with other robot units (e.g. H2/H3).

**H1-IF-002**  
H1 **shall provide at least one CAN or CAN-FD interface** for deterministic safety and control communication with H2/H3/H4.

**H1-IF-003**  
H1 **shall provide at least one high-speed sensor interface** (e.g. MIPI CSI, LVDS, or equivalent) suitable for one or more high-resolution cameras.

**H1-IF-004**  
H1 **shall provide at least one high-speed general expansion interface** (e.g. PCIe or equivalent) for future sensor or accelerator expansion.

**H1-IF-005**  
H1 **shall expose dedicated connectors or board-to-board interfaces** towards H2, H3 and H4 for:

- power distribution in accordance with the system power concept,  
- data communication (Ethernet, CAN, auxiliary UARTs/IO), and  
- safety / interlock signals (e.g. STO, ROBOT_ENABLE, LIMP_HOME, HEARTBEAT).

**H1-IF-006**  
H1 **shall support Wi-Fi connectivity** (Wi-Fi 6 or equivalent) including provision for external antennas or antenna connectors.

**H1-IF-007**  
H1 **shall support connection of a 4G/5G cellular modem** (either on-board or via module), including SIM access and antenna interfaces.

**H1-IF-008**  
H1 **shall support Bluetooth connectivity** (≥Bluetooth 5.0) for local human-machine interface devices and service tools.

**H1-IF-009**  
H1 shall provide at least one debug/service interface (e.g. serial console,USB device port, or JTAG access) for development and field diagnostics.

---

## 6. Power requirements

**H1-PWR-001**  
H1 shall be supplied from the robot’s 24 V system bus, with an operating input
voltage range as defined by the system power specification (e.g. 7–24 V).

**H1-PWR-002**  
H1 shall include input protection appropriate for the system power environment,
including at least:

- reverse-polarity protection,
- surge and transient suppression (e.g. TVS), and
- input filtering for conducted emissions and immunity.

**H1-PWR-003**  
H1 shall be designed to withstand the specified voltage transients and dips on
the 24 V bus without permanent damage. H1 shall provide the necessary signals
and control mechanisms (e.g. supervisor indicators, power-good and reset
controls) to allow the system to transition to a defined safe state in the
event of severe under-voltage.

**H1-PWR-004**  
H1 shall locally generate all internal power rails required by its compute
module, on-board communication devices and any directly attached peripherals,
including at minimum:

- an intermediate rail (if required by the chosen architecture),
- 5 V main,
- 3.3 V main,
- 3.3 V standby (always-on),
- 1.8 V (for low-voltage IO and MIPI domains).

**H1-PWR-005**  
Each internal power rail on H1 shall meet the voltage, current and ripple limits
specified by the components it supplies (compute module, memory, PHYs, radios,
sensors directly connected to H1, etc.).

**H1-PWR-006**  
H1 shall implement a controlled power-up and power-down sequence for its compute
module and critical on-board peripherals, including appropriate reset and
enable timing in accordance with the compute module and device design guides.

---

## 7. Safety & Supervision Requirements

**H1-SAF-001**  
H1 **shall include an independent safety and supervision controller** (MCU), supplied from an always-on rail, which remains active regardless of the compute module’s state.

**H1-SAF-002**  
The safety/supervisor MCU **shall monitor, at a minimum**:

- system input voltage (24 V bus),  
- system input current,  
- status of key internal rails (5 V, 3.3 V),  
- heartbeat or health indication from the main compute module,  
- safety-related signals exchanged with H2/H3/H4.

**H1-SAF-003**  
The supervisor **shall be able to command a controlled shutdown or inhibit**:

- the main compute module, and  
- selected power outputs and control signals towards actuators,  

in response to defined fault conditions or safety events.

**H1-SAF-004**  
Safety-related signals between H1 and other units **shall have clearly defined polarity and fail-safe defaults**, such that loss of power, cable disconnection or module reset leads to a safe condition.

---

## 8. Security Requirements (NEW)

**H1-SEC-001**
H1 shall support Hsecure boot, preventing execution of unauthorized firmware.

**H1-SEC-002**
H1 shall include hardware-based root of trust (TPM, HSM, or SoC-integrated equivalent).

**H1-SEC-003**
H1 shall enforce secure debug lockout for JTAG/SWD/UART in production mode.

**H1-SEC-004**
H1 shall support secure key provisioning during manufacturing.

---

## 9. Environmental & Reliability Requirements

**H1-ENV-001**  
H1 **shall operate correctly within the specified robot ambient temperature range**, to be defined in the product standard (e.g. −10 °C to +50 °C or project-specific).

**H1-ENV-002**  
H1 **shall withstand storage temperatures** outside the operating range without permanent damage.

**H1-ENV-003**  
H1 **shall be designed to meet the vibration and shock levels** required for the robot’s operating environment (to be defined by system engineering).

**H1-ENV-004**  
H1 **shall be designed to meet EMC/ESD requirements** according to applicable industrial/robotics standards (exact standards to be specified).

**H1-ENV-005**  
H1 **shall be designed with reliability targets** consistent with the robot’s service life (e.g. MTBF/MTTF to be defined), and shall use components with appropriate derating.

---

## 10. Verification & Process Requirements

**H1-VER-001**  
Each requirement in this specification **shall be verified** by at least one of the following methods: analysis, test, inspection or demonstration.

**H1-VER-002**  
A **requirements traceability matrix (RTM)** **shall be maintained**, linking each H1 requirement to design artefacts (architecture, schematic, layout, firmware) and verification artefacts (test cases, reports, analyses).

**H1-VER-003**  
H1 **shall comply with the chosen compute module vendor’s design guidelines** (power delivery, signaling, thermal), and such compliance shall be demonstrated through design reviews and, where applicable, measurements.

**H1-VER-004**  
H1 **shall undergo design analysis** including, but not limited to:

- Tolerance stack-up and worst-case analysis
- FMEA for critical functions
- Signal Integrity  
- Relaibility - MTTF, MTBF
- Power-integrity and thermal analyses

---
