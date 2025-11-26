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

# 🧩 Introduction to VSDBabySoC
VSDBabySoC is a **miniature System-on-Chip (SoC)** designed for educational and research purposes. It combines:
- A lightweight RISC‑V core (RVMyth)
- Two important analog hard macros (PLL & DAC)
- A pseudo-random generator
- Clock gating logic

It represents a realistic SoC environment—combining **digital RTL**, **hard macros**, **memory-less datapaths**, and **integration-level design decisions**.

---

# ⭐ Why VSDBabySoC?
Modern SoCs integrate multiple pre-designed analog/digital blocks. VSDBabySoC helps you understand:
- How hard macros are inserted into a digital SoC
- How timing and area constraints change with macros
- Why black-box modeling is critical during synthesis
- How synthesis differs from full-system integration

This makes it an ideal project to transition from simple digital design (Week‑6) to **professional SoC methodologies**.

---





# 🧰 Stage 0 – Tool & Environment Setup

A clean Linux environment was prepared with all necessary open‑source VLSI tools such as Docker, OpenLane, OpenROAD, Yosys, iverilog, GTKWave, and the SkyWater sky130 PDK.





---

# 🧱 Stage 1 – Design Definition & Architecture

<img width="2270" height="1260" alt="image" src="https://github.com/user-attachments/assets/41a1d211-411c-4e67-9284-4d0c5478dd98" />

A System-on-Chip integrates multiple subsystems — processor, memory, I/O controllers, clocks and power management — on a single silicon die.
It enables compact, power-efficient designs used in smartphones, IoT devices, and embedded systems.

BabySoC offers a simplified SoC architecture that is ideal for learning without overwhelming complexity.
It integrates minimal but representative modules to demonstrate real-world SoC concepts and dataflow.

Reviewed BabySoC CPU architecture, design hierarchy, memory map, and module-level connectivity.

---

# 🧾 Stage 2 – RTL Coding & Testbench

Reviewed reference RTL modules and testbenches.  
Simulated using iverilog → vvp → GTKWave.

---

# 🧪 Stage 3 – Functional Simulation

Performed basic functional verification and confirmed signal activity correctness.

<img width="844" height="512" alt="image" src="https://github.com/user-attachments/assets/7e973092-572f-42c3-9a4a-0a7394dbd121" />

---

# 🏭 Stage 4 – Synthesis (RTL → Netlist)

Pre-Synthesis Simulation Waveform Analysis
The pre-synthesis simulation of VSDBabySoC was analyzed using GTKWave. The main signals observed were:

clk – System clock signal. Provides timing reference for all synchronous modules.
reset – Active high reset. Initially asserted to initialize the system and de-asserted after 120 ns to start normal operation.
vco_in – Input to the VCO block of the DAC/PLL. Toggles according to the VCO frequency after reset.
out – DAC output signal. Starts producing values after reset de-assertion, representing the digital-to-analog conversion.
ENb_VCO – VCO enable signal. Kept high (1) during simulation to ensure continuous VCO operation.
Observations:

clk and vco_in toggle periodically after reset de-assertion.
ENb_VCO = 1 ensures the VCO remains active.
DAC output (out) shows expected waveform after reset, confirming correct operation of the SoC modules.

<img width="1062" height="574" alt="image" src="https://github.com/user-attachments/assets/13d1a2c4-a184-4c8b-9c9d-6ff234524932" />

Troubleshooting (Detailed Case Study – Issue #28)
🧩 Problem Encountered
While simulating the BabySoC design, iverilog threw multiple module hierarchy and inclusion errors, similar to those discussed in India RISC-V Tapeout Issue #28.
The compiler failed to recognize interlinked modules (avsddac, avsdpll, and top-level vsdbabysoc.v), causing incomplete netlist generation.

⚙️ Root Cause Analysis
Incorrect include paths for module directories during compilation.
Missing macro definition (-DPRE_SYNTH_SIM) while invoking iverilog.
Verilog source files were spread across multiple folders (src/module, src/include), requiring explicit linking.
I had initially assumed iverilog would automatically detect all submodules, which is incorrect — explicit inclusion is mandatory.
🔧 Corrective Steps Implemented
# Step 1: Identified all submodule file paths
find src/module -type f -name "*.v"

# Step 2: Compiled with explicit include and macro
iverilog -g2012 -o output/pre_synth_sim/pre_synth_sim.out -DPRE_SYNTH_SIM \
  -I src/module \
  src/module/testbench.v \
  src/module/vsdbabysoc.v \
  src/module/avsddac.v \
  src/module/avsdpll.v

# Step 3: Verified simulation output and waveform dump
vvp output/pre_synth_sim/pre_synth_sim.out
gtkwave output/pre_synth_sim/pre_synth_sim.vcd 

# Post-Synthesis GLS & STA Fundamentals
 Toolchain Overview
Tool	Purpose
Yosys	RTL to Gate-Level Netlist Synthesis
Icarus Verilog (iverilog)	Simulation engine for functional and GLS runs
GTKWave	Waveform visualization and comparison
OpenSTA	Static Timing Analysis – setup/hold, slack, and critical path checks
⚙️ Part 1 – Post-Synthesis GLS (Yosys + Icarus + GTKWave)
🧩 Step 1: Synthesis of BabySoC Design
# Run Yosys synthesis script
 cd ~/VSDBabySoC
 make synth
🧩 Step 2: Run Gate-Level Simulation (GLS)
 cd ~/VSDBabySoC
 make post_synth_sim

<img width="1153" height="385" alt="image" src="https://github.com/user-attachments/assets/f0aa5184-feff-409d-8b39-49fc603123fb" />

View and Compare Waveforms
Open GTKWave to view post-synthesis output and compare it with functional simulation.
```
 gtkwave output/post_synth_sim/post_synth_sim.vcd

```

<img width="1280" height="800" alt="image" src="https://github.com/user-attachments/assets/04395a80-3c81-48bb-8c1c-908a4ca40f67" />

## Yosys report
<img width="959" height="350" alt="image" src="https://github.com/user-attachments/assets/b300e258-83f1-4049-b066-d6b5d17818f7" />

## clkgate

<img width="820" height="522" alt="image" src="https://github.com/user-attachments/assets/0de766a7-b2fa-447b-b9c1-9c9f163f3d47" />

## RVMYTH

<img width="491" height="469" alt="image" src="https://github.com/user-attachments/assets/42aa97fa-854b-48a5-bf96-5161b7eb2cd4" />

## VSDBABYSOC

<img width="791" height="306" alt="image" src="https://github.com/user-attachments/assets/c8ffa10d-04b8-4144-b20c-a357935b64e9" />

## design hierarchy

<img width="932" height="639" alt="image" src="https://github.com/user-attachments/assets/bfe00085-b32f-4b05-a2f4-2fb769b4b588" />

## chekpass

<img width="872" height="184" alt="image" src="https://github.com/user-attachments/assets/c006af33-0732-4420-8113-24edf773680f" />

# printing stastistics

## vsdbabysoc

<img width="842" height="690" alt="image" src="https://github.com/user-attachments/assets/a7ea3481-7843-4c83-867f-b372a062b3ad" />


# Generate Timing Graphs with OpenSTA

## Step 1: Load Netlist and Constraints and Perform Timing Analysis
### Create a file named run_sta.tcl:

```
 cd ~/VSDBabySoC
 make sta
```
## Step 2: Output of the OPenSTA tool
```
Warning: ./lib/avsddac.lib line 1, library avsddac already exists.
Startpoint: _9532_ (rising edge-triggered flip-flop clocked by clk)
Endpoint: _10034_ (rising edge-triggered flip-flop clocked by clk)
Path Group: clk
Path Type: max

  Delay    Time   Description
---------------------------------------------------------
   0.00    0.00   clock clk (rise edge)
   0.00    0.00   clock network delay (ideal)
   0.00    0.00 ^ _9532_/CLK (sky130_fd_sc_hd__dfxtp_1)
   4.40    4.40 ^ _9532_/Q (sky130_fd_sc_hd__dfxtp_1)
   5.06    9.47 v _8103_/Y (sky130_fd_sc_hd__clkinv_1)
   0.54   10.01 ^ _8106_/Y (sky130_fd_sc_hd__o211ai_1)
   0.00   10.01 ^ _10034_/D (sky130_fd_sc_hd__dfxtp_1)
          10.01   data arrival time

  11.00   11.00   clock clk (rise edge)
   0.00   11.00   clock network delay (ideal)
   0.00   11.00   clock reconvergence pessimism
          11.00 ^ _10034_/CLK (sky130_fd_sc_hd__dfxtp_1)
  -0.13   10.87   library setup time
          10.87   data required time
---------------------------------------------------------
          10.87   data required time
         -10.01   data arrival time
---------------------------------------------------------
           0.86   slack (MET)

```
🧩 Observations:
Critical Path:
Slack Value:
Interpretation:
If slack > 0 → Timing met ✅
If slack < 0 → Violation exists ⚠️ (requires optimization)

---

# 📐 Stage 5 – Floorplanning & Placement

Executed floorplan generation and placed all standard cells using OpenROAD.

# Install OpenROAD Flow Scripts

```
# Clone OpenROAD Flow Scripts
 git clone https://github.com/The-OpenROAD-Project/OpenROAD-flow-scripts.git
 cd OpenROAD-flow-scripts

# Install all prerequisites
 sudo ./setup.sh


# Verify installation
 ./openroad -version
```

<img width="1200" height="231" alt="image" src="https://github.com/user-attachments/assets/5be01eff-d482-44b9-9828-b102cc585570" />

<img width="600" height="672" alt="image" src="https://github.com/user-attachments/assets/314d096b-1009-41c8-9d41-c465b674d20f" />

# Execute Floorplan + Placement

## Verify:

Core area and die dimensions generated.

Standard cells placed successfully.

Logs created under logs/ directory.

Terminal logs for floorplan & placement completion.

Floorplan and placement layout images.

# Troubleshooting & Learnings
Setting up OpenROAD Flow Scripts was the most challenging yet insightful phase so far. The process tested my patience, problem-solving, and system-level understanding — skills essential for real-world VLSI development.

🔧 Long Setup & Slow Build:
The sudo ./setup.sh process took hours due to limited VM resources. I monitored logs patiently and learned about internal dependencies.
➤ Learning: Toolchain compilation time reflects dependency depth; patience and system monitoring are key.

⚙️ Dependency Conflicts (CMake, SWIG, TCL):
Build failures due to version mismatches were resolved by verifying versions (cmake --version, swig --version) and updating .bashrc paths.
➤ Learning: Environment consistency ensures smooth builds.

---

# 🛣 Stage 6 – Routing

## Purpose: Connect all placed cells and macros electrically while satisfying design rules and timing constraints.
<img width="1203" height="651" alt="image" src="https://github.com/user-attachments/assets/d87f591e-4771-4106-87dc-a396154493b2" />

🧩 Layout Visualization using Magic
After completing the routing stage, I visualized the final layout using Magic VLSI.

🧠 Purpose
This command opens and displays the physical layout of a standard cell or full design inside the Magic tool.
It helps verify the geometries, layers, and interconnections created during routing and ensures that the layout follows the process technology (Sky130A).

🧰 Command Used
```
magic -T /openlane/pdks/sky130A/libs.tech/magic/sky130A.tech sky130_inv.mag &
``
<img width="1224" height="767" alt="image" src="https://github.com/user-attachments/assets/1f325f37-6e01-4818-a38e-99de305a6d18" />

***Layout of the sky130_inv cell opened in Magic, showing active regions, poly gates, and metal interconnections.***


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

