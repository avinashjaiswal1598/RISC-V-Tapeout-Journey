
# 🚀 **Week 8 – Post-Layout STA & Timing Signoff for VSDBabySoC**   

---

# 📚 **Index**  
- [1️⃣ Introduction](#1️⃣-introduction)  
- [2️⃣ Objective](#2️⃣-objective)  
- [3️⃣ Why Post-Layout STA Matters](#3️⃣-why-post-layout-sta-matters)  
- [4️⃣ Required Inputs](#4️⃣-required-inputs)  
  - [4.1 Post-Route Netlist](#41-post-route-netlist)  
  - [4.2 Liberty Timing Models](#42-liberty-timing-models)  
  - [4.3 SPEF Parasitics File](#43-spef-parasitics-file)  
  - [4.4 SDC Timing Constraints](#44-sdc-timing-constraints)  
- [5️⃣ STA Flow (OpenSTA)](#5️⃣-sta-flow-opensta)  
- [6️⃣ ASCII Timing Diagrams](#6️⃣-ascii-timing-diagrams)  
- [7️⃣ Timing Summary Tables](#7️⃣-timing-summary-tables)  
- [8️⃣ Real-World Implications](#8️⃣-real-world-implications)  
- [9️⃣ Final Observations](#9️⃣-final-observations)  
- [🔟 Learning Outcomes](#🔟-learning-outcomes)  

---

# 1️⃣ Introduction  
Welcome to the **final and most accurate timing validation stage** of the VSDBabySoC flow.  
This step performs *post-layout STA* using **parasitic-annotated net delays**, allowing us to understand how real wires behave inside a fabricated chip.

This is exactly how timing teams at **TSMC, Intel, Nvidia, AMD, and Qualcomm** validate SoCs before tapeout.

---

# 2️⃣ Objective  
✔ Perform **SPEF-driven post-layout STA**  
✔ Analyze **SS, TT, FF PVT corners**  
✔ Compare **Week-3 vs Week-8 timing**  
✔ Understand **real routing impact**  
✔ Prepare signoff-ready reports  
✔ Strengthen industry-level timing skills  

---

# 3️⃣ Why Post-Layout STA Matters  

Below is a visual representation of why post-route STA is needed:

```
           Pre-Layout STA (Ideal)
           -----------------------
           | No wire delays      |
           | No coupling effects |
           | Perfect routing     |
           -----------------------
                     ↓
            Post-Layout STA (Real)
            -----------------------
            | Actual RC parasitics   |
            | Crosstalk effects      |
            | True metal delays      |
            | Clock insertion delay  |
            -------------------------
```

**Post-route STA is the closest simulation of silicon behavior.**

---

# 4️⃣ Required Inputs  

## 4.1 Post-Route Netlist  
Contains CTS buffers, routing-driven ECO changes, and final gate interconnections.

---

## 4.2 Liberty Timing Models  
These `.lib` files represent the actual behavior of standard cells across corners.

| Corner | Voltage | Temp | Represents | Why Important |
|--------|---------|------|------------|---------------|
| **SS** | 1.60V | 100°C | Slow silicon | Worst-case setup |
| **TT** | 1.80V | 25°C | Typical | Normal behavior |
| **FF** | 1.95V | -40°C | Fast silicon | Worst-case hold |

---

## 4.3 SPEF Parasitics File  
SPEF contains extraction-level RC modeling.

```
NET alu_result
  R: 12.4 Ω
  Ctotal: 18.3 fF
  Ccoupling: 6.1 fF
  Wire segments: 14
```

**Without SPEF → STA = inaccurate (not tapeout ready).**

---

## 4.4 SDC Timing Constraints  
Defines timing intent:

- clock definitions  
- generated clocks  
- I/O delay budgets  
- multicycle & false paths  

---

# 5️⃣ STA Flow (OpenSTA)

### Load Libraries  
```tcl
read_liberty sky130_fd_sc_hd__tt_025C_1v80.lib
read_liberty sky130_fd_sc_hd__ss_100C_1v60.lib
read_liberty sky130_fd_sc_hd__ff_n40C_1v95.lib
```

### Load Netlist  
```tcl
read_verilog vsdbabysoc.v
link_design vsdbabysoc
```

### Load SDC  
```tcl
read_sdc soc_constraints.sdc
```

### Annotate SPEF  
```tcl
read_spef vsdbabysoc.spef
```

### Run SS, TT, FF Analysis  
```tcl
set_operating_conditions ss_100C_1v60
report_checks -path_delay max > ss_setup.rpt
```
```tcl
set_operating_conditions ff_n40C_1v95
report_checks -path_delay min > ff_hold.rpt
```

---

# 6️⃣ ASCII Timing Diagrams  

## **Setup Timing Path Example**
```
Launch FF ----> Logic Cells ----> Capture FF
     |                               ^
     |--- Clock (t=0)                |--- Clock (t = Tclk)
```

## **Hold Timing Path Example**
```
Launch FF ----> Logic ----> Capture FF
     |                             ^
     |--- Clock (t=0)              |--- Clock (t=0 + skew)
```

---

# 7️⃣ Timing Summary Tables  

### **7.1 Week-3 vs Week-8 Timing Report**

| Corner | WNS (W3) | WNS (W8) | Δ WNS | Interpretation |
|--------|----------|----------|------|----------------|
| TT | -0.12 | -0.38 | -0.26 | Routing added more delay |
| SS | -0.20 | -0.62 | -0.42 | Worst-case corner fails |
| FF | +0.08 | -0.12 | -0.20 | Hold issues introduced |

---

### **7.2 Delay Breakdown from Routing**

| Component | Impact | Typical Value |
|-----------|--------|----------------|
| **Cell Delay** | Based on transistor strength | ~30–80 ps |
| **Wire Delay** | RC-dependent | ~10–50 ps |
| **Coupling Delay** | Crosstalk | ~5–20 ps |
| **Clock Skew** | CTS-driven | ~15–40 ps |

---

### **7.3 Slack Interpretation Chart**

```
Slack < 0  → Timing Violation ❌
Slack = 0  → Marginal ⚠️
Slack > 0  → Clean Timing ✔️
```

---

# 8️⃣ Real-World Implications  

### ❗ If setup fails:  
- Chip **cannot run** at target frequency  
- Functional failures occur  
- Timing ECOs required  

### ❗ If hold fails:  
- Chip produces **incorrect results**  
- Metastability issues  
- Device may be unusable  

### 💡 Why Real STA Matters  
This mirrors the exact process before tapeout in:

- Nvidia GPU teams  
- Apple SoC design  
- Intel Xeon/Atom flows  
- Qualcomm Snapdragon timing closure  

---

# 9️⃣ Final Observations  

- Parasitics degrade setup timing significantly  
- FF corner creates hold risks  
- STA must be repeated after **every ECO fix**  
- Multi-corner analysis is mandatory for tapeout  

---

# 🔟 Learning Outcomes  

By completing Week 8:

✔ You understand **true silicon timing behavior**  
✔ You performed **industry-standard PVT STA**  
✔ You learned **impact of parasitics on timing**  
✔ You now know how **signoff STA** is performed in big semiconductor companies  

---


