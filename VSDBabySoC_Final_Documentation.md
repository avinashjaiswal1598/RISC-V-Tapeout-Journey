# VSDBabySoC – Final Stage‑Wise Documentation (RISC-V Tapeout Journey)

> **Author:** Avinash Jaiswal  
> **Platform:** [VSDIAT](https://vsdiat.vlsisystemdesign.com/)

<img width="1280" height="720" alt="image" src="https://github.com/user-attachments/assets/005076f3-40ef-4a04-8ff9-787f9e79c717" />

---

# 📘 Introduction

This repository contains my complete **stage‑wise documentation** for the VSDBabySoC RISC‑V Tapeout Journey. Over several weeks, I moved through the full ASIC design flow—from understanding CMOS device behavior to RTL verification, synthesis, physical design, parasitic extraction, and finally post‑layout STA across PVT corners.

For each stage, I included:
- Steps I followed  
- Commands used  
- My understanding of the process  
- Important observations  

---

# 📂 Repository Structure

```
RISC-V-Tapeout-Final/
├── README.md
├── images/
├── reports/
└── scripts/
```

---

# 🧰 Stage 0 – Tool & Environment Setup

A clean Linux environment was prepared with all necessary open‑source VLSI tools such as Docker, OpenLane, OpenROAD, Yosys, iverilog, GTKWave, and the SkyWater sky130 PDK.





---

# 🧱 Stage 1 – Design Definition & Architecture

Reviewed BabySoC CPU architecture, design hierarchy, memory map, and module-level connectivity.

---

# 🧾 Stage 2 – RTL Coding & Testbench

Reviewed reference RTL modules and testbenches.  
Simulated using iverilog → vvp → GTKWave.

---

# 🧪 Stage 3 – Functional Simulation

Performed basic functional verification and confirmed signal activity correctness.

---

# 🏭 Stage 4 – Synthesis (RTL → Netlist)

Used Yosys/OpenLane synthesis to generate a gate‑level netlist using sky130_fd_sc_hd standard cells.

---

# 📐 Stage 5 – Floorplanning & Placement

Executed floorplan generation and placed all standard cells using OpenROAD.

---

# 🛣 Stage 6 – Routing

Completed global and detailed routing, producing a DRC‑clean routed layout conceptually.

---

# 📊 Stage 7 – SPEF Generation

Used OpenROAD to extract RC parasitics and generate the post‑route `.spef` file.

---

# ⏱ Stage 8 – Post‑Layout STA Across PVT Corners

Performed OpenSTA analysis using TT/SS/FF liberty files, the post‑route netlist, the SPEF file, and SDC constraints.

### ✔ Learning Outcomes

#### 🔹 Why pre‑route timing is different from post‑route timing  
Pre‑route timing assumes ideal wires with almost zero delay. After routing, actual metal layers introduce resistance and capacitance, increasing true propagation delays. Thus, post‑route STA reflects real silicon behavior.

#### 🔹 How SPEF changes timing calculations  
SPEF provides real parasitic information—resistance, capacitance, and coupling. STA uses this data to compute accurate net delays. This replaces ideal delay models with extraction‑based delays.

#### 🔹 How real silicon effects impact critical paths  
RC delay, coupling capacitance, and long‑wire resistance degrade setup timing and may create hold issues. These effects reshape the critical path profile completely compared to pre‑route design.

#### 🔹 Why multi‑corner STA is essential  
Silicon varies with process (P), voltage (V), and temperature (T).  
- Slow corner → worst setup  
- Fast corner → worst hold  
Only multi‑corner STA ensures the chip works under all real‑world operating conditions.

---

# 📝 Final Reflections

This journey helped me understand the complete end‑to‑end ASIC design flow and how each stage contributes to achieving a tape‑out‑ready design.

---

# 📎 Appendix (To Be Updated When Images/Logs Are Added)

- Floorplan view  
- Placement view  
- Routing results  
- SPEF snippet  
- STA reports  
- Week‑3 vs Week‑8 comparison tables  

