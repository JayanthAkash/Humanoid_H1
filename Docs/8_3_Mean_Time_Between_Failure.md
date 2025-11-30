## Preliminary MTBF / MTTF Result for H1 (Vendor-Based FIT)

### 1. Method and Data Sources

For this revision, the MTBF prediction for H1 is based on:

1. **BOM-based parts-count**
   Using `H1_Jetson_AGX_Orin_SOM.csv`, all components on the H1 carrier PCB were
   grouped by type:

   - Resistors
   - Capacitors
   - Inductors / ferrite beads
   - LEDs
   - Diodes & ESD / TVS devices
   - MOSFETs / transistors
   - Digital ICs (logic, level shifters, GPIO expanders, muxes, etc.)
   - Analog / power ICs (DC-DC, LDOs, power switches, transceivers)
   - Oscillators (crystal & MEMS oscillator)
   - Connectors (AGX connector, M.2 sockets, USB-C, RJ45, FFCs, etc.)
   - Mechanical / test points (ignored for MTBF)

   From the BOM we obtained the following **population per category**:

   | Category           | Qty |
   |--------------------|----:|
   | Resistors          | 147 |
   | Capacitors         | 117 |
   | Inductors / beads  |   3 |
   | LEDs               |   7 |
   | Diodes / ESD       |  19 |
   | MOSFETs / trans.   |  13 |
   | Digital ICs        |  14 |
   | Analog / power ICs |  13 |
   | Oscillators        |   2 |
   | Connectors         |  21 |
   | Mech / testpoints  |  27 |

2. **Reference FIT rates from vendor documents**

   Exact FIT values for every individual MPN are generally **not published in the
   datasheet**; they are usually provided via separate reliability or functional
   safety documents, or on request. Instead, we rely on **category-level reference
   FIT tables** from major vendors:

   - **TI application report SLOA294A** – “Understanding Functional Safety FIT
     Base Failure Rate Estimates per IEC 62380 and SN 29500”
     This document provides **reference FIT rates** for integrated circuits,
     discrete semiconductors, and passive components by type and complexity. :contentReference[oaicite:1]{index=1}

   - TI Functional Safety FIT notes for logic devices (e.g. **SN74LVC3G07-Q1**
     8 FIT at 55 °C as a typical CMOS logic reference). :contentReference[oaicite:2]{index=2}

   - **Murata MLCC FAQ** – confirms that failure rate / FIT data for ceramic
     capacitors can be provided and that ceramic caps generally exhibit very
     low FIT in benign conditions.

   - **Würth Elektronik connector FIT guide** – provides temperature-dependent
     FIT data for connectors; typical values at 40–60 °C lie in the order of
     tens of FIT per mated pair.

   Based on those references (and staying conservative for a ground-benign,
   ~40 °C environment), we assign the following **per-device FIT**:

   | Category           | Assumed FIT / device (failures / 10⁹ h) | Justification (summary) |
   |--------------------|-----------------------------------------:|--------------------------|
   | Resistors          | 0.05 FIT                                 | Passive, low-stress; in line with SN29500/IEC style tables. :contentReference[oaicite:5]{index=5} |
   | Capacitors (MLCC)  | 0.5 FIT                                  | MLCCs have low FIT in benign env.; value chosen conservatively above Murata silicon-cap examples. :contentReference[oaicite:6]{index=6} |
   | Inductors / beads  | 1 FIT                                    | Passive magnetic parts; simple construction. :contentReference[oaicite:7]{index=7} |
   | LEDs               | 2 FIT                                    | Simple opto-semiconductors, moderate FIT. |
   | Diodes / ESD / TVS | 5 FIT                                    | Discrete semis per TI/IEC reference ranges. :contentReference[oaicite:8]{index=8} |
   | MOSFETs / trans.   | 5 FIT                                    | Discrete MOSFETs / BJTs as above. :contentReference[oaicite:9]{index=9} |
   | Digital ICs        | 10 FIT                                   | CMOS logic / small digital ICs around ~5–10 FIT typical; we choose 10 FIT as conservative. :contentReference[oaicite:10]{index=10} |
   | Analog / power ICs | 15 FIT                                   | Power / analog devices typically somewhat higher FIT than simple logic (more area & stress). :contentReference[oaicite:11]{index=11} |
   | Oscillators        | 20 FIT                                   | Crystals / oscillators often dominate field failures; assigned higher value. :contentReference[oaicite:12]{index=12} |
   | Connectors         | 15 FIT                                   | Based on Würth FIT curves at moderate temperature, using a mid-range value for mated pairs.  |
   | Mech / testpoints  | 0 FIT                                    | Not counted as electronic failure sources at this stage. |

3. **Calculate category FIT and total board FIT**

   For each category:

   \[
   \text{FIT}_{\text{category}} = \text{FIT}_{\text{per device}} \times \text{Qty}
   \]

   This gives:

   | Category           | Qty | FIT / device | Category FIT |
   |--------------------|----:|-------------:|-------------:|
   | Resistors          | 147 |        0.05  |        7.35  |
   | Capacitors         | 117 |        0.5   |       58.50  |
   | Inductors / beads  |   3 |        1.0   |        3.00  |
   | LEDs               |   7 |        2.0   |       14.00  |
   | Diodes / ESD       |  19 |        5.0   |       95.00  |
   | MOSFETs / trans.   |  13 |        5.0   |       65.00  |
   | Digital ICs        |  14 |       10.0   |      140.00  |
   | Analog / power ICs |  13 |       15.0   |      195.00  |
   | Oscillators        |   2 |       20.0   |       40.00  |
   | Connectors         |  21 |       15.0   |      315.00  |
   | **Total**          |     |              |  **932.85**  |

   So the **H1 carrier-board failure rate** is:

   \[
   \text{FIT}_{\text{H1,carrier}} = 932.85\ \text{FIT}
   \]
   \[
   \lambda_{\text{H1}} = \frac{932.85}{10^9}\ \text{failures/hour}
   \]

4. **Compute MTBF / MTTF**

   For a constant failure rate, **MTBF ≈ MTTF = 1/λ**:

   \[
   \text{MTBF}_{\text{H1,carrier}} =
   \frac{10^9}{932.85} \approx 1.07\times 10^6\ \text{hours}
   \]

   So, rounded:

   - **MTBF ≈ 1,070,000 hours**
   - ≈ **122 years** if operated 24 h/day
   - ≈ **366 years of “calendar usage”** if operated 8 h/day, 365 days/year

   This is clearly **far above** the intended product life (e.g. 10 years), which is what we want: the electronics are not expected to be the limiting factor under the assumed environment.

> **Important**
> This MTBF is for the **H1 carrier PCB only** (components in `H1_Jetson_AGX_Orin_SOM.csv`).
> The **Jetson AGX Orin SOM, modem modules, and other controllers** are treated as
> separate field-replaceable units (FRUs) with their **own vendor-supplied FIT /
> MTBF** values and are **not** expanded into individual internal components here.

---

### 2. Category Contribution and Design Focus

Percentage contribution to total FIT:

- Analog / power ICs: 195 / 932.85 ≈ **21 %**
- Digital ICs: 140 / 932.85 ≈ **15 %**
- Connectors: 315 / 932.85 ≈ **34 %**
- Protection (diodes, MOSFETs): (95 + 65) / 932.85 ≈ **17 %**
- Passives (R, C, L) + LEDs + osc: remaining ≈ **13 %**

This confirms that, in line with industry experience:

- **Connectors** and **active ICs (especially power devices)** dominate the
  predicted failure rate.
- Large populations of **resistors and capacitors** do *not* dominate FIT in a
  benign environment when properly derated.

In practice this means:

- Our focus on **WCA + derating** for the power tree and
  connector ratings is aligned with reliability impact.
- For any further reliability improvement, we would first look at:
  - Derating or redundancy on **critical power ICs**, and
  - Mechanical / environmental robustness of **connectors** (strain relief,
    locking mechanisms, ingress protection).

---

### 3. Example Mission Profile (10 Years)

For a nominal mission:

- 10 years
- 8 h/day operation
- Indoor, 0…45 °C (as assumed in WCA)

Mission time:

\[
t = 10\ \text{years} \times 8\ \text{h/day} \times 365\ \text{days}
 \approx 29{,}200\ \text{hours}
\]

With \(\lambda_{\text{H1}} = 932.85 / 10^9 \approx 33\times 10^{-7}\ \text{h}^{-1}\):

\[
\lambda t \approx 33\times 10^{-7}\times 29200 \approx 0.0273
\]
\[
R(t) = e^{-\lambda t} \approx e^{-0.0273} \approx 0.973
\]

So, for **one H1 carrier board**:

- Probability of **surviving 10 years** at 8 h/day ≈ **97.3 %**
- Probability of at least one failure in that period ≈ **2.7 %**

This is a **first-order, vendor-based estimate** and will be refined with:

- Actual junction temperatures from post-layout thermal analysis,
- Vendor-specific FIT data for the Jetson SOM and other modules, and
- Any field data collected from prototype and early production units.

---

### 4. Limitations and Next Steps

- The per-device FIT rates are **category-level estimates derived from vendor
  reference documents**, *not* exact values for each individual MPN in the BOM.
- Some vendors (e.g. Murata for MLCC, major IC vendors for automotive parts)
  explicitly state FIT / MTBF data is available **on request**, not directly in the
  public datasheet.



1. TI – Understanding Functional Safety FIT Base Failure Rate Estimates
   Title: “Understanding Functional Safety FIT Base Failure Rate Estimates per IEC 62380 and SN 29500 (Rev. A)”
   Link:  https://www.ti.com/lit/SLOA294A

2. TI – Example Functional Safety / FIT application (CAN transceiver)
   Title: “TCAN1042x-Q1 Functional Safety, FIT Rate, Failure Mode Distribution and Diagnostic Coverage”
   Link:  https://www.ti.com/lit/pdf/slla502

3. Murata – MLCC Reliability / FIT and MTBF Data
   FAQ: “Is data from reliability tests on ceramic capacitors available?”
   Link: https://www.murata.com/en-sg/support/faqs/capacitor/ceramiccapacitor/qlty/0020

   FAQ: “Do you have data on failure rate, FIT value, MTTF and MTBF?”
   Link: https://www.murata.com/en-sg/support/faqs/capacitor/ceramiccapacitor/qlty/0022

   General capacitor reliability / selection:
   Link: https://www.murata.com/en-sg/support/faqs/capacitor

4. TDK – FIT and MTTF / MTBF for MLCCs
   FAQ: “FIT and MTTF / MTBF (7): How does TDK calculate Failure Rate of MLCCs (Multilayer Ceramic Capacitors)?”
   Link: https://product.tdk.com/en/contact/faq/capacitors-0107.html

5. Würth Elektronik – Connector Reliability Context (used as reference for connector FIT assumptions)
   Connectors overview:
   Link: https://www.we-online.com/en/components/products/em/connectors

   “Trilogy of Connectors” reference guide:
   Link: https://www.we-online.com/en/components/products/TRILOGY_OF_CONNECTORS_ENGLISH

6. Siemens SN 29500 – Standard for Failure Rates
   Overview / description:
   Link: https://www.isograph.com/software/reliability-workbench/prediction-software/siemens-sn-29500/

   Alternate reference/summary:
   Link: https://aldservice.com/SN-29500-Siemens.html

7. General SN 29500 / FIT usage examples
   Paper: “Reliability prediction for automotive electronics” (discusses SN 29500 failure rate use)
   Link: https://dialnet.unirioja.es/descarga/articulo/9722426.pdf

   Blog walkthrough: “Your Step-by-Step Guide for Performance Level Calculations of Safety-Critical Electronics (How to calculate FIT using SN29500)”
   Link: https://oxeltech.de/your-step-by-step-guide-for-performance-level-calculations-of-safety-critical-electronics/

8. Murata Silicon Capacitor Reliability Example (very low FIT baseline)
   Link: https://www.murata.com/products/capacitor/siliconcapacitors/overview/passivecomponentnetwork/reliability
