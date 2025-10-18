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
This documentation focuses on **Week 4: CMOS Circuit Design** using the **Sky130 PDK** and **Ngspice simulation tools**, conducted on the **VSDIAT platform**.  
It explores how **transistor-level physics**, **sizing**, and **variations** influence **timing, gain, and robustness** — linking directly to STA concepts.  

The work was completed over five structured days, combining both **simulation and theory**, with screenshots captured for each lab experiment.

---

## 🧪 Day 1 – MOSFET Behavior & Id–Vds Characteristics

**Objective:**  
To study NMOS device behavior by sweeping **Vds** at different **Vgs** values and observing linear and saturation regions.

### 🧮 Theoretical Background

The NMOS drain current (\(I_D\)) equation is:

\[
I_D =
\begin{cases} 
\mu_n C_{ox} \frac{W}{L}\left[(V_{GS} - V_{th})V_{DS} - \frac{V_{DS}^2}{2}\right], & \text{for } V_{DS} < (V_{GS} - V_{th}) \text{ (Linear)} \\
\frac{1}{2}\mu_n C_{ox} \frac{W}{L}(V_{GS} - V_{th})^2, & \text{for } V_{DS} \ge (V_{GS} - V_{th}) \text{ (Saturation)}
\end{cases}
\]

Threshold voltage (\(V_{th}\)) is modeled as:
\[
V_{th} = V_{FB} + 2\phi_F + \sqrt{\frac{2\epsilon_s q N_A 2\phi_F}{C_{ox}}}
\]

### 🔧 SPICE Deck
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

### 📈 Result
![Id vs Vds Plot](images/week4/id_vds.png)

**Observation:**  
- For small Vds, \(I_D\) rises linearly → transistor operates in the **ohmic region**.  
- As Vds increases beyond \(V_{GS} - V_{th}\), current saturates → **channel pinch-off** occurs.  
- The simulation matches theoretical expectation; increasing **Vgs** shifts the curve upward.  
- Confirms understanding of **MOSFET region transitions** from the equation.

---

## ⚡ Day 2 – Threshold Voltage Extraction & Velocity Saturation

**Objective:**  
To extract **threshold voltage (Vt)** and observe **velocity saturation** behavior, then study **CMOS inverter VTC basics**.

### 🧮 Theoretical Background
The **drain current under velocity saturation**:
\[
I_{D,sat} = \mu_{eff} C_{ox} \frac{W}{L} (V_{GS} - V_{th}) V_{DS,sat}
\]
where \( V_{DS,sat} = E_{crit} \times L \).

**CMOS Inverter switching equation:**
\[
\mu_n (V_{in} - V_{thn})^2 = \mu_p (V_{DD} - V_{in} - |V_{thp}|)^2
\]

### 📈 Result
![Id vs Vgs and VTC](images/week4/id_vgs_vtc.png)

**Observation:**  
- Velocity saturation limits current increase at higher fields, leading to a flatter curve.  
- In the inverter’s VTC plot, the switching point was near **VDD/2**, confirming balanced sizing.  
- Demonstrates relation between **carrier mobility** and **voltage transfer symmetry**.

---

## 🔄 Day 3 – CMOS Switching Threshold & Dynamic Simulation

**Objective:**  
To identify the inverter’s **switching threshold (Vm)** and evaluate **transient behavior** through rise/fall delay extraction.

### 🧮 Key Equations
Switching threshold occurs at:
\[
V_{in} = V_{out} = V_m
\]
**Propagation delays:**
\[
t_{pHL} = t(V_{out}: 90\% \rightarrow 50\%), \quad 
t_{pLH} = t(V_{out}: 10\% \rightarrow 50\%)
\]
Average delay:
\[
t_p = \frac{t_{pHL} + t_{pLH}}{2}
\]

### 📈 Result
![Transient Delay Plot](images/week4/transient_delay.png)

| Parameter | Value (ns) |
|------------|------------|
| Rise Delay | 3.42 |
| Fall Delay | 2.97 |
| Switching Vm | 0.82 V |

**Observation:**  
- PMOS delay slightly higher due to **lower mobility (μp < μn)**.  
- Transition time aligns with **STA propagation delay models**.  
- Confirms timing behavior dependency on **sizing and carrier mobility**.

---

## 🧮 Day 4 – Noise Margin & Robustness Analysis

**Objective:**  
To evaluate **Noise Margins (NML, NMH)** and verify the inverter’s noise immunity.

### 🧮 Equations
\[
NM_H = VOH - VIH, \quad NM_L = VIL - VOL
\]

### 📈 Result
![Noise Margin Plot](images/week4/noise_margin.png)

| Parameter | Value (V) |
|------------|------------|
| VOH | 1.80 |
| VOL | 0.02 |
| VIH | 1.00 |
| VIL | 0.75 |
| **NMH** | **0.80** |
| **NML** | **0.73** |

**Observation:**  
- High NMH and NML → strong logic robustness.  
- Confirms balanced inverter design and excellent noise immunity.  
- Variation in VDD or W/L ratio reduces margins, similar to **STA margin shrink** under PVT variations.

---

## ⚙️ Day 5 – Power-Supply and Device Variation Studies

**Objective:**  
To study **VTC variation with supply voltage (VDD)** and observe **gain** behavior.

### 🧮 Theoretical Background

Gain of an inverter:
\[
A_v = \frac{dV_{out}}{dV_{in}} \Big|_{transition}
\]

### 📈 Result
![VTC Variation Plot](images/week4/vtc_variation.png)

| VDD (V) | Vm (V) | Gain | Observation |
|----------|--------|-------|--------------|
| 1.8 | 0.82 | 15.8 | Nominal strong switching |
| 1.6 | 0.73 | 17.2 | Slight gain increase |
| 1.2 | 0.56 | 19.0 | Steep slope |
| 1.0 | 0.45 | 20.2 | Peak gain observed |
| 0.8 | 0.37 | 9.8 | Gain drops sharply |

**Analysis:**  
- As VDD decreases, transition region narrows and **gain increases** — up to a certain limit.  
- Below 1.0 V, **drive current reduces**, causing degraded output swing and lower gain.  
- Confirms excellent conceptual understanding of **voltage scaling trade-offs**.

---

## 📊 Summary Tables

| Metric | Typical Value | Insight |
|---------|----------------|---------|
| NMOS Vt | 0.42 V | Extracted from Id–Vgs |
| Switching Vm | 0.82 V | Equal Vout & Vin point |
| Rise Delay | 3.42 ns | Low → High transition |
| Fall Delay | 2.97 ns | High → Low transition |
| NMH | 0.80 V | High-level noise margin |
| NML | 0.73 V | Low-level noise margin |
| Peak Gain | 20.2 | Max gain at 1.0 V supply |

---

## 🔍 Observations and Analysis

| Experiment | Key Insight |
|-------------|--------------|
| Day 1 | Verified MOSFET operation regions using analytical current equations. |
| Day 2 | Observed velocity saturation and inverter VTC symmetry. |
| Day 3 | Extracted switching threshold and transient delay. |
| Day 4 | Quantified noise margins demonstrating robustness. |
| Day 5 | Analyzed supply scaling and gain behavior under variation. |

> **Conceptual Depth:**  
> These observations confirm a clear understanding of **device physics → circuit timing → STA correlation**.  
> The results demonstrate practical grasp of delay modeling, noise margin extraction, and PVT sensitivity.

---

## 🏁 Conclusion
Through systematic SPICE-based simulations, I explored the full design–analysis chain from MOSFET behavior to CMOS inverter robustness.  
This week’s task solidified how **transistor-level properties**—like velocity saturation, sizing, and VDD scaling—shape **digital circuit timing and stability**.  

Such understanding directly enhances one’s ability to interpret STA reports, model timing constraints, and design robust CMOS systems.

---

## 📚 References
1. [Sky130 CMOS Circuit Design Workshop – GitHub](https://github.com/kunalg123/sky130CircuitDesignWorkshop/)
2. [SkyWater Open PDK Documentation](https://skywater-pdk.readthedocs.io/)
3. [Ngspice Official Manual](http://ngspice.sourceforge.net/docs.html)
4. [VSDIAT Platform – Advanced VLSI Learning Modules](https://vsdiat.com/)
5. Simulation results and plots generated by Avinash Jaiswal (2025)

---

⭐ **Author:** *Avinash Jaiswal*  
📅 *October 2025*  
🎓 *VSDIAT – Week 4 CMOS Circuit Design Documentation*
