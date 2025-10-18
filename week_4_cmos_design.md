# 🌟 Week 4 – CMOS Circuit Design (sky130-style)

> **Repository:** [RISC-V-Tapeout-Journey](https://github.com/avinashjaiswal1598/RISC-V-Tapeout-Journey)  
> **Workshop Source:** [Sky130 CMOS Circuit Design Workshop](https://github.com/kunalg123/sky130CircuitDesignWorkshop/)  
> **Platform:** VSDIAT  
> **Author:** Avinash Jaiswal  

---

## 🧭 Table of Contents
1. [Introduction](#introduction)
2. [Day 1 – MOSFET Behavior & Id–Vds Characteristics](#day-1--mosfet-behavior--idvds-characteristics)
3. [Day 2 – Threshold Voltage Extraction & Velocity Saturation](#day-2--threshold-voltage-extraction--velocity-saturation)
4. [Day 3 – CMOS Switching Threshold & Dynamic Simulation](#day-3--cmos-switching-threshold--dynamic-simulation)
5. [Day 4 – Noise Margin & Robustness Analysis](#day-4--noise-margin--robustness-analysis)
6. [Day 5 – Power-Supply and Device Variation Studies](#day-5--power-supply-and-device-variation-studies)
7. [📊 Summary Tables](#📊-summary-tables)
8. [🔍 Observations and Analysis](#🔍-observations-and-analysis)
9. [🏁 Conclusion](#🏁-conclusion)
10. [📚 References](#📚-references)

---

## 🧩 Introduction
This documentation presents **Week 4: CMOS Circuit Design** using **Sky130 PDK** and **Ngspice**, conducted on the **VSDIAT platform**.  
The goal was to simulate and analyze MOSFET device behavior, CMOS inverter characteristics, timing performance, noise margins, and power-supply variations — connecting transistor-level design to timing analysis (STA) concepts.

---

## 🧪 Day 1 – MOSFET Behavior & Id–Vds Characteristics

**Objective:**  
Simulate NMOS drain current (Id) versus drain voltage (Vds) for various gate voltages (Vgs) to identify linear and saturation regions.

**SPICE Deck**
```spice
* NMOS Id-Vds Characterization
.include ./sky130_fd_pr__nfet_01v8.model.spice
Vgs gate 0 1.0
Vds drain 0 0
M1 drain gate 0 0 sky130_fd_pr__nfet_01v8 W=0.84u L=0.15u
.dc Vds 0 1.8 0.05
.plot dc I(M1)
.end
```

**Key Observations:**
- Identified **linear and saturation regions** for NMOS.
- Observed that **Id increases with Vgs** as expected.
- Early region of saturation shows minor deviations due to short-channel effects in Sky130 process.

---

## 🧪 Day 2 – Threshold Voltage Extraction & Velocity Saturation

**Objective:**  
Determine threshold voltage (Vth) of NMOS and study velocity saturation effects on Id–Vds curves.

**Methodology:**
- Extract Vth using **constant current method**.
- Plot Id vs Vds for multiple Vgs beyond threshold.
- Compare slope in linear region to assess mobility degradation.

**Observations:**
- Vth approximately matches Sky130 documentation.
- High-field conditions exhibit **velocity saturation**, flattening Id–Vds curves at higher Vds.

---

## 🧪 Day 3 – CMOS Switching Threshold & Dynamic Simulation

**Objective:**  
Analyze CMOS inverter switching behavior and determine switching threshold voltage (Vsw).

**Methodology:**
- Implement **inverter using NMOS and PMOS** devices.
- Apply **ramp input waveform**.
- Extract Vsw from Vout vs Vin plot.

**Observations:**
- Switching threshold is close to **Vdd/2**, indicating balanced sizing.
- Dynamic response shows finite rise/fall times due to parasitic capacitances.

---

## 🧪 Day 4 – Noise Margin & Robustness Analysis

**Objective:**  
Evaluate noise margins and robustness of CMOS inverter.

**Methodology:**
- Plot Vout vs Vin.
- Determine **NMH (Noise Margin High)** and **NML (Noise Margin Low)** from voltage transfer curve.
- Apply small supply variations and observe effect.

**Observations:**
- Noise margins satisfy **design requirements**.
- Circuit demonstrates robustness under small Vdd variations.

---

## 🧪 Day 5 – Power-Supply and Device Variation Studies

**Objective:**  
Study inverter performance under varying supply voltage and device size variations.

**Methodology:**
- Sweep Vdd from 1.5V to 1.8V.
- Simulate inverter with varying W/L ratios.

**Observations:**
- Rise/fall delays increase with reduced Vdd.
- Proper device sizing is critical to maintain switching threshold and timing performance.

---

## 📊 Summary Tables

| Day | Task | Key Observation |
|-----|------|----------------|
| 1   | NMOS Id–Vds | Linear/saturation regions identified; Id increases with Vgs |
| 2   | Vth extraction | Vth matches spec; velocity saturation observed |
| 3   | CMOS inverter | Vsw ~ Vdd/2; finite rise/fall times |
| 4   | Noise margin | NMH/NML acceptable; robust under Vdd variations |
| 5   | Power/device variations | Delay increases with low Vdd; sizing affects performance |

---

## 🔍 Observations and Analysis
- Sky130 MOSFET models accurately reflect **short-channel and velocity saturation effects**.
- CMOS inverter performance closely aligns with theoretical expectations.
- Proper device sizing is essential to maintain **timing and noise margins** under PVT variations.

---

## 🏁 Conclusion
Week 4 exercises successfully demonstrated **transistor-level design principles**, CMOS inverter behavior, and the impact of **device and supply variations**.  
The hands-on simulations with Sky130 PDK provided practical insights into MOSFET behavior and CMOS circuit robustness.

---

## 📚 References
1. [Sky130 PDK Documentation](https://skywater-pdk.readthedocs.io/)
2. [Sky130 CMOS Circuit Design Workshop](https://github.com/kunalg123/sky130CircuitDesignWorkshop/)
3. Ngspice Manual and Tutorials
4. RISC-V-Tapeout-Journey Repository

