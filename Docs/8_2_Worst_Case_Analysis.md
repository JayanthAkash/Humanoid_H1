## Worst-Case Analysis (WCA) Report

---

### 1. Purpose

This document captures the **Worst-Case Analysis (WCA)** performed on the H1 carrier board for the Jetson AGX Orin SOM.

Objective: show that, under **combined component tolerances, input variations, and environmental extremes**, all critical voltages and thresholds remain within safe limits defined by device specs and system requirements.

This is a **schematic-level** analysis (no PCB measurement data yet).

---

### 2. Scope

Analysed blocks (from `H1_Jetson_AGX_Orin_SOM.kicad_sch`):

- 24 V input → XL4015 pre-regulator → `VIN_SOM`
- 5 V and 3.3 V high-current bucks (SiC431)
- 3.3 V_STBY LDO (NCP730B)
- 1.8 V LDO (TPS7A0518)
- Buck **UVLO / enable thresholds**
- First-order **thermal WCA** on the 5 V buck

Other functions (LEDs, small logic pull-ups, etc.) are not WCA’d individually.

---

### 3. Assumptions & Corner Conditions

#### 3.1 Electrical and Environmental Corners

- **24 V bus at robot level**
  - Nominal: 24 V
  - Operating corner for H1: 20 V … 28 V

- **Ambient temperature inside torso**
  - Operating range for WCA: **–10 °C … +45 °C**

- **H1 power tree loads (design target)**
  - 5 V buck: up to **10 A continuous**, 12 A peak
  - 3.3 V buck: up to **8 A continuous** (assumed for logic, radios, etc.)
  - 3.3 V_STBY: ≤ 150 mA
  - 1.8 V LDO: ≤ 200 mA

- **Jetson AGX Orin VDD_IN range**
  - Carrier must provide **7–20 V** to VDD_IN
  - H1 therefore aims for ~18.6 V pre-regulated `VIN_SOM`.

#### 3.2 Component Tolerances (Unless Noted)

- All feedback / divider resistors: **±1 %**
- SiC431 feedback reference (VFB): **±1 % over –40…+125 °C**
- NCP730B LDO output accuracy: **±1 %**
- TPS7A0518 1.8 V LDO output accuracy: **±3 % (max)**

---

### 4. Methodology

For each node / parameter:

1. Use the design equations in the schematic comments (for SiC431 and UVLO) and the regulators’ datasheets.
2. Apply **absolute worst-case** combinations of tolerances (not RMS):
   - For **minimum** output, drive all tolerances in the direction that reduces it.
   - For **maximum** output, drive all in the direction that increases it.
3. Compute `Value_min` and `Value_max`.
4. Compare with:
   - Device absolute min / max and
   - Functional system spec (USB, Jetson VDD_IN, etc.).
5. Record **margin** and **status (PASS / RISK)**.

---

### 5. WCA Results – Main Power Rails (SiC431 Bucks & Pre-reg)

#### 5.1 Design equations from schematic

- **Buck output equation**
  \[
  V_{OUT} = V_{REF} \cdot \frac{R_{FB\_H} + R_{FB\_L}}{R_{FB\_L}}
  \]
  with \(V_{REF} = 0.6\; \text{V}\) (SIC431 feedback reference).

- **5 V buck feedback network**
  - \(R_{FB\_H} = 73.2\ \text{k}\Omega\)
  - \(R_{FB\_L} = 10\ \text{k}\Omega\)
  - Comment in schematic: \(V_{OUT} = 0.6\cdot(73k2+10k)/10k = 4.992\ \text{V}\)

- **3.3 V buck feedback network**
  - \(R_{FB\_H} = 45.3\ \text{k}\Omega\)
  - \(R_{FB\_L} = 10\ \text{k}\Omega\)
  - Comment in schematic: \(V_{OUT} = 0.6\cdot(45k3+10k)/10k = 3.318\ \text{V}\)

- **Pre-regulator (XL4015 style)**
  - Comment in schematic:
    \[
    V_{OUT} = 1.25\cdot(1 + R_6/R_7), \quad V_{OUT,nom} \approx 18.6\ \text{V}
    \]
  - This implies \(R_6/R_7 \approx 13.88\).

#### 5.2 Rail-level WCA summary

**Assumptions for WCA:**

- SiC431 feedback reference tolerance: ±1 %
- XL4015 (pre-reg) reference: assumed ±1 % (typical for 1.25 V buck regulators).
- All feedback resistors: ±1 %.

| ID | Rail / Node       | Equation & Resistors                     | Spec (Min…Max)              | WCA Result (Min…Max) | Margin vs spec                           | Status |
|----|-------------------|------------------------------------------|-----------------------------|----------------------|------------------------------------------|--------|
| P1 | 5 V buck output   | SiC431, R_H = 73.2k, R_L = 10k          | 4.75…5.25 V (USB 5 V ±5 %)  | 4.856…5.132 V        | ≥106 mV from each limit                  | PASS   |
| P2 | 3.3 V buck output | SiC431, R_H = 45.3k, R_L = 10k          | 3.135…3.465 V (3.3 V ±5 %)  | 3.232…3.407 V        | ≥97 mV from each limit                   | PASS   |
| P3 | VIN_SOM pre-reg   | XL4015, Vref = 1.25 V, R_6/R_7 ≈ 13.88  | 7…20 V (Jetson VDD_IN range)| 18.074…19.140 V      | ≥11.074 V above min, 0.860 V below max   | PASS   |
| P4 | 3.3 V_STBY LDO    | NCP730B fixed 3.3 V, ±1 %               | 3.135…3.465 V               | 3.267…3.333 V        | Far inside ±5 % window                   | PASS   |
| P5 | 1.8 V LDO         | TPS7A0518 fixed 1.8 V, ±3 % max         | 1.71…1.89 V                 | 1.746…1.854 V        | Far inside ±5 % window                   | PASS   |

#### 5.3 Worked example – 5 V buck (P1)

Inputs:

- Vref = 0.6 V ±1 %
- R_H = 73.2 kΩ ±1 %
- R_L = 10.0 kΩ ±1 %

Worst-case **minimum** (all tolerances toward lower Vout):

- Vref_min = 0.6·0.99 = 0.594 V
- R_H_min = 73.2k·0.99 = 72.468 kΩ
- R_L_max = 10k·1.01 = 10.10 kΩ

\[
V_{OUT,min} = 0.594\cdot\frac{72.468k + 10.10k}{10.10k} = 4.856\ \text{V}
\]

Worst-case **maximum** (all toward higher Vout):

- Vref_max = 0.606 V
- R_H_max = 73.2k·1.01 = 73.932 kΩ
- R_L_min = 10k·0.99 = 9.90 kΩ

\[
V_{OUT,max} = 0.606\cdot\frac{73.932k + 9.90k}{9.90k} = 5.132\ \text{V}
\]

Comparison with spec:

- Spec window: 4.75…5.25 V
- WCA result: 4.856…5.132 V
- Minimum margin to any limit: **106 mV** → **PASS**.

(Similar calculations were done for the 3.3 V buck and pre-reg.)

---

### 6. WCA – UVLO / Enable Thresholds

Using the standard SiC431 formula for UVLO:

\[
R_{FB\_H} = \frac{R_{FB\_L}(V_{TH} - 0.6)}{0.6}
\]

For an intended VIN turn-on threshold of 8.4 V,
\(R_{FB\_H}/R_{FB\_L} = (8.4/0.6) - 1 = 13\).

Assuming:

- Vref (FB) = 0.6 V ±1 %
- R_H and R_L = ±1 %

| ID | Function / Node        | Nominal VIN_ON | WCA VIN_ON (Min…Max) | Comment                         | Status |
|----|------------------------|---------------:|----------------------|----------------------------------|--------|
| U1 | Pre-reg UVLO (24→VIN_SOM) | 8.4 V       | 8.163…8.643 V        | Keeps converter off below ~8.2 V | PASS   |

Interpretation:

- Even with tolerances, the pre-reg will **not** enable below ~8.16 V (well above brownout levels for a 24 V robot bus).
- Upper end 8.64 V is acceptable; gives clean start without chatter.

---

### 7. WCA – LDO Rails (3.3 V_STBY and 1.8 V)

These are fixed-output parts; WCA is straightforward:

#### 7.1 3.3 V_STBY (NCP730B)

- Output accuracy: ±1 % over temp
- Nominal: 3.300 V

\[
V_{out,min} = 3.3 \cdot 0.99 = 3.267\ \text{V}
\]
\[
V_{out,max} = 3.3 \cdot 1.01 = 3.333\ \text{V}
\]

- System spec for 3.3 V logic: 3.135…3.465 V (±5 %).
- WCA result sits well inside → **large margin, PASS**.

#### 7.2 1.8 V LDO (TPS7A0518)

- Output accuracy: ±3 % max
- Nominal: 1.800 V

\[
V_{out,min} = 1.8 \cdot 0.97 = 1.746\ \text{V}
\]
\[
V_{out,max} = 1.8 \cdot 1.03 = 1.854\ \text{V}
\]

- Common 1.8 V logic range: 1.71…1.89 V (±5 %).
- Again, well inside → **PASS**.

---

### 8. Thermal WCA – SiC431 5 V Buck

From SiC431 datasheet:

- θ_JA ≈ **16 °C/W** in reference layout
- Maximum junction temperature rating: **150 °C** {index=11}

#### 8.1 Continuous 10 A load case (design target)

Assumptions:

- VOUT = 5.0 V
- IOUT = 10 A → P_OUT = 50 W
- Worst-case efficiency at this load: ~94 % (assumed from datasheet curves).

Then:

- P_IN = 50 / 0.94 = 53.19 W
- P_LOSS = P_IN – P_OUT = **3.19 W**

With ambient = 45 °C:

\[
T_J = T_A + P_{LOSS}\cdot\theta_{JA} = 45 + 3.19\cdot16 = 96\ ^\circ\text{C}
\]

- Margin to 150 °C abs max: **54 °C**
- Margin to 125 °C recommended operating Tj: **29 °C**
→ **Thermally comfortable – PASS** (assuming PCB copper similar to reference).

#### 8.2 Peak 12 A load case (short duration)

Assumptions:

- VOUT = 5.0 V, IOUT = 12 A → P_OUT = 60 W
- Worst-case efficiency at this load: ~92 %

Then:

- P_IN = 60 / 0.92 = 65.22 W
- P_LOSS ≈ **5.22 W**

\[
T_J = 45 + 5.22\cdot16 \approx 128.5\ ^\circ\text{C}
\]

- Below 150 °C abs max but **slightly above** 125 °C recommended operating junction.

> **Conclusion:**
> 10 A continuous, 12 A short burst is realistic.
> For **12 A continuous** operation at +45 °C ambient, it is
> close to the safe limit and should:
> - Improve cooling (larger copper area, airflow, or heatsinking).

---

### 9. Summary

- **5 V buck:** WCA 4.856…5.132 V – meets USB/logic requirements with good margin.
- **3.3 V buck:** WCA 3.232…3.407 V – comfortably within ±5 % window.
- **3.3 V_STBY & 1.8 V LDOs:** accuracy of the LDOs gives excellent margin.
- **UVLO for pre-reg:** 8.16…8.64 V – ensures clean start and avoids low-VIN operation.
- **Thermal:** 5 V buck is safe for 10 A continuous at 45 °C ambient; 12 A peak is acceptable but continuous 12 A is close to Tj limits.

### 10. References

1. SiC431 – Vishay, “SiC431 3 V to 24 V Input, 24 A microBUCK DC/DC Converter”
   https://www.vishay.com/docs/74589/sic431.pdf

2. NCP730B – onsemi, “NCP730 150 mA LDO Regulator”
   Product page: https://www.onsemi.com/products/power-management/linear-regulators-ldo/ncp730
   Datasheet:    https://www.onsemi.com/download/data-sheet/pdf/ncp730-d.pdf

3. TPS7A0518 (TPS7A05 family) – Texas Instruments, “TPS7A05 Low IQ LDO”
   Product page: https://www.ti.com/product/TPS7A05
   Datasheet:    https://www.ti.com/lit/ds/symlink/tps7a05.pdf

4. AP22615 / AP22815 – Diodes Inc., “AP22815/615 Power Distribution Switches”
   Product page: https://www.diodes.com/part/view/AP22615
   Datasheet:    https://www.diodes.com/assets/Datasheets/AP22815_615.pdf

5. Jetson AGX Orin – “Jetson Module Adaptation and Bring-Up: Checklists”
   General checklist: https://docs.nvidia.com/jetson/archives/r35.1/DeveloperGuide/text/HR/JetsonModuleAdaptationAndBringUp/Checklists.html
   AGX Orin series:   https://docs.nvidia.com/jetson/archives/r35.6.1/DeveloperGuide/HR/JetsonModuleAdaptationAndBringUp/JetsonAgxOrinSeries.html


