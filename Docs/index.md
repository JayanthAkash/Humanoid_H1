# Introduction

## 1.1 Context – FIT.Fest ’25 Hackathon

The **BGSW FIT.Fest ’25 Hackathon** has issued a *Request for Proposal (RFP)* for the design of a complete electronics platform for a **32-DOF humanoid robot**.

The RFP defines four major electronic sub-systems:

- **H1 – Main Robotics Processing and Communications Unit**
- **H2 – Robotics Sensory and Perception Management System**
- **H3 – Robotics Mobility and Manipulation Controller**
- **H4 – Robotics Power Management Unit**

Each project must deliver, at minimum:

- Product Requirements Document
- HW / SW / Mechanical Requirements
- High-Level Design / Architecture document
- Schematics, CAD and relevant software code
- Design analysis: WCA, tolerance stack-up, signal integrity, reliability (MTTF/MTBF), FMEA
- Component and module test plans
- Bill of Materials with cost estimation for 10k units

This document is the response and design proposal for **H1 – Main Robotics Processing and Communications Unit**.

---

## 1.2 Purpose of this Document

The purpose of this document is to:

- Present a clear **system-level understanding** of the humanoid platform and the role of H1 within it.
- Capture the **product vision and requirements** for H1 in alignment with the hackathon RFP and the overall humanoid use-cases (research, demo, and future commercialisation).
- Describe the **high-level architecture** of H1, including:
  - Main compute module and safety supervisor
  - On-board communication buses and external interfaces
  - Power-tree relevant to H1 and its peripherals
- Provide a basis for **detailed hardware design, analysis and verification**, including:
  - Product and HW/SW requirements
  - High-level design and interface definitions
  - Design analysis (WCA, tolerance stack-up, signal integrity, reliability, FMEA)
  - Component and module-level test plans
  - Bill of Materials (BoM) and cost targets for 10k-piece volume

In simple terms, this introduction explains **what we are building, why it matters, and how the rest of the document is organised**. Later sections will go deeper into the actual circuits, interfaces and analysis.

---

## 1.3 Role of H1 in the Humanoid System

H1 is the **“brain and backbone”** of the humanoid.

From a functional perspective, H1:

- Hosts the **main high-compute CPU/GPU module** (e.g. Jetson AGX Orin SOM) capable of:
  - AI performance of **>200 TOPS**
  - **32 GB memory** for perception, mapping and planning workloads
- Orchestrates all high-level **robot behaviour**, including:
  - Sensor fusion and perception (fed mainly via H2)
  - Motion planning and trajectory generation (commands sent to H3)
  - System monitoring, diagnostics and communication with back-end/cloud services
- Interfaces with other electronic units over robust, high-bandwidth channels:
  - **To H2 (Sensory & Perception)** – CSI/LVDS, Ethernet or similar links for camera and sensor data
  - **To H3 (Mobility & Manipulation)** – real-time command and feedback via Ethernet, CAN-FD or other deterministic bus
  - **To H4 (Power Management)** – telemetry, safety states, pre-charge, power-enable and fault signalling
- Provides **external connectivity** for the humanoid:
  - Ethernet for wired networking
  - Wi-Fi 6 and 5G cellular modem for wireless connectivity
  - Bluetooth 5 for accessories and local devices
- Operates from the system’s **24 V power architecture**, with local DC-DC conversion to all rails required by the compute SOM and peripherals. H1 must tolerate real-world power conditions (transients, dips, inrush) while maintaining a defined safe state under fault.

Conceptually, if the humanoid robot were a small distributed computer system:

- **H1** is the central compute and communication hub.
- **H2/H3/H4** are “smart peripherals” connected to it.
- The overall behaviour and safety of the robot strongly depend on the robustness and clarity of the design choices made in H1.

---

## 1.4 Scope

The scope of this document is **limited to the electronics of H1**. Specifically, we cover:

- **Main compute SOM and support circuits**
  - Power, reset, boot configuration, clocking and storage (NVMe, eMMC, etc.)
- **Communication interfaces**
  - High-speed: Ethernet, PCIe/NVMe, USB, CSI/LVDS as applicable
  - Control buses: CAN / CAN-FD, I²C, SPI, UART for interfacing with H2/H3/H4 and local peripherals
  - Debug and maintenance ports (USB-C, UART console, JTAG where applicable)
- **Safety and supervision**
  - Safety supervisor MCU and its interfaces to H2/H3/H4
  - Safety-critical signals such as Robot_Enable, STO, Limp_Home, fault lines, watchdogs, and heartbeat monitoring
- **Local power-tree**
  - Conversion from 24 V input to intermediate rails and point-of-load converters
  - Protection, monitoring and sequencing related to H1
- **Connectors and physical interfaces**
  - Board-to-board and harness connectors to the other units
  - Placement constraints and design considerations that impact cabling and EMC
- **Design for test and manufacturing**
  - Test points, boundary-scan/JTAG access (where appropriate)
  - Built-in diagnostics hooks to support production test and field service

Out of scope for this document (but referenced where needed) are:

- Mechanical design of the humanoid and housing
- Detailed motor driver electronics and joint actuators (primarily in H3)
- High-level software algorithms beyond what is required to derive hardware needs

Subsequent sections build on this introduction to provide a structured, engineering-ready description of the **H1 Main Robotics Processing and Communications Unit**, including requirements, architecture, detailed design, analysis and test strategy.
