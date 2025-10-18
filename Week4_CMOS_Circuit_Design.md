# 🌟 Week 4 – CMOS Circuit Design using Sky130 PDK

> **Repository:** [RISC-V-Tapeout-Journey](https://github.com/avinashjaiswal1598/RISC-V-Tapeout-Journey)  
> **Platform:** [VSDIAT](https://vsdiat.com/)  
> **Workshop Reference:** [Sky130 CMOS Circuit Design Workshop by Kunal Ghosh](https://github.com/kunalg123/sky130CircuitDesignWorkshop/)  
> **Author:** Avinash Jaiswal   

---

## 🧭 Table of Contents
1. [Introduction](#introduction)
2. [Day 1 – MOSFET Behavior & Id–Vds Characteristics](#day-1--mosfet-behavior--idvds-characteristics)
3. [Day 2 – Threshold Voltage Extraction & Velocity Saturation](#day-2--threshold-voltage-extraction--velocity-saturation)
4. [Day 3 – CMOS Switching Threshold & Dynamic Simulation](#day-3--cmos-switching-threshold--dynamic-simulation)
5. [Day 4 – CMOS Noise Margin & Robustness Evaluation](#day-4--cmos-noise-margin--robustness-evaluation)
6. [Day 5 – CMOS Power Supply & Device Variation Robustness](#day-5--cmos-power-supply--device-variation-robustness)
7. [Summary Table](#summary-table)
8. [Observations and Analysis](#observations-and-analysis)
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

```spicedeck

*Model Description
.param temp=27


*Including sky130 library files
.lib "sky130_fd_pr/models/sky130.lib.spice" tt


*Netlist Description



XM1 Vdd n1 0 0 sky130_fd_pr__nfet_01v8 w=5 l=2

R1 n1 in 55

Vdd vdd 0 1.8V
Vin in 0 1.8V

*simulation commands

.op
.dc Vdd 0 1.8 0.1 Vin 0 1.8 0.2

.control

run
display
setplot dc1
.endc

.end
```  


<img width="1280" height="800" alt="IdVsVds" src="https://github.com/user-attachments/assets/d18c8f33-74e7-4938-9991-1635d6e92294" />


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
- Identify threshold point (Vt ≈ 0.76 V).  
- Create CMOS inverter using Sky130 devices and perform DC analysis.

```spicedeck
*Model Description
.param temp=27


*Including sky130 library files
.lib "sky130_fd_pr/models/sky130.lib.spice" tt


*Netlist Description

XM1 Vdd n1 0 0 sky130_fd_pr__nfet_01v8 w=0.39 l=0.15

R1 n1 in 55

Vdd vdd 0 1.8V
Vin in 0 1.8V

*simulation commands

.op
.dc Vin 0 1.8 0.1

.control

run
display
setplot dc1
.endc

.end

```
<img width="1123" height="639" alt="Screenshot from 2025-10-19 00-40-23" src="https://github.com/user-attachments/assets/b8463767-2fc7-49b2-b2c5-2380a9600106" />



**Observation:**  
- Threshold voltage extracted ≈ 0.76 V.  
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

### CMOS VTC

```spicedeck
*Model Description
.param temp=27


*Including sky130 library files
.lib "sky130_fd_pr/models/sky130.lib.spice" tt


*Netlist Description


XM1 out in vdd vdd sky130_fd_pr__pfet_01v8 w=0.84 l=0.15
XM2 out in 0 0 sky130_fd_pr__nfet_01v8 w=0.36 l=0.15


Cload out 0 50fF

Vdd vdd 0 1.8V
Vin in 0 1.8V

*simulation commands

.op

.dc Vin 0 1.8 0.01

.control
run
setplot dc1
display
.endc

.end
```

<img width="1213" height="615" alt="cmos_vtc" src="https://github.com/user-attachments/assets/3686c5d0-544b-483e-808b-557bed2ec2e3" />

### Switching Threshold Voltage STV
for stv zoom in the area where Vin = Vout.

<img width="1213" height="615" alt="cmos_vtc_stv" src="https://github.com/user-attachments/assets/a897e883-e541-4dca-8146-6221a2284a9c" />

### CMOS Transiant Analysis 

```spicedeck
*Model Description
.param temp=27


*Including sky130 library files
.lib "sky130_fd_pr/models/sky130.lib.spice" tt


*Netlist Description


XM1 out in vdd vdd sky130_fd_pr__pfet_01v8 w=0.84 l=0.15
XM2 out in 0 0 sky130_fd_pr__nfet_01v8 w=0.36 l=0.15


Cload out 0 50fF

Vdd vdd 0 1.8V
Vin in 0 PULSE(0V 1.8V 0 0.1ns 0.1ns 2ns 4ns)

*simulation commands

.tran 1n 10n

.control
run
.endc

.end
```

<img width="1213" height="615" alt="cmos_tran_outvsin" src="https://github.com/user-attachments/assets/65f7f96d-1ec3-4201-856a-9b778714e6b5" />

### Rise delay & Fall delay

<img width="1213" height="788" alt="cmos_delay" src="https://github.com/user-attachments/assets/74f85314-ff7a-4118-be60-134ab6cc58c4" />


**Result Summary:**

| Parameter | Value (ns) |
|------------|------------|
| Rise Delay | 2.48-2.15=0.33 |
| Fall Delay | 4.34-4.05=0.29 |
| Switching Threshold Voltage Vm | 0.876 V |

**Observation:**  
- Rise delay > fall delay due to lower PMOS mobility.  
- Switching point (~0.876 V) aligns with static VTC results.  
- Confirms correct timing performance for Sky130 inverter.

---

## 🧮 Day 4 – CMOS Noise Margin & Robustness Evaluation

**Objective:**  
Determine logic levels (VOH, VOL, VIH, VIL) and compute noise margins (NMH, NML).

**Procedure:**  
- Extract VOH, VOL, VIH, VIL from inverter VTC.  
- Compute NMH = VOH – VIH and NML = VIL – VOL.

### Noise Margin

```spicedeck

*Model Description
.param temp=27


*Including sky130 library files
.lib "sky130_fd_pr/models/sky130.lib.spice" tt


*Netlist Description


XM1 out in vdd vdd sky130_fd_pr__pfet_01v8 w=1 l=0.15
XM2 out in 0 0 sky130_fd_pr__nfet_01v8 w=0.36 l=0.15


Cload out 0 50fF

Vdd vdd 0 1.8V
Vin in 0 1.8V

*simulation commands

.op

.dc Vin 0 1.8 0.01

.control
run
setplot dc1
display
.endc

.end
```

<img width="1218" height="617" alt="inv_noisemargin" src="https://github.com/user-attachments/assets/4390877a-c0c4-4542-9ebe-0363d7e04ad7" />

**Result Summary:**

| Parameter | Value (V) |
|------------|-----------|
| VOH | 1.739 |
| VOL | 0.066 |
| VIH | 1.007 |
| VIL | 0.749 |
| NMH | 0.732 |
| NML | 0.683 |

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

### Supply Variation

```spicedeck

*Model Description
.param temp=27


*Including sky130 library files
.lib "sky130_fd_pr/models/sky130.lib.spice" tt


*Netlist Description


XM1 out in vdd vdd sky130_fd_pr__pfet_01v8 w=1 l=0.15
XM2 out in 0 0 sky130_fd_pr__nfet_01v8 w=0.36 l=0.15


Cload out 0 50fF

Vdd vdd 0 1.8V
Vin in 0 1.8V

.control

let powersupply = 1.8
alter Vdd = powersupply
        let voltagesupplyvariation = 0
        dowhile voltagesupplyvariation < 6
        dc Vin 0 1.8 0.01
        let powersupply = powersupply - 0.2
        alter Vdd = powersupply
        let voltagesupplyvariation = voltagesupplyvariation + 1
      end

plot dc1.out vs in dc2.out vs in dc3.out vs in dc4.out vs in dc5.out vs in dc6.out vs in xlabel "input voltage(V)" ylabel "output voltage(V)" title "Inveter dc characteristics as a function of supply voltage"

.endc

.end
```

<img width="1218" height="617" alt="supply_voltage_vtc" src="https://github.com/user-attachments/assets/ad8533bf-239d-439c-bd18-8b6bd6d39fbc" />

### Supply Voltage Gain

<img width="1220" height="685" alt="supply_voltage_gain" src="https://github.com/user-attachments/assets/40f26d0f-319f-4754-a59e-4c92fec99b9d" />


**Result Summary:**

| VDD | Gain |
|------|------|
| 1.8 | 7.03 |
| 1.6 | 8.45 |
| 1.2 | 10.06 |
| 1.0 | 10.12 |
| 0.8 | 9.67 | 

**Observation:**  
- Gain increases as VDD drops to 1.0 V, but falls at 0.8 V due to weak current drive.  
- Demonstrates power–performance trade-off and robustness boundary.

---

## 📊 Summary Table

| Metric | Typical Value | Insight |
|---------|----------------|---------|
| NMOS Vt | 0.76 V | Extracted from Id–Vgs curve |
| Switching Vm | 0.876 V | Equal Vout = Vin |
| Rise Delay | 0.33 | Output low → high |
| Fall Delay | 0.29 ns | Output high → low |
| NMH | 0.732 V | High-level noise margin |
| NML | 0.683 V | Low-level noise margin |
| Peak Gain | 10.12 | At 1.0 V supply |

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

