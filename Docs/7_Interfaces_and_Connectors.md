## 7. Interfaces & Connectors

### 7.1 Interface Summary Table

| Group                 | Interface / Bus                     | Direction (w.r.t. H1) | Typical Use                                   |
|-----------------------|-------------------------------------|------------------------|----------------------------------------------|
| Power                 | 24 V from H4                        | Input                  | Primary supply for H1                        |
| Power                 | Pre-charge / Enable lines           | Bi-directional         | Contactors and inrush control with H4        |
| To H2                 | Ethernet / CSI / USB                | Bi-directional         | High-bandwidth sensor data, configuration    |
| To H3                 | Ethernet and/or CAN0                | Bi-directional         | Motion commands, joint feedback              |
| To H3                 | Robot_Enable, STO, Limp_Home        | Output                 | Safety control of actuators                  |
| To H4                 | CAN1                                | Bi-directional         | BMS and power telemetry                      |
| To H4                 | Power status / reset lines          | Bi-directional         | H1 power control, reset                      |
| External network      | RJ45 Gigabit Ethernet               | Bi-directional         | Lab / debug network                          |
| Wireless              | M.2 Key-E Wi-Fi/BT                  | Bi-directional         | Wi-Fi and Bluetooth connectivity             |
| Wireless              | 5G modem (PCIe + USB)               | Bi-directional         | Cellular data link                           |
| Storage               | M.2 Key-M NVMe SSD                  | Local                  | OS, models, logging                           |
| Cameras & lidar       | USB 3.2, CSI connectors             | Bi-directional         | RealSense, monocular camera, lidar          |
| Service               | USB-C ports (2× user, 1× service)   | Bi-directional         | Firmware update, console, external devices   |
| Debug / Manufacturing | JTAG/SWD, UART, test pads           | Bi-directional         | Programming, bring-up, end-of-line test      |

---

### 7.2 Connector to H2 – Sensory & Perception Unit

The H2 unit concentrates head-mounted cameras and IMUs. H1 connects to it via:

- **Gigabit Ethernet** (if H2 has its own MCU or FPGA):  
  - Carries compressed image streams, time stamps and health/status messages.

- **Alternatively / additionally, CSI & USB links**:  
  - CSI flat-flex cables from H1’s CSI connectors to head camera modules.  
  - USB 3.2 for Intel RealSense and other USB-based sensors.

- **Control & sync GPIOs** (optional):  
  - Frame-sync signals, trigger lines or GPIO interrupts can be routed via spare pins on the H2 harness.

The logical view is that H1 receives all perception data from H2 with sufficient bandwidth and low latency to feed its vision pipelines.

---

### 7.3 Connector to H3 – Mobility & Manipulation Controller

The H3 controller interfaces the joint drives and grippers. H1 provides:

- **CAN0 bus**  
  - Deterministic messaging for joint targets, drive telemetry and fault reporting.

- **Optional Ethernet link**  
  - Higher-bandwidth channel for richer state (joint torques, temperature maps) or future extensions (TSN, distributed control).

- **Safety lines**  
  - `Robot_Enable`, `STO`, `Limp_Home` are wired in a dedicated twisted-pair harness for noise immunity.  
  - These lines must be interpreted by H3 even if CAN/Ethernet is lost.

H3 returns the robot’s motion state and safety acknowledgement signals (e.g. “drives ready”, “STO active”) so that H1 can supervise transitions between modes.

---

### 7.4 Connector to H4 – Power Management Unit

The H4 unit manages the 24 V Li-ion battery pack and charging. The H1–H4 interface consists of:

- **24 V main power feed** into H1’s power tree.  
- **CAN1 bus** for:  
  - BMS measurements (voltage, currents, SoC, temperatures),  
  - charger status (plugged in, charging current),  
  - fault codes (over-voltage, under-voltage, over-current).  
- **Discrete control lines** (number and polarity to be finalised):  
  - `H1_POWER_EN` / `H1_PRECHARGE` – request to energise H1.  
  - Optional `RESET_OUT` / `FAULT_IN` lines for tighter coordination in severe fault cases.

This interface allows H1 to both **influence** and **react to** power decisions at system level.

---

### 7.5 External RF & Network Interfaces (Wi-Fi/BT, 5G, Ethernet)

- **Wi-Fi/BT (M.2 Key-E)**  
  - PCIe / SDIO + UART control lines connect to Jetson’s host interface.  
  - RF ports lead to external dual-band antennas on the humanoid shell.  
  - GPIOs from Jetson or S32K116 control `W_DISABLE`, reset and power-enable pins.

- **5G Modem**  
  - PCIe x2 and USB 3.2 signals connect to the modem; a SIM card connector is provided on the carrier.  
  - Multiple RF ports route to 4G/5G and GNSS antennas, respecting modem layout guidelines.

- **Ethernet**  
  - RGMII connection from Jetson to Gigabit PHY, then to:  
    - RJ45 for lab network, or  
    - internal harness connector towards other humanoid units.

These interfaces make H1 the primary network and cloud gateway for the robot.

---

### 7.6 Debug & Manufacturing Interfaces (JTAG/SWD, UART, Test Pads)

To support development, bring-up and manufacturing test, H1 exposes:

- **JTAG/SWD headers**  
  - For S32K116 safety MCU and EFM8 supervisor.  
  - Used for programming firmware, updating bootloaders and running boundary-scan or production tests.

- **Jetson debug access**  
  - USB-C service port can place Jetson into recovery mode for reflashing.  
  - Optional UART console broken out via an internal header for low-level debugging.

- **Test pads (TPs) on critical nets**  
  - Power rails: 24 V in, VIN_SOM, 5 V, 3.3 V, 3.3 V_STBY, 1.8 V.  
  - Power-control lines: `VIN_PWR_ON`, `CARRIER_POWER_ON`, `MODULE_POWER_ON`, `MODULE_SHDN_N`, `SYS_RESET_N`, `FORCE_SHUTDOWN`.  
  - High-speed / differential nets (PCIe, USB, CSI) may have carefully placed pads or loop-backs for eye-diagram and signal-integrity validation (where allowed by the layout).

- **Manufacturing test hooks**  
  - Reserved GPIOs and I²C lines from Jetson or S32K116 can be temporarily re-purposed for in-circuit test (ICT) patterns, LED blink codes, etc.

Together, these interfaces ensure that H1 can be efficiently verified in the lab, tested on the production line and serviced in the field without dismantling the entire humanoid.
