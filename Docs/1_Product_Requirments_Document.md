# H1 Product Requirements Document (PRD)

## Main Robotics Processing And Communications Unit

# 1. Product Purpose

H1 is the main robotics processing and communications unit for the 32-DOF humanoid robot. It is designed to solve the fundamental challenge in humanoid robotics: the need for a unified, high-performance compute and communication platform that can reliably process large volumes of sensor data, execute advanced AI workloads and coordinate all distributed subsystems in real time.

H1 provides the essential intelligence and communication backbone for the entire humanoid platform acting as the robot’s high compute “brain”.
H1 provides the foundational electronics infrastructureor autonomy algorithms, connectivity, diagnostics, safety-state coordination and fleet-level scalability. As a modular and production-ready platform, it supports current and future humanoid robot variants

The primary users of H1 are the robot’s internal subsystems : H2 (sensing), H3 (mobility and joint controllers) and H4 (power and energy management) which depend on continuous, deterministic communication with a central compute unit. In addition, engineering teams, manufacturing teams and cloud/operations teams rely on H1 as the foundation for integration, testing, deployment, telemetry and OTA updates. Ultimately, end users benefit from the system performance, reliability, and capabilities enabled by H1.

# 2. Product Overview

H1 is a consolidated high compute and communications electronics module that integrates:

- A scalable high-performance compute module delivering ≥200 TOPS of AI performance, with high memory capacity and high memory bandwidth for fast and efficient data processing
- High-speed data interfaces and low-latency control buses for real-time subsystem communication
- Integrated wireless connectivity, including WiFi-6, Bluetooth 5.x and support for external 5G modems
- A robust power subsystem with regulation, protection and sequencing
- Industrial-grade reliability features and safety-critical hardware
- Manufacturing and service interfaces for development, testing and production use
- Comprehensive health monitoring and telemetry for system diagnostics and fleet operation
- A modular board assembly optimized for integration, routing and thermal design

# 3. Scope and Non-Goals

## 3.1 In-Scope (Electronics Only)

H1 focuses exclusively on the electronics required to deliver compute, communication and system orchestration.
H1 responsibilities include:

- High-performance compute subsystem integration
- Carrier board electronics design
- Power regulation, sequencing and protection
- High-speed communication bus interfaces
- Wireless and cellular connectivity
- Diagnostics and testability
- Security hooks and manufacturing interfaces
- PCB and integration elements
  
## 3.2 Out of Scope

- Mechanical parts (enclosure, heatsinks, brackets)
- Software/OS/AI algorithms (middleware, autonomy, locomotion logic)
- Sensor/motor/actuator electronics
- Battery, BMS and high-power distribution
- Cloud services and OTA back-end logic
- Formal regulatory certifications (FCC/CE/UL/PTCRB/RED; only pre-compliance is considered)
- Manufacturing operations (fixtures, automation, supply chain mgmt.)

# 4. Product Goals

The following goals define the essential outcomes H1 must achieve as the centralized compute and communications platform for the humanoid robot. These goals guide the design direction, engineering decisions and validation criteria for the product.

## 4.1 Primary goals

- **Centralize high-performance computation**: Provide the robot with a unified compute platform capable of supporting real-time perception, planning and decision-making workloads.

- **Enable deterministic subsystem communication**: Enable deterministic, low-latency communication with all internal subsystems (H2 sensing, H3 mobility, H4 power) for coordinated robot operation.

- **Provide robust internal and external connectivity**

- **Deliver a reliable power integration subsystem** with proper protection, regulation, sequencing

## 4.2 Business and Product Goals

- **Reduce system integration complexity**: Standardize the compute and communication architecture across the humanoid platform, simplifying hardware and software integration
  
- **Enable scalable manufacturing**: Support reliable production at volumes up to 10,000 units per year through a manufacturable and testable electronics design.
  
- **Accelerate development of humanoid capabilities and time-to-market**: Provide a stable compute foundation that shortens development cycles for autonomy, perception, control and cloud-connected features.
  
- **Ensure long-term extensibility**: Enable future enhancements in AI, autonomy, software, and cloud services without requiring major hardware redesigns.
  
- **Establish a reusable platform baseline**: Position H1 as the standard compute module for current and future humanoid robot variants, ensuring consistent architecture, predictable performance and reduced engineering effort across product generations

## 4.3 Success Criteria

H1 is considered successful when it reliably delivers the performance, integration capability, and operational stability required for the humanoid robot platform. The following criteria define measurable outcomes across technical, reliability, integration, and business dimensions.

### 4.3.1 Technical Success Criteria

- Real-time compute performance:Supports perception, planning, and control workloads without missed cycles, dropped frames, or latency overruns
- Deterministic subsystem communication: maintains low-latency, low-jitter communication with H2, H3, and H4 under full load.
- Stable power operation: Correct regulation, sequencing, protection, and telemetry across the full voltage and load range.
- Reliable wireless connectivity:
- Thermal & electrical robustness: No throttling, resets, or instability within the specified thermal and electrical operating envelope.

### 4.3.2 Reliability and Safety Criteria

- Fault-tolerant operation: System recovers gracefully from internal resets, communication errors, and subsystem faults.
- Correct safety signaling: Heartbeats, watchdogs, and fault lines operate within defined timing constraints
- Pre-compliance readiness:Passes internal EMC, ESD, and thermal pre-compliance thresholds without major redesign.
- Stable long-duration operation:No unexpected resets, hangs, or unsafe behavior during extended stress and endurance tests.

### 4.3.3 Integration and Manufacturing Criteria

- Seamless integration: Interfaces with H2, H3 and H4 without architecture or pinout rework.
- EVT/DVT/PVT progression:Meets acceptance criteria for engineering, design and production validation builds.
- Manufacturing testability: All debug and test interfaces (JTAG, UART, boundary scan, test points) support automated EOL testing.
- Scalable production:Design supports manufacturing at volumes up to 10,000 units/year.

### 4.3.4 Business and Platform Criteria

- Reusable compute platform: H1 is adopted as the standard compute module for multiple humanoid robot variants.
- Reduced integration effort: Lowers integration complexity for autonomy, perception, and control teams.
- Faster feature deployment: Enables rapid rollout of new humanoid capabilities without hardware redesign.
  
# 5. Constraints

## 5.1 System Constraints

- 24 V power architecture: H1 must operate from the robot’s 24 V power input and comply with system-level power distribution constraints.
- Defined subsystem interfaces: Interfaces to H2 (sensing), H3 (mobility), and H4 (power) must follow agreed communication protocols and timing requirements
- Physical integration envelope: H1 must fit within the mechanical space allocated in the humanoid torso electronics bay (dimensions provided by system team).
- Thermal envelope limits: H1 must meet thermal performance inside the system-level cooling constraints (no dedicated active cooling provided by H1).
- Real-time communication constraints: H1 must support deterministic, low-jitter communication with distributed controllers under system load.

## 5.2 Electrical & Environmental Constraints

- Operating voltage, load and transient limits:
All power electronics must operate within the system-defined voltage ranges, transient behaviors and grounding constraints.
- EMI/EMC design guidelines: H1 must adhere to internal EMC/ESD design rules to support future system-level certification.
- RF coexistence constraints: Placement, filtering, and routing must avoid interference with other RF systems within the robot.
- Component availability and lifecycle: Components must meet minimum lifecycle and availability requirements for 10k units/year production.

## 5.3 Organizational & Project Constraints

- Common architecture across variants: H1 must maintain compatibility across current and future humanoid robot variants with minimal redesign.
- Manufacturability and testability: PCB stack-up, connectors, and interfaces must support automated testing and scalable assembly.
  
# 6. Assumptions

- Subsystem interface definitions will be stable and provided by H2, H3, and H4 teams. H2, H3, H4 will deliver finalized ICDs (timing, pinouts, electrical levels) before layout freeze.
- 24-V bus electrical characteristics (surges, ripple, noise limits) will be defined and remain stable.
- A centralized cooling solution will be provided by the system/mechanical team (e.g., shared heatsink or thermal interface), not by the H1 electronics team.
- The selected compute module  will remain available throughout development.
- Software will provide drivers for compute, WiFi, BT, 5G, and bring-up images before EVT.
- Mechanical design will provide PCB outline, mounting, and antenna placement constraints.
- Manufacturing partner supports 8-layer high-speed boards and EOL test processes. Manufacturing test infrastructure (fixtures, automation software) will be provided by the manufacturing engineering team; H1 will only provide electrical interfaces.
- Long-lead components (PMIC, supervisors, RF modules) will be procurable within timelines.

# 7. Dependencies

- Internal Teams:
  - H2 (Sensors): Sensor ICDs, bandwidth/timing specs, electrical compatibility.
  - H3 (Mobility): Motor/actuator ICDs, control-loop timing and latency
  - H4 (Power): 24 V power ICD, transients, sequencing, protection & fault lines
  - Mechanical: Board envelope, connector placements, mounting, thermal & RF zones.
  - Software: Drivers, WiFi/BT/5G, BSPs for EVT
  - Security: Security workflow, debug lockout rules
  - Manufacturing: EOL test requirements, fixture needs,Fixture readiness, AVL approvals
  - Quality and Compliance: Environmental & reliability test requirements (EVT/DVT/PVT), EMC/ESD/RF guidelines, certification considerations.
  - System Engineering: System architecture, timing budgets, mechanical/thermal constraints.
- External Vendors:
  - Compute module vendor (docs, errata, availability)
  - RF module vendors (5G, WiFi/BT)
  - EMC pre-compliance labs

# 8. Stakeholders

The development of H1 involves multiple teams across hardware, software, system engineering, manufacturing, and operations. This chapter identifies all key stakeholders and their responsibilities to ensure alignment, accountability, and effective decision-making throughout the product lifecycle.

| **Stakeholder**                            | **Responsibilities**                                                                                                                      |
| ------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------- |
| **Product Owner / Product Management**     | Defines requirements, owns PRD, prioritizes scope, aligns H1 with overall humanoid platform strategy.                                     |
| **H1 Electronics Hardware Team**           | Schematics, PCB layout, SI/PI, power design, RF routing, component selection, validation, and pre-compliance readiness.                   |
| **Firmware / Platform Software Team**      | Board bring-up, bootloader, drivers, BSP enablement, RF/WiFi/BT/5G integration, PCIe/Ethernet/USB support, hardware diagnostics.          |
| **Mechanical / Industrial Design Team**    | Enclosure design, board mechanical envelope, connector placement, mounting points, thermal interface zones, antenna placement guidelines. |
| **H2 Sensing Team**                        | Sensor ICDs, bandwidth and timing requirements, sensor electrical compatibility, real-time data pipeline requirements.                    |
| **H3 Mobility & Joint Control Team**       | ICDs for motor controllers, actuator buses, real-time control loop requirements, heartbeat/fault signaling.                               |
| **H4 Power & Energy Management Team**      | Power ICDs, 24 V input characteristics, transient/load requirements, power sequencing, protection signals.                                |
| **Security / Platform Security Team**      | Secure boot requirements, key provisioning flows, debug lock, hardware security guidelines.                                               |
| **Manufacturing Engineering / Operations** | DFM, DFT, boundary scan, test fixtures, EOL test strategy, production ramp readiness, mass-production support.                            |
| **Quality & Reliability Engineering**      | Environmental, thermal, vibration, reliability requirements; EVT/DVT/PVT validation and qualification testing.                            |
| **Compliance / Regulatory Team**           | EMC, ESD, safety, RF guidelines; coordination of system-level certification programs.                                                     |
| **System Engineering**                     | System architecture ownership; cross-subsystem constraints; timing/power/thermal budgets; H1-H2-H3-H4 integration.                        |


## 8.1 Stakeholders RACI Matrix for H1 development

**Legend**:

- R = Responsible (owns the work / executes)
- A = Accountable (final decision-maker / sign-off)
- C = Consulted (provides input / expertise)
- I = Informed (kept updated)

| **Activity / Deliverable**                            | Product Owner | H1 Hardware | Firmware / BSP | Mechanical | H2 | H3 | H4 | Security | Manufacturing / Ops | Quality & Reliability | Compliance | System Engg | Cloud / OTA |
| ----------------------------------------------------- | ------------- | ----------- | -------------- | ---------- | -- | -- | -- | -------- | ------------------- | --------------------- | ---------- | ----------- | ----------- |
| **PRD definition & approval**                         | R/A             | C           | C              | I          | I  | I  | I  | C        | I                   | I                     | I          | C           | I           |
| **Schematic design**                                  | I             | R/A         | C              | C          | C  | C  | C  | C        | C                   | C                     | C          | C           | I           |
| **PCB layout & SI/PI**                                | I             | R/A         | C              | C          | C  | C  | C  | C        | C                   | C                     | C          | C           | I           |
| **Power architecture (24 V integration)**             | C             | R           | C              | C          | I  | I  | A  | C        | C                   | C                     | C          | C           | I           |
| **High-speed interface design (PCIe, USB, Ethernet)** | I             | R           | C              | C          | C  | C  | C  | C        | C                   | C                     | C          | C           | I           |
| **RF routing & antenna placement**                    | I             | R           | C              | A          | I  | I  | I  | C        | C                   | C                     | A/C        | C           | I           |
| **Firmware bring-up & drivers**                       | I             | C           | R/A            | I          | C  | C  | C  | C        | C                   | C                     | I          | C           | I           |
| **Interface definitions (ICDs)**                      | I             | C           | C              | I          | A  | A  | A  | C        | I                   | I                     | I          | R/A         | I           |
| **Thermal / mechanical integration**                  | I             | C           | I              | R/A        | I  | I  | I  | I        | C                   | C                     | I          | C           | I           |
| **Security enablement (secure boot, debug lock)**     | I             | C           | C              | I          | I  | I  | I  | R/A      | I                   | I                     | C          | C           | I           |
| **Manufacturability (DFM/DFT)**                       | I             | C           | C              | C          | I  | I  | I  | I        | R/A                 | C                     | I          | C           | I           |
| **End-of-Line test design**                           | I             | C           | C              | I          | I  | I  | I  | C        | R/A                 | C                     | I          | C           | I           |
| **EVT/DVT/PVT qualification**                         | I             | R           | C              | C          | C  | C  | C  | I        | C                   | R/A                   | C          | C           | I           |
| **EMC/ESD pre-compliance**                            | I             | R           | C              | C          | I  | I  | I  | I        | C                   | R                     | A          | C           | I           |
| **System integration & robot bring-up**               | I             | R           | C              | C          | C  | C  | C  | I        | I                   | C                     | I          | A/R         | I           |

# 10. Use case

The humanoid robot is designed for service, industrial, research, and remote-operation environments where human-like 32-DOF movement, real-time AI processing (≥200 TOPS) and reliable wireless connectivity are essential. H1 enables these applications by providing the centralized compute, communication, and safety backbone of the robot.

# 10. High-Level Features

| **Category**                              | **High-Level Feature**                  | **What It Enables (Electronics Scope)**                     |
| ----------------------------------------- | --------------------------------------- | ----------------------------------------------------------- |
| **High-Performance Compute**              | Centralized AI compute platform         | Real-time perception, planning, and sensor fusion.          |
|                                           | High memory & data bandwidth            | Smooth processing of multi-camera and multi-sensor streams. |
|                                           | Modular compute architecture            | Upgradeable compute without redesigning subsystems.         |
| **Deterministic Subsystem Communication** | Low-latency communication with H2/H3/H4 | Stable robot behavior across 32 DOF.                        |
|                                           | High-bandwidth sensor interfaces        | Ingestion of camera, IMU, force sensors, etc.               |
|                                           | Synchronized subsystem timing           | Predictable locomotion and coordinated motion.              |
| **Wireless & External Connectivity**      | WiFi-6 + Bluetooth 5.x                  | Cloud access, diagnostics, device pairing.                  |
|                                           | 5G modem support (M.2 Key-B)            | OTA updates, remote monitoring, teleoperation.              |
|                                           | Flexible RF architecture                | Swappable modems, configurable antenna placement.           |
| **24 V Power Integration**                | Native 24-V system compatibility        | Seamless integration with robot power architecture.         |
|                                           | Regulated internal rails & sequencing   | Deterministic bring-up and stable power domains.            |
|                                           | Power health monitoring                 | Fault detection and safe-state transitions.                 |
| **Reliability & Safety**                  | Industrial-grade EMC/ESD design         | Stable operation in noisy/harsh environments.               |
|                                           | Deterministic operation under load      | Prevents unsafe subsystem miscoordination.                  |
|                                           | Hardware watchdog & fault hooks         | Safe recovery from hardware faults.                         |
| **Diagnostics & Observability**           | Built-in telemetry & monitoring         | Debugging, health checks, field servicing.                  |
|                                           | Debug interfaces (with lockout)         | Engineering bring-up with secure access control.            |
|                                           | Manufacturing test support              | High-volume production (10k units/year).                    |
| **Platform Scalability**                  | Standardized interfaces (H2/H3/H4)      | Consistent integration across robot variants.               |
|                                           | Expandable I/O & compute pathways       | Additional sensors or future capabilities.                  |
|                                           | Reusable compute platform               | Lower redesign cost for new robot generations.              |
| **Security (Electronics Scope)**          | Secure debug lockout capability         | Prevents unauthorized physical access.                      |
|                                           | Tamper-resistant power/reset circuits   | Protection against fault injection/glitch attacks.          |
|                                           | RF/compute isolation & shielding        | Reduced attack surface and predictable RF performance.      |
|                                           | Secure manufacturing provisions         | Fuse programming and production-test lockout.               |


# 10. Release Roadmap

Assumptions

- Subsystem ICD Maturity: Interface Control Documents (ICDs) from H2, H3, and H4 are currently in draft form and are expected to evolve during early design phases.
- Design Reuse: No existing electronics or board designs are being reused for H1; the solution is assumed to be a new, from-scratch design.
- Team Capacity: The electronics development team (schematic, layout, validation) consists of five or more engineers, with sufficient capacity for parallel work across design and bring-up phases.

| **Phase**                                | **Timeline** | **Objectives**                                      | **Key Workstreams**                                                                                                                | **Exit Criteria**                                                       |
| ---------------------------------------- | ------------ | --------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------- |
| **1. Concept & Alignment**               | Month 0–1    | Define scope, feasibility, and system alignment     | • PRD finalization<br>• Draft ICD alignment<br>• Compute SOM evaluation<br>• Initial architecture<br>• Risk register               | • PRD v1.0<br>• ICD draft v0.5<br>• Feasibility confirmed               |
| **2. Architecture & Schematic Design**   | Month 1–3    | Complete system electrical design                   | • Power tree design<br>• PCIe/CSI/Ethernet definition<br>• RF/5G integration plan<br>• Schematic v1.0<br>• ICD v0.7<br>• BOM v0.5  | • Schematic approved<br>• ICD stable<br>• Mechanical constraints frozen |
| **3. PCB Layout & EVT Build**            | Month 3–5    | Produce EVT prototype                               | • High-speed routing<br>• RF routing<br>• EMC-aware layout<br>• DFM review<br>• EVT build execution                                | • Layout v1.0<br>• EVT boards delivered                                 |
| **4. EVT – Engineering Validation Test** | Month 5–7    | Validate electrical bring-up and core functionality | • Power bring-up<br>• SOM boot testing<br>• CAN/Eth/PCIe/CSI validation<br>• RF checks<br>• Thermal hotspot evaluation             | • EVT report<br>• Fix list for DVT                                      |
| **5. DVT – Design Validation Test**      | Month 7–10   | Validate reliability, stress, and pre-compliance    | • DVT hardware<br>• Stress and soak tests<br>• EMC/ESD testing<br>• RF tuning<br>• Integration validation<br>• EOL plan definition | • DVT passed<br>• Ready for PVT                                         |
| **6. PVT – Production Validation Test**  | Month 10–12  | Validate large-scale manufacturability              | • PVT build<br>• Pilot production run<br>• Fixture validation<br>• EOL testing<br>• Final BOM release                              | • 95% FPY achieved<br>• Manufacturing validated                         |
| **7. MP – Mass Production**              | Month 12+    | Ramp to 10k units/year                              | • Volume production<br>• Yield tracking<br>• ECO management<br>• Field support readiness                                           | • Stable MP pipeline established                                        |

## 10.1. Key Risks & Impact
| **Risk Area**            | **Issue**                              | **Impact**                           | **Worst-Case Effect**   |
| ------------------------ | -------------------------------------- | ------------------------------------ | ----------------------- |
| **ICD Uncertainty**      | Late changes from H2/H3/H4 teams       | Requires schematic + layout rework   | **+1–2 months** delay   |
| **RF Complexity**        | 5G/WiFi detuning or coexistence issues | Antenna/matching redesign, rerouting | **+1–2 months** delay   |
| **EMC/ESD Failures**     | Failure in pre-compliance tests        | PCB respin, layout changes           | **+1–2 months** delay   |
| **SOM Availability**     | Vendor delivery delays                 | Bring-up schedule pushes out         | **+2–4 weeks** delay    |
| **Mechanical Changes**   | Connector or outline changes           | Layout redo, rerouting               | **+3–6 weeks** delay    |
| **Manufacturing Issues** | Fixture readiness gaps                 | PVT schedule slip                    | **+1–1.5 months** delay |

## 10.2. Risk-Adjusted Timeline Summary

| **Phase**                  | **Best Case** | **Realistic** | **Worst Case** |
| -------------------------- | ------------- | ------------- | -------------- |
| **Concept & Alignment**    | 3 weeks       | 4–5 weeks     | 6–8 weeks      |
| **Schematic Design**       | 6 weeks       | 7–9 weeks     | 10–14 weeks    |
| **PCB Layout + EVT Build** | 6 weeks       | 7–8 weeks     | 12–14 weeks    |
| **EVT**                    | 4–6 weeks     | 6–8 weeks     | 10+ weeks      |
| **DVT**                    | 6–8 weeks     | 8–10 weeks    | 12–16 weeks    |
| **PVT**                    | 5–6 weeks     | 6–7 weeks     | 8–12 weeks     |
| **MP**                     | Month 10–11   | Month 12–14   | Month 16–18    |
