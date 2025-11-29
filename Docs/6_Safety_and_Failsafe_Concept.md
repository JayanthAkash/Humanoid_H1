## 6. Safety & Failsafe Concept

### 6.1 Safety Goals & Assumptions

The safety design of H1 is guided by these goals:

1. **Prevent unintended motion** during power-up, shutdown and fault conditions.
2. **Ensure a controlled stop** (Limp-Home or full torque-off) whenever critical faults occur in compute, power or communication paths.
3. **Maintain sufficient diagnostics** so that faults can be analysed and are not silently ignored.
4. **Fail to a safe state by default** if any safety-related component loses power or becomes disconnected.

Assumptions:

- H3 implements Safe Torque Off (STO) at the motor drive level when commanded via STO line(s).
- H4 can disconnect the 24 V bus from downstream loads via contactors or solid-state switches.
- Mechanical structure (brakes, joint limits) is sized to survive a controlled stop from the speeds specified in the RFP.

---

### 6.2 Safety Functions Implemented in H1

H1 contributes the following safety functions:

1. **Monitoring of compute health**
   - S32K116 monitors a periodic heartbeat / watchdog from Jetson. Loss of the heartbeat within a defined timeout places the system into Fault mode.

2. **Management of operating modes**
   - The safety state machine (Off, Standby, Normal, Limp-Home, Fault) lives in S32K116. It decides when H1 is allowed to command motion and when it must request STO or reduced performance.

3. **Safe enable / disable of motion**
   - Discrete outputs:
     - `Robot_Enable` – grants permission for motion in H3 when high.
     - `STO` – causes Safe Torque Off when asserted.
     - `Limp_Home` – requests reduced-speed, reduced-torque mode.
   - Lines are designed so that loss of power or broken wires tends to drive them to a safe combination (Robot_Enable low, STO asserted).

4. **Power-path control**
   - H1 can request H4 to cut or restore 24 V power; EFM8 can disable local DC-DC converters and high-power loads.
   - Non-critical loads (5G modem, some sensors, fan if failed, etc.) can be shed in Limp-Home to protect the battery and thermal margins.

5. **Supervision of power integrity**
   - Under-voltage, over-voltage or regulator faults are identified by the power-good network and interpreted as safety-relevant events by S32K116.

6. **Logging and reporting of safety events**
   - Jetson logs safety state transitions and root-cause codes provided by S32K116 (e.g. “E-Stop”, “CAN loss H3”, “Over-temp H4”).
   - These logs are stored on NVMe and can be retrieved via USB-C or network.

---

### 6.3 Interaction with H2/H3/H4 for Safety (STO, Robot_Enable, Limp_Home)

- **With H3 – Mobility & Manipulation Controller**
  - Fast, deterministic safety signals: `Robot_Enable`, `STO`, `Limp_Home`.
  - Motion commands and state feedback over Ethernet and/or CAN.
  - In Normal mode, Robot_Enable is high and STO de-asserted; H3 follows high-level commands from Jetson.
  - In Limp-Home mode, Limp_Home is asserted; H3 must enforce reduced limits regardless of application commands.
  - In Fault mode, Robot_Enable is low and STO asserted; H3 must disable torque and bring the robot to a stop.

- **With H4 – Power Management Unit**
  - CAN messages carry BMS status, charger state and power faults to S32K116 and Jetson.
  - Discrete lines allow H1 to request pre-charge, main contactor open/close, and power-enable for H1 itself and potentially other loads.
  - Severe faults in H4 (e.g. over-current, thermal runaway) result in contactor opening, which H1 treats as a safety fault and logs appropriately.

- **With H2 – Sensory & Perception Unit**
  - Although H2 is not primarily safety-critical, loss of key perception inputs (e.g. lidar, depth camera) can trigger transition to Limp-Home if redundancy is reduced.
  - H1 uses health/status messages from H2 (over Ethernet/CAN) to decide whether full-speed operation is allowed.

---

### 6.4 Safe State Behaviour & Diagnostics

**Safe states**

- **No-motion safe state**
  - `Robot_Enable` low, `STO` asserted, actuators disabled in H3.
  - 24 V may remain present for logging and communication, or H4 may open contactors.

- **Limp-Home safe state**
  - `Limp_Home` asserted, `Robot_Enable` may stay high.
  - H3 restricts speed/torque and may only allow specific trajectories (e.g. standing upright, slow walk to a safe zone).

**Diagnostics & fault handling**

- Faults are classified (sensor, compute, power, communication, user E-Stop) and latched in S32K116 until cleared.
- Jetson exposes a diagnostic API (service tool, web interface, or command-line) to read:
  - current safety mode,
  - last N fault events with timestamps,
  - rail status and power-on counters from EFM8.
- During development and production, debug connectors and test pads allow the team to observe safety signals and power behaviour with a logic analyser or scope.

These mechanisms ensure that any single serious failure results in a predictable, analysable safe state rather than uncontrolled behaviour.
