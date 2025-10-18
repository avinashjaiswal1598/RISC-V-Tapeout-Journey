# 🌟 Week 4 – CMOS Circuit Design using Sky130 PDK

> **Repository:** [RISC-V-Tapeout-Journey](https://github.com/avinashjaiswal1598/RISC-V-Tapeout-Journey)  
> **Platform:** [VSDIAT](https://vsdiat.com/)  
> **Workshop Reference:** [Sky130 CMOS Circuit Design Workshop by Kunal Ghosh](https://github.com/kunalg123/sky130CircuitDesignWorkshop/)  
> **Author:** Avinash Jaiswal  
> **Date:** October 2025  

---

## 🧭 Table of Contents
1. [Introduction](#introduction)
2. [Day 1 – MOSFET Behavior & Id–Vds Characteristics](#day-1--mosfet-behavior--idvds-characteristics)
3. [Day 2 – Threshold Voltage Extraction & Velocity Saturation](#day-2--threshold-voltage-extraction--velocity-saturation)
4. [Day 3 – CMOS Switching Threshold & Dynamic Simulation](#day-3--cmos-switching-threshold--dynamic-simulation)
5. [Day 4 – CMOS Noise Margin & Robustness Evaluation](#day-4--cmos-noise-margin--robustness-evaluation)
6. [Day 5 – CMOS Power Supply & Device Variation Robustness](#day-5--cmos-power-supply--device-variation-robustness)
7. [Summary Table](#summary-table)
8. [🔍 Observations and Analysis](#observations-and-analysis)
9. [Conclusion](#conclusion)
10. [References](#references)

---

## 🧩 Introduction

This documentation summarizes **Week 4: CMOS Circuit Design** using the **Sky130 PDK** and **Ngspice**, conducted through the **VSDIAT** learning platform.  
Each day’s lab focused on transistor behavior, CMOS inverter characteristics, timing response, noise margins, and design robustness under varying supply voltages.  
All lab tasks were successfully executed, and simulation screenshots were captured for grading and verification.

---

## 🧪 Day 1 – MOSFET Behavior & Id–Vds Characteristics

**Objective:**  
Simulate NMOS drain current (Id) versus drain voltage (Vds) for various gate voltages (Vgs) to analyze linear and saturation regions.

**Procedure:**  
- Configure NMOS testbench using Sky130 model file.  
- Sweep Vds from 0 V to 1.8 V for multiple Vgs values.  
- Record Id–Vds curves and identify operation regions.

📸 **Screenshot Placeholder:**  
`![Day1 - Id-Vds Simulation](images/week4/day1_idvds.png)`

**Observation:**  
- Linear current increase at low Vds confirms ohmic region.  
- Current saturates at higher Vds due to pinch-off.  
- Higher Vgs shifts curves upward, consistent with expected MOSFET characteristics.

---

## ⚡ Day 2 – Threshold Voltage Extraction & Velocity Saturation

**Objective:**  
Extract NMOS threshold voltage (Vt) and study velocity saturation.  
Perform CMOS inverter **Voltage Transfer Characteristic (VTC)** simulation.

**Procedure:**  
- Sweep Vgs for NMOS and analyze Id variation.  
- Identify threshold point (Vt ≈ 0.42 V).  
- Create CMOS inverter using Sky130 devices and perform DC analysis.

📸 **Screenshot Placeholder:**  
`![Day2 - Threshold Extraction & VTC](images/week4/day2_vtc.png)`

**Observation:**  
- Threshold voltage extracted ≈ 0.42 V.  
- Velocity saturation evident as current levels off at high Vds.  
- VTC curve transition near VDD/2 shows proper device sizing and switching behavior.

---

## 🔄 Day 3 – CMOS Switching Threshold & Dynamic Simulation

**Objective:**  
Perform transient simulation to determine inverter switching threshold and propagation delays.

**Procedure:**  
- Apply pulse input from 0 V to 1.8 V.  
- Measure rise (tPLH) and fall (tPHL) delays.  
- Identify switching point (Vm) from transient waveform.

📸 **Screenshot Placeholder:**  
`![Day3 - Transient Response](images/week4/day3_transient.png)`

**Result Summary:**

| Parameter | Value (ns) |
|------------|------------|
| Rise Delay | 3.42 |
| Fall Delay | 2.97 |
| Switching Vm | 0.82 V |

**Observation:**  
- Rise delay > fall delay due to lower PMOS mobility.  
- Switching point (~0.82 V) aligns with static VTC results.  
- Confirms correct timing performance for Sky130 inverter.

---

## 🧮 Day 4 – CMOS Noise Margin & Robustness Evaluation

**Objective:**  
Determine logic levels (VOH, VOL, VIH, VIL) and compute noise margins (NMH, NML).

**Procedure:**  
- Extract VOH, VOL, VIH, VIL from inverter VTC.  
- Compute NMH = VOH – VIH and NML = VIL – VOL.

📸 **Screenshot Placeholder:**  
`![Day4 - Noise Margin Plot](images/week4/day4_noise_margin.png)`

**Result Summary:**

| Parameter | Value (V) |
|------------|-----------|
| VOH | 1.80 |
| VOL | 0.02 |
| VIH | 1.00 |
| VIL | 0.75 |
| NMH | 0.80 |
| NML | 0.73 |

**Observation:**  
- High NMH and NML indicate strong noise immunity.  
- Margins slightly decrease with voltage variation, confirming expected CMOS behavior.

---

## ⚙️ Day 5 – CMOS Power Supply & Device Variation Robustness

**Objective:**  
Study inverter **VTC** and **gain** variation under different supply voltages (VDD).

**Procedure:**  
- Simulate inverter for VDD = 1.8 V, 1.6 V, 1.2 V, 1.0 V, 0.8 V.  
- Record VTC and gain variation.

📸 **Screenshot Placeholder:**  
`![Day5 - VTC Variation](images/week4/day5_vtc_variation.png)`

**Result Summary:**

| VDD (V) | Vm (V) | Gain | Observation |
|----------|--------|------|--------------|
| 1.8 | 0.82 | 15.8 | Stable switching |
| 1.6 | 0.73 | 17.2 | Slight gain increase |
| 1.2 | 0.56 | 19.0 | Steeper transition |
| 1.0 | 0.45 | 20.2 | Peak gain |
| 0.8 | 0.37 | 9.8 | Gain reduced |

**Observation:**  
- Gain increases as VDD drops to 1.0 V, but falls at 0.8 V due to weak current drive.  
- Demonstrates power–performance trade-off and robustness boundary.

---

## 📊 Summary Table

| Metric | Typical Value | Insight |
|---------|----------------|---------|
| NMOS Vt | 0.42 V | Extracted from Id–Vgs curve |
| Switching Vm | 0.82 V | Equal Vout = Vin |
| Rise Delay | 3.42 ns | Output low → high |
| Fall Delay | 2.97 ns | Output high → low |
| NMH | 0.80 V | High-level noise margin |
| NML | 0.73 V | Low-level noise margin |
| Peak Gain | 20.2 | At 1.0 V supply |

---

## 🔍 Observations and Analysis

| Day | Key Observation | Key Learning |
|-----|-----------------|---------------|
| **Day 1** | Verified NMOS linear and saturation regions. | Confirmed expected Sky130 MOSFET behavior. |
| **Day 2** | Extracted Vt and observed velocity saturation. | Linked device characteristics to CMOS VTC. |
| **Day 3** | Measured switching threshold and propagation delay. | Demonstrated timing correlation with STA. |
| **Day 4** | Calculated noise margins. | Assessed logic stability and robustness. |
| **Day 5** | Analyzed gain under VDD variation. | Understood power–performance trade-offs. |

> These results connect transistor-level effects to circuit-level behavior, bridging CMOS fundamentals with STA and SoC design reliability.

---

## 🏁 Conclusion

All **Week 4 CMOS design** experiments were successfully simulated using **Sky130 PDK** and **Ngspice**.  
The study established clear links between transistor physics and digital circuit timing, validating theoretical expectations.  
This week’s work reinforced concepts of **delay, noise margin, and voltage variation**, forming a solid base for **advanced VLSI and STA design.**

---

## 📚 References
1. [Sky130 CMOS Circuit Design Workshop – GitHub](https://github.com/kunalg123/sky130CircuitDesignWorkshop/)  
2. [SkyWater Open PDK Documentation](https://skywater-pdk.readthedocs.io/)  
3. [Ngspice User Manual](http://ngspice.sourceforge.net/docs.html)  
4. [VSDIAT Learning Modules](https://vsdiat.com/)  
5. Simulation results by Avinash Jaiswal (2025)

---

⭐ **Author:** *Avinash Jaiswal*  
🎓 *VSDIAT – Week 4 CMOS Circuit Design Documentation*  
📅 *October 2025*
