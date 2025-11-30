# H1 – Tolerance Stack-Up Analysis (Critical Aspects)

**Board:** H1 – Main Robotics Processing & Communications Unit (etson AGX Orin carrier -schematic-only)  

---

## 1. Objective & Scope

The objective of this report is to compute **worst-case min/max values** for the most **critical electrical quantities** in the H1 schematic, considering component tolerances and the specified system input range of **7–24 V**.

The analysis verifies that, under worst-case combinations of tolerances:

- All **critical supply rails** stay within the limits required by Jetson AGX Orin, S32K116, and other peripherals.
- **Current limits and shunt measurements** operate within safe and predictable bands.
- **ADC front-ends** used by S32K116 do not exceed absolute maximum ratings and have bounded error.
- **Logic-level straps and safety defaults** remain valid at tolerance extremes.
- **Reset and power-up timing** remains safe.

This is a **schematic-level** analysis and will serve as input to later **Worst-Case Analysis (WCA)** and **FMEA** documents.

---

## 2. Assumptions

### 2.1 Electrical

- **System supply (24 V bus):** 7 V … 24 V (as per system specification).
- **Temperature range:** Covered implicitly by datasheet worst-case values (no separate thermal model here).

### 2.2 Component Tolerances (unless otherwise stated)

- Standard resistors: **±1 %**
- Shunt resistors: **±3 %**
- XL4015 (1.25 V ref buck): **±2 % Vref**
- SIC431 (0.6 V ref buck): **±1 % Vref**
- NCP730 3.3 V LDO: **±2 % Vout**
- TPS7A05 1.8 V LDO: **±2 % Vout**
- AP22615 current-limit coefficient: **±5 %**
- Capacitors in RC timing: **±10 %**

### 2.3 Out of Scope

- PCB-dependent SI (impedance, eye diagrams, crosstalk, via stubs)  
- Mechanical tolerances (connector positions, hole sizes, etc.)  
- Long-term ageing, thermal cycling and detailed MTBF (covered in reliability analysis)  
- Detailed CAN/RGMII/USB protocol timing (will be covered by interface timing budget)

---

## 3. DC Supply Voltages

### 3.1 Summary Table

| Rail               | Source / Circuit                          | Nominal | Worst-Case Min | Worst-Case Max | Comments / Verdict |
|--------------------|-------------------------------------------|---------|----------------|----------------|--------------------|
| 24 V system bus    | External PSU/BMS                          | 24 V    | **7 V**        | **24 V**       | System-level spec; H1 designed to tolerate full range. |
| VCC / VIN_SOM      | XL4015 buck (R30=100 kΩ, R31=7.2 kΩ)      | 18.6 V  | **17.9 V**     | **19.3 V**     | Intermediate bus feeding 5 V & 3.3 V bucks; ~±3.8 % spread. |
| 5 V main           | SIC431 buck                               | 4.99 V  | **4.86 V**     | **5.13 V**     | Jetson / USB / camera / external 5 V; within 5.0 V ±5 %. |
| 3.3 V main         | SIC431 buck                               | 3.32 V  | **3.23 V**     | **3.41 V**     | For PHY, IO, sensors; within 3.3 V ±5 %. |
| 3.3 V_STBY         | NCP730 LDO from 5 V                       | 3.3 V   | **3.23 V**     | **3.37 V**     | Always-on S32K116/supervisor domain; within MCU supply range. |
| 3.3 V_ANA (VDDA)   | 3.3 V_STBY via ferrite bead + filter      | ~3.3 V  | **≈3.22 V**    | **≈3.36 V**    | Same tolerance as 3.3 V_STBY minus bead drop; safe for ADC. |
| 1.8 V IO           | TPS7A05 fixed 1.8 V LDO                   | 1.8 V   | **1.76 V**     | **1.84 V**     | CSI/MIPI and low-voltage IO; within typical 1.8 V IO window. |

### 3.2 Key Calculations (Short)

**VCC / VIN_SOM – XL4015 buck**

- Formula:  
  \( V_\text{VCC} = V_\text{REF} \cdot \left(1 + \dfrac{R_{30}}{R_{31}}\right) \)
- Vref_nom = 1.25 V, R30 = 100 kΩ, R31 = 7.2 kΩ  
  → V_VCC_nom ≈ 18.6 V
- With Vref ±2 %, R30/R31 ±1 % → VCC_min ≈ 17.9 V, VCC_max ≈ 19.3 V.

**5 V main – SIC431**

- Formula:  
  \( V_{5V} = 0.6 \cdot \dfrac{R_\text{TOP}+R_\text{BOT}}{R_\text{BOT}} \)
- RTOP = 73.2 kΩ, RBOT = 10 kΩ → V_5V_nom ≈ 4.99 V
- With Vref ±1 %, resistors ±1 % → V_5V_min ≈ 4.86 V, V_5V_max ≈ 5.13 V.

**3.3 V main – SIC431**

- Same formula with RTOP = 45.3 kΩ, RBOT = 10 kΩ → V_3V3_nom ≈ 3.32 V
- → V_3V3_min ≈ 3.23 V, V_3V3_max ≈ 3.41 V.

**3.3 V_STBY / 1.8 V LDOs**

- 3.3 V_STBY = 3.3 V ±2 % → 3.23–3.37 V  
- 1.8 V = 1.8 V ±2 % → 1.76–1.84 V

**Outcome:** All DC rails that power Jetson, S32K116 and main peripherals are inside their standard ±5 % design windows under worst-case tolerances.

---

## 4. Currents, Limits & Shunts

### 4.1 AP22615 Load-Switch Current Limit (per Channel)

**Use case:** CSI rails, USB VBUS, 5V_H2 and other protected 5 V / 3.3 V outputs.

**Datasheet relationship (approx):**

\[
I_\text{LIM}[\text{A}] \approx \dfrac{6800}{R_\text{LIM}[\Omega]}
\]

**Example design:**

- RLIM = 6.8 kΩ → I_LIM_nom ≈ 1.0 A

**Tolerances:**

- RLIM = ±1 %  
- Internal coefficient = ±5 %

**Worst-case ILIM:**

- I_LIM_min ≈ 1.0 · (1 − 0.05 − 0.01) ≈ 0.94 A  
- I_LIM_max ≈ 1.0 · (1 + 0.05 + 0.01) ≈ 1.06 A  

**Verdict:**

- Rail and connector sizing is based on I_LIM_max.  
- Short-circuits are limited to ≈1.06 A, protecting upstream DC/DC converters and wiring.

---

### 4.2 Shunt Current & Power

**Circuit:**

- Shunt resistor R_SHUNT in the 24 V path.  
- Current-sense amplifier with gain G.  
- Output into S32K116 ADC.

**Transfer function:**

\[
V_\text{OUT} = I_\text{BUS} \cdot R_\text{SHUNT} \cdot G
\]

**Tolerances:**

- R_SHUNT: ±3 %  
- G: ±1 %

**Effective gain spread:**

- G_eff,min ≈ 0.96 · G  
- G_eff,max ≈ 1.04 · G  

→ ≈ ±4 % error due to shunt + amplifier.

**Shunt power at I_MAX:**

\[
P_\text{SHUNT,max} = I_\text{MAX}^2 \cdot R_\text{SHUNT,max}
\]
\[
R_\text{SHUNT,max} = R_\text{nom}(1 + 0.03)
\]

Shunt is chosen so that P_SHUNT_max remains below its rated power with derating margin, considering worst-case I_MAX from DC/DC limits and AP22615 limits.

**Verdict:**

- Current measurement error ≈ ±5–6 % when ADC reference tolerance is included.  
- Shunt operates within its safe power envelope, protected by upstream current limits.

---

## 5. ADC & Measurement Paths (S32K116)

### 5.1 24 V Bus Voltage Monitor

**Circuit:**

- V_BUS → R_top → node → R_bot → GND  
- Node → S32K116 ADC input

Example values:

- R_top = 150 kΩ  
- R_bot = 10 kΩ  

Divider ratio:

\[
k = \dfrac{10k}{150k + 10k} = 0.0625
\]

Nominal values:

- At 24 V: V_ADC = 24 · 0.0625 = 1.50 V  
- At 30 V (surge example): V_ADC = 30 · 0.0625 = 1.875 V  

**Tolerances:**

- R_top, R_bot: ±1 % → ratio error ≈ ±2 %

At 30 V:

- V_ADC_min ≈ 30 · 0.0613 ≈ 1.84 V  
- V_ADC_max ≈ 30 · 0.0638 ≈ 1.91 V  

**Requirements:**

- ADC input range: 0–VREFH (≈3.3 V typical).  
- Abs max > 3.3 V (device-dependent), so 1.9 V is very safe.

**Verdict:**

- 24 V monitor never exceeds ADC limits, even with surge + tolerance.  
- Gain error ≈ ±2 %, acceptable for diagnostics and can be corrected in software if required.

---

### 5.2 24 V Bus Current Monitor

**Circuit:**

- R_SHUNT + amplifier with gain G → S32K116 ADC.

From Section 4.2:

- Front-end gain error ≈ ±4 %  
- ADC reference adds ≈ ±1–2 %  

**Total current measurement error:** ≈ ±5–6 %

**Verdict:**

- Adequate accuracy for safety thresholds, load monitoring and logging.  
- Output scaled so that at maximum expected I_BUS, V_ADC remains below ~2–2.5 V, safely under VREFH and abs max.

---

## 6. Logic Levels, Straps & Safety Defaults

### 6.1 3.3 V Rail Considerations

- 3.3 V main: 3.23–3.41 V  
- 3.3 V_STBY: 3.23–3.37 V  

Typical 3.3 V logic levels:

- VIH_min ≈ 2.0 V  
- VIL_max ≈ 0.8 V  

### 6.2 Example: Ethernet PHY Strap Pin

**Circuit:**

- Strap pin with pull-up (10 kΩ) to 3.3 V_STBY.  
- Optional pull-down / divider depending on the desired strap value.

Worst-case logic high:

- VDD_min = 3.23 V  
- Negligible leakage → V_HIGH_min ≈ 3.23 V  
- Margin: 3.23 V − 2.0 V = 1.23 V above VIH_min.

Worst-case logic low (0-strap):

- Designed so nominal < 0.3–0.4 V.  
- With ±1 % resistors and VDD_max = 3.37 V, V_LOW_max still well below 0.8 V.

### 6.3 Representative Summary Table

| Signal          | Function         | V_LOW,max (worst) | VIL,max | V_HIGH,min (worst) | VIH,min | Verdict |
|-----------------|------------------|-------------------|---------|--------------------|---------|---------|
| PHY_STRAP_x     | PHY addr/mode    | ~0.3–0.4 V        | 0.8 V   | ~3.23 V            | 2.0 V   | OK      |
| S32K_BOOT_CFGx  | MCU boot config  | ~0.3–0.4 V        | 0.8 V   | ~3.23 V            | 2.0 V   | OK      |
| Safety default lines (e.g. ROBOT_ENABLE) | Safe idle state | ~0.3–0.4 V or ~3.23 V depending on design | 0.8 V | 3.23 V | 2.0 V | OK |

**Verdict:**

- Even at tolerance extremes, logic 0 and 1 levels are far from VIL/VIH thresholds.  
- Mis-strapping or unsafe default states due solely to tolerances are highly unlikely.

---

## 7. Reset & Power-Up Timing (RC-Based)

### 7.1 S32K116 RESET_b RC Delay

**Circuit:**

- R_RESET = 10 kΩ (±1 %) from 3.3 V_STBY to RESET_b.  
- C_RESET = 100 nF (±10 %) from RESET_b to GND.

Approximation for time to reach ~0.6 · VDD:

\[
t \approx 0.92 \cdot R C
\]

Nominal:

- t_nom ≈ 0.92 · 10 kΩ · 100 nF ≈ 0.92 ms.

Worst-case spread:

- R: 9.9 kΩ … 10.1 kΩ  
- C: 90 nF … 110 nF  
- VDD variation has small effect on factor (0.92).

Result:

- t_min ≈ 0.7–0.8 ms  
- t_max ≈ 1.1–1.2 ms  

**Requirement:**

- Reset must stay low until 3.3 V_STBY has settled (tens to hundreds of microseconds).

**Verdict:**

- Even at t_min, reset is held long enough to guarantee stable supply before S32K116 starts.  
- Other RC-based delays (e.g., soft-enable of power switches) will have similar relative spreads and are not timing-critical at this stage.

---

## 8. Notes on Remaining Aspects

To keep this report focused while still demonstrating coverage of important areas:

1. **UVLO / EN thresholds:**  
   - EN and UVLO pins on regulators are either tied directly to rails or managed by the S32K116 (software-supervised). Exact min/max VIN_ON and VIN_OFF thresholds can be added in a dedicated power-sequencing WCA; current analysis assumes regulators operate within their published UVLO bounds whenever rails are declared “valid”.

2. **High-speed timing budgets (RGMII, CAN, etc.):**  
   - Clock ppm, PHY internal delays and PCB skew are not yet quantified here. They will be captured in a separate **interface timing & SI** document. At schematic stage, no tolerance-driven show-stoppers are identified.

3. **Protection & clamp levels:**  
   - TVS and surge components are chosen such that their clamping voltages remain below the voltage ratings of downstream components (24 V → clamp < component ratings; USB/Ethernet → standard ESD components). Detailed surge waveforms and clamping behaviour will be part of the EMC/protection analysis.

4. **Component stress / derating:**  
   - Capacitor voltage ratings and resistor power ratings exceed worst-case rail voltages and I²R levels derived from this stack-up. A full derating analysis will quantify margins per component type.

5. **Safety behaviour:**  
   - The worst-case voltages, currents and logic levels from this report will be used in FMEA to show:  
     - Jetson cannot be over-supplied (5 V stays below worst-case spec).  
     - ADC inputs are never over-driven.  
     - Short-circuits on camera/USB rails are limited to ILIM_max.  
     - Safety lines remain in their intended “safe” default state, even at tolerance extremes.

---

## 9. Overall Conclusion

At schematic stage, the **worst-case tolerance stack-up** for H1 shows that:

- All critical **DC rails** (VCC, 5 V, 3.3 V, 3.3 V_STBY, 3.3 V_ANA, 1.8 V) remain within or tighter than standard ±5 % windows, providing safe supplies for Jetson AGX Orin, S32K116 and peripherals.
- **Measurement and protection circuits** (24 V voltage divider, 24 V current monitor, AP22615 load switches) never exceed ADC or device absolute maximum ratings and exhibit bounded error bands suitable for diagnostics and safety logic.
- **Logic-level straps and safety defaults** show large margins to VIH/VIL, making mis-strapping due to tolerances very unlikely.
- **Reset and RC-based power-up delays** reliably keep the S32K116 in reset until rails are stable, across all tolerance combinations considered.

No critical violations are identified by this tolerance analysis. The results provide a strong foundation for the next steps: detailed **Worst-Case Analysis (WCA)**, **FMEA**, and **SI/timing** analysis in subsequent design phases.

---

## 10. References

1. XL4015 – Adjustable Step-Down (Buck) Converter Datasheet  
2. SIC431 – Synchronous Buck Regulator Datasheet  
3. NCP730 – 3.3 V LDO Regulator Datasheet  
4. TPS7A05 – 1.8 V LDO Regulator Datasheet  
5. AP22615 – Adjustable Current-Limited High-Side Power Switch Datasheet  
6. NXP S32K116 – Datasheet and Reference Manual  
7. NVIDIA Jetson AGX Orin – Module Datasheet and Carrier Board Design Guide  
8. KSZ9031 (or equivalent) – Gigabit Ethernet PHY Datasheet  
9. H1 Schematic Set – H1_Jetson_AGX_Orin_SOM and S32K116 Supervisor Schematics
