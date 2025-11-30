# Failure Mode and Effects Analysis (FMEA)

## 1. What is FMEA?

Failure Mode and Effects Analysis (FMEA) is a **structured, systematic method** used to:

- Identify how a product or system can fail (Failure Modes)  
- Understand the **effects** of those failures on the customer, system, or safety  
- Estimate the **risk** of each failure using ratings for:
  - **Severity (S)** – How bad is the effect?
  - **Occurrence (O)** – How often is the cause likely to happen?
  - **Detection (D)** – How likely are we to detect the failure before it causes harm?
- Prioritize actions that **reduce risk** by:
  - Lowering severity where possible  
  - Reducing the probability of occurrence  
  - Improving detection with better tests, monitoring, or diagnostics  

Traditionally, risk is summarized using the **Risk Priority Number (RPN)**:

> `RPN = Severity × Occurrence × Detection`

and/or by a color **heatmap** of S/O/D ratings.

FMEA is a **living document**: it should be updated whenever the design changes, new tests reveal issues, or field experience provides new insight.

---

## 2. Types of FMEA used in this project

We are using two closely related FMEA types:

1. **Design FMEA (DFMEA)**  
   - Focus: Failures due to the **hardware design** of the H1 carrier board  
   - Uses schematic-level information, component choices, and interfaces  
   - Looks at how parts of the design can fail and affect the robot system

2. **Concept DFMEA – Safety Supervisor**  
   - Focus: High-level **safety concept** around the Safety MCU (S32K116) and its signals  
   - Works even though the safety schematic is not fully implemented in KiCad yet  
   - Evaluates risk on the level of **safety architecture** (STO lines, health lines, enable lines, power control, etc.)

> **Important:** Our FMEAs are **hardware-centric**. We intentionally **exclude failures caused purely by software, flashing, updates, or algorithms**, and concentrate on hardware architecture, components, and wiring.

---

## 3. Rating Scales

We use three 1–10 scales:

- **Severity (S)** – Impact on safety, system operation, or customer  
- **Occurrence (O)** – Likelihood of the cause happening in the design  
- **Detection (D)** – Likelihood of detecting the failure before it causes harm  

A separate **Severity / Occurrence / Detection scale table** is maintained in this workbook (with text descriptions for each score from 1 to 10). These ratings are used consistently across all DFMEA sheets.

---

## 4. Scope of Our FMEA Work

### 4.1 Inputs Available

For this hackathon, we **do not have a physical board or test data**. Our analysis is based on:

- H1 **High Level Design (HLD)** block diagrams  
- H1 **carrier-board schematics** in KiCad (`H1_Jetson_AGX_Orin_SOM.pdf` and related power / I/O schematics)  
- Word documents describing:
  - Safety Supervisor concept (S32K116 MCU and interfaces)
  - Schematic design review and improvement points

All FMEA content is therefore **design-time / paper analysis**, not measured field reliability.

---

### 4.2 H1 Carrier Design DFMEA (Sheet: “H1 Carrier Design FMEA”)

**Goal:** Understand non-safety-specific hardware risks on the H1 carrier.

**Main modules covered:**

- **Power**
  - 24 V input protection and connector
  - Buck regulators for 5 V, 3V3, 1V8, VIN_SOM
- **CSI (Cameras)**
  - Camera load switches
  - I²C mux and GPIO expander
  - CSI lane routing options
- **Ethernet**
  - Gigabit PHY, magnetics, RJ45
  - Ethernet cable / link loss behaviour
- **5G Modem**
  - VBAT power rails
  - SIM socket
- **Wi-Fi / NVMe**
  - M.2 Key-E Wi-Fi module
  - M.2 Key-M NVMe SSD
- **CAN**
  - CAN transceiver and connector
- **USB-C / USB 2.0**
  - USB-C VBUS power switches
  - CC configuration network
  - SuperSpeed pairs and ESD protection
  - Debug USB port
- **Critical Connections**
  - Jetson SOM board-to-board connector
  - H2 and H3 high-speed connectors
  - Jetson thermal management (heatsink and fan)

For each item we capture:

- Hardware **function**
- **Failure mode**, **effects**, and **causes**
- Current **design controls** (prevention and detection)
- S/O/D ratings and **RPN**
- **Recommended actions** (design changes, test requirements, docs)

---

### 4.3 Safety Supervisor Concept DFMEA  
(Sheet: “Safety Supervisor Concept DFMEA”)

**Goal:** Evaluate the robustness of the **safety architecture** around the S32K116 even before a full KiCad schematic exists.

**Key concept items:**

- Safety Supervisor MCU core and NVM
- 24 V current and voltage monitoring paths
- CAN-FD interface to H4 (battery & power)
- Control and feedback lines to Jetson:
  - Power enable, reset, shutdown, health/heartbeat
- Safety-related signals to H3 motion controller:
  - STO command path
  - Robot_Enable
  - Limp_Home
  - Motion_Healthy and Drive_Fault feedback
- Interfaces to H2 perception:
  - H2 power and reset control
  - H2 fault/health lines
- Local user interfaces:
  - Fault-acknowledge button
  - Status LEDs and buzzer

Each row describes:

- What happens if the **signal or hardware path fails**
- How the failure affects **robot safety or availability**
- Hardware-level **prevention and detection** measures required
- S/O/D and RPN
- Implementation **requirements** to carry into final schematics and tests

---

## 5. How This FMEA Will Be Used

1. **Design refinement**  
   - Drive concrete schematic changes (e.g., fuses, OVP, pull-downs, test points, feedback signals).
   - Feed into the **Design Review** document for traceability.

2. **Test planning**  
   - Many “Recommended Actions” are actually **test requirements**:
     - Production tests (continuity, power-rail checks)
     - Lab validation (brown-outs, shorts, thermal tests, STO tests)
     - System-level behaviour (link loss, limp-home, heartbeat loss)

3. **RFP deliverable**  
   - Demonstrates that the H1 team has applied **systematic risk analysis** to both:
     - The **carrier-board electronics**, and
     - The **safety supervisor concept**.

4. **Future updates**  
   - Once KiCad schematics for Safety MCU and later PCB prototypes exist, the same DFMEA sheets can be:
     - Updated with **new failure modes** or **re-rated** based on real test data.
     - Used as the starting point for **process FMEA** or **test FMEA** if required.

---