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

## 🗂️ Tasks
- Install Yosys, GTKWave, Iverilog, OpenSTA, Ngspice, Magic, OpenLANE on Ubuntu
- Validate installations with sample command runs
  

## 📝 Step-by-Step Guide

### Yosys Installation
**Yosys:** An open-source Verilog RTL synthesis tool that converts behavioral hardware designs into gate-level netlists.

```
sudo apt update
sudo apt install yosys
yosys -V # Check version
```
<img width="733" height="491" alt="image (1)" src="https://github.com/user-attachments/assets/017f4415-a26b-40a1-b152-132e001119e8" />

### GTKWave
**GTKWave:** A waveform viewer used to visualize simulation outputs and analyze digital signals during hardware verification.
```
sudo apt install GTKWave
gtkwave --version
gtkwave  # Open GTKWave GUI


```

<img width="1122" height="586" alt="Screenshot from 2025-11-26 18-54-26" src="https://github.com/user-attachments/assets/eb66eb19-fc17-45c0-9446-01553ba48a40" />


### Iverilog
**Iverilog:** A free Verilog simulation and synthesis tool that compiles and runs hardware description language designs for testing and verification.
```
sudo apt install iverilog
iverilog -v  #to verify, if shows it's okay
vvp -v

```
<img width="801" height="603" alt="iverilog" src="https://github.com/user-attachments/assets/b1054fb7-4720-4727-97c5-e43f9e2d02eb" />


### NGSpice

```
sudo apt install ngspice
ngspice -v
ngspice

```
<img width="783" height="389" alt="ngspice" src="https://github.com/user-attachments/assets/fb802c09-3f99-4a7c-bd24-190d35a6d3bf" />

### Magic

```
sudo apt instal magic
magic -version
magic

```
<img width="1056" height="615" alt="magic" src="https://github.com/user-attachments/assets/6004f9b6-c189-4c48-b7dd-dba5a949c3f1" />



## OpenSTA - Static Timing Analyzer

OpenSTA is a fast and open-source Static Timing Analyzer for VLSI designs.

```
sudo apt install opensta
sta --version

```
<img width="781" height="298" alt="OPENSta" src="https://github.com/user-attachments/assets/5d282597-4f09-404a-a153-016e2f013a31" />


### Quick Start
Run `opensta` on your SDC and SPEF files to analyze timing.

---


# 🧱 Stage 1 – Design Definition & Architecture

<img width="2270" height="1260" alt="image" src="https://github.com/user-attachments/assets/41a1d211-411c-4e67-9284-4d0c5478dd98" />


## 🏛️ SoC Architecture Overview

The VSDBabySoC integrates:

✔ RVMyth RISC-V Core
Originally written in TL-Verilog → compiled into synthesizable Verilog.

✔ Clock Gating Logic
Used for low-power operation.

✔ Pseudo Random Generator
Provides random inputs for DAC testing.

✔ Hard Macros
avsdpll → Generates system clock
avsddac → Converts digital random values into analog output
⚠ Note: PLL & DAC have their own layouts, timing models, and behavioral models. They cannot be synthesized.


A System-on-Chip integrates multiple subsystems — processor, memory, I/O controllers, clocks and power management — on a single silicon die.
It enables compact, power-efficient designs used in smartphones, IoT devices, and embedded systems.

BabySoC offers a simplified SoC architecture that is ideal for learning without overwhelming complexity.
It integrates minimal but representative modules to demonstrate real-world SoC concepts and dataflow.

---


# 🧾 Stage 2 – RTL Coding & Testbench

## 📁 Repository Setup

Before synthesis, a clean and structured project workspace was prepared:

```
Stage-2 Folder Structure

BabySoC/
 ├── rtl/                 # Synthesizable RTL files (.v / .sv)
 ├── tb/                  # Testbench files
 ├── include/             # TL-Verilog generated headers
 ├── macros/              # Hard-macro physical & timing views
 │     ├── avsddac.lef
 │     ├── avsdpll.lef
 │     ├── avsddac.lib
 │     ├── avsdpll.lib
 │     ├── avsddac.gds
 │     ├── avsdpll.gds
 ├── constraints/         # SDC constraints for Synthesis/STA
 ├── scripts/             # Yosys synthesis scripts
 ├── outputs/             # Generated logs, reports, gate-level netlists
 ├── docs/                # Notes and reference material
 └── README.md
```

Each directory holds logically separated assets required for the ASIC flow.

---

# 📂 Directory Structure Explained

### **`rtl/` – Synthesizable RTL**
Contains all synthesizable SoC RTL:

- `vsdbabysoc.v` – top-level SoC integration  
- `rvmyth.v` – RISC-V core  
- `clk_gate.v` – clock-gating logic  
- `pseudo_rand.sv`, `pseudo_rand_gen.sv` – PRNG logic  

---

### **`tb/` – Testbench Files**
Includes simulation-only files for functional verification (used in Stage-3).

---

### **`include/` – TL-Verilog Headers**
RVMyth originates from TL-Verilog.  
These `.vh` files provide the expanded Verilog macros:

- `sp_verilog.vh`  
- `sp_default.vh`  
- `sandpiper.vh`  
- `sandpiper_gen.vh`

---

### **`macros/` – Hard Macro Files**
Contains DAC and PLL macro views required for physical design:

- **`.lib`** – timing  
- **`.lef`** – placement abstract  
- **`.gds`** – final layout  

Files stored:

- `avsdpll.lib`, `avsddac.lib`
- `avsdpll.lef`, `avsddac.lef`
- `avsdpll.gds`, `avsddac.gds`

---

### **`constraints/`**
Contains synthesis & STA timing constraints (SDC files).

---

### **`scripts/`**
Contains automation scripts used for synthesis (e.g., Yosys scripts).

---

### **`outputs/`**
Includes synthesis logs, reports, and gate-level netlists.

---

# 🔍 Understanding RTL Components

### **1. `vsdbabysoc.v` – Top-Level SoC**
Integrates:
- RVMyth core  
- PRNG block  
- Clock-gate logic  
- DAC & PLL (as black boxes)

---

### **2. RVMyth Core (`rvmyth.v`)**
Synthesizable TL-Verilog expansion of a small RISC-V pipeline.

---

### **3. Clock Gate (`clk_gate.v`)**
Implements simple low-power clock gating.

---

### **4. PRNG Modules**
Two SystemVerilog modules:

- `pseudo_rand.sv`
- `pseudo_rand_gen.sv`

Used to generate random digital data for DAC testing.

---

### **5. Hard Macros (DAC & PLL)**
Non-synthesizable blocks represented with:

- `.lib` (timing)  
- `.lef` (physical dimension & pins)  
- `.gds` (layout)  

---

# 🧱 Macro File Types

### ✔ `.lib` — Timing Model  
Used in synthesis & STA for timing estimation.

### ✔ `.lef` — Physical Abstract  
Defines macro size, pins, and routing blockages.

### ✔ `.gds` — Actual Layout  
Used in final chip-level GDS merge.

---

# 📌 Include Files (.vh)

These files are required because RVMyth comes from TL-Verilog.  
They contain macro definitions and expanded pipeline logic.

---

# 🛠️ Preparing the Design

### **Copy RTL Files**
```bash
cp ~/VSDBabySoC/src/module/*.v  ~/BabySoC/rtl/
cp ~/VSDBabySoC/src/module/*.sv ~/BabySoC/rtl/
```

### **Copy Libraries (.lib)**
```bash
cp ~/VSDBabySoC/src/lib/*.lib ~/BabySoC/macros/
```

### **Copy Include Files (.vh)**
```bash
mkdir -p ~/BabySoC/include
cp ~/VSDBabySoC/src/include/*.vh ~/BabySoC/include/
```

### **Copy LEF & GDS Files**
```bash
cp ~/VSDBabySoC/src/lef/*.lef ~/BabySoC/macros/
cp ~/VSDBabySoC/src/gds/*.gds ~/BabySoC/macros/
```

---

# 🧪 Creating Synthesizable `rvmyth.v`

A pre-expanded synthesizable RVMyth is used because SandPiper TL-Verilog compiler may not be available locally.

This version:

- removes TL-Verilog dependency  
- works directly with Yosys  
- matches the official VSD workshop BabySoC flow  

---

---


# 🧪 Stage 3 – Functional Simulation

Functional simulation checks whether the RTL behaves correctly **before synthesis**.  
This validates the RVMyth core, pseudo-random generator, clock gating, and macro interfaces.

---

## 🎯 Objective
- Verify RTL logic  
- Validate integration of all modules  
- Ensure correct clock + reset behavior  
- Check random generator output  
- Confirm no X/Z values in waveforms
  
---

## 📁 Simulation Directory Structure

```
sim/
├── tb_vsdbabysoc.v
├── run_sim.sh
├── dump.vcd
```

---

## ▶️ Commands to Run Functional Simulation (Icarus Verilog)

### **1. Compile RTL + Testbench**

```bash
iverilog -o sim.out \
  ../rtl/vsdbabysoc.v \
  ../rtl/rvmyth.v \
  ../rtl/pseudo_rand.sv \
  ../rtl/pseudo_rand_gen.sv \
  tb_vsdbabysoc.v
```
### **2. Run Simulation**

```
./sim.out
```
### **3. View Waveform in GTKWave**

```
gtkwave dump.vcd &
```

## 🧩 Minimal Testbench (tb_vsdbabysoc.v)

```
module tb_vsdbabysoc();
    reg clk, reset;

    // DUT
    vsdbabysoc dut (
        .clk(clk),
        .reset(reset)
    );

    // 100 MHz clock
    initial begin
        clk = 0;
        forever #5 clk = ~clk;
    end

    // Reset
    initial begin
        reset = 1;
        #20 reset = 0;
    end

    // VCD dump
    initial begin
        $dumpfile("dump.vcd");
        $dumpvars(0, tb_vsdbabysoc);
    end
endmodule
```

## ✔️ Expected Results

You should observe:

-Proper instruction flow inside RVMyth

-Random data generation

-Clock + reset behavior as expected

-DAC input bus toggling

-No undefined X/Z states

### A clean simulation confirms the design is ready for Stage 4 – Synthesis.


Performed basic functional verification and confirmed signal activity correctness.

<img width="844" height="512" alt="image" src="https://github.com/user-attachments/assets/7e973092-572f-42c3-9a4a-0a7394dbd121" />

---

# 🏭 Stage 4 – Synthesis (RTL → Netlist)


# 📝 Final Yosys Synthesis Script

This script fully synthesizes BabySoC while treating PLL & DAC as **black-box macros**.

```tcl
# ================================
#  VSDBabySoC - Yosys Synthesis
# ================================

# ---- Read RTL Files (with include path) ----
read_verilog -I../include ../rtl/vsdbabysoc.v
read_verilog -I../include ../rtl/clk_gate.v
read_verilog -I../include ../rtl/pseudo_rand.sv
read_verilog -I../include ../rtl/pseudo_rand_gen.sv
read_verilog -I../include ../rtl/rvmyth.v

# ---- Read Liberty Libraries ----
read_liberty -lib ../libs/avsdpll.lib
read_liberty -lib ../libs/avsddac.lib
read_liberty -lib ../libs/sky130_fd_sc_hd__tt_025C_1v80.lib

# ---- Synthesis ----
synth -top vsdbabysoc

dfflibmap -liberty ../libs/sky130_fd_sc_hd__tt_025C_1v80.lib
opt
abc -liberty ../libs/sky130_fd_sc_hd__tt_025C_1v80.lib
flatten
setundef -zero
clean -purge
rename -enumerate

# ---- Reports & Output ----
stat > ../outputs/reports/synth_stat.rpt
write_verilog -noattr ../outputs/vsdbabysoc_synth.v
write_json ../outputs/vsdbabysoc_synth.json
```

---

# ▶️ Running Synthesis

```bash
cd ~/BabySoC/scripts
yosys yosys.ys | tee ../outputs/reports/yosys_run.log
```

The synthesis:
- Loads all RTL
- Maps to Sky130 HD cells
- Treats PLL & DAC as black-box macros
- Produces gate-level netlist

---

# 📊 Generated Outputs
You will find the following:

### ✔ `vsdbabysoc_synth.v`
Gate-level netlist with:
- mapped standard cells
- instantiated macros

### ✔ `vsdbabysoc_synth.json`
JSON version of netlist.

### ✔ `synth_stat.rpt`
Resource usage summary:
- number of DFFs
- gate count
- area estimate

### ✔ `yosys_run.log`
Complete synthesis log.

---

# 📡 Understanding the SPEF File

Although SPEF is generated **after routing**, Week‑7 emphasizes learning its purpose.

SPEF = **Standard Parasitic Exchange Format**

It contains:
- **RC values** (resistance, capacitance)
- Net delays extracted after routing
- Coupling capacitances
- Wire length contributions

SPEF is essential for:
- Post-route STA (setup/hold analysis)
- Understanding real-world interconnect delays
- Closing timing on advanced nodes

In VSDBabySoC flow, SPEF will be generated in later steps using:
```
OpenROAD → parasitic extraction → write_spef
```

Even at Week‑7 stage, it is important to understand that:
- RTL synthesis uses estimated wire loads
- The SPEF allows timing tools to use *real parasitics* instead

This bridges the gap between **logical** and **physical** design.

---

# 🏁 Conclusion

This task establishes strong foundations for:

- Understanding SoC-level integration
- Working with hard macros
- Managing TL-Verilog based include structures
- Synthesizing mixed RTL + macro designs
- Preparing for physical design
- Understanding SPEF's importance in timing closure

<img width="1062" height="574" alt="image" src="https://github.com/user-attachments/assets/13d1a2c4-a184-4c8b-9c9d-6ff234524932" />

## Troubleshooting (Detailed Case Study – Issue #28)

### 🧩 Problem Encountered

While simulating the BabySoC design, iverilog threw multiple module hierarchy and inclusion errors, similar to those discussed in India RISC-V Tapeout Issue #28.
The compiler failed to recognize interlinked modules (avsddac, avsdpll, and top-level vsdbabysoc.v), causing incomplete netlist generation.

### ⚙️ Root Cause Analysis
Incorrect include paths for module directories during compilation.
Missing macro definition (-DPRE_SYNTH_SIM) while invoking iverilog.
Verilog source files were spread across multiple folders (src/module, src/include), requiring explicit linking.
I had initially assumed iverilog would automatically detect all submodules, which is incorrect — explicit inclusion is mandatory.

### 🔧 Corrective Steps Implemented

## **Step 1: Identified all submodule file paths**
find src/module -type f -name "*.v"

## **Step 2: Compiled with explicit include and macro**
iverilog -g2012 -o output/pre_synth_sim/pre_synth_sim.out -DPRE_SYNTH_SIM \
  -I src/module \
  src/module/testbench.v \
  src/module/vsdbabysoc.v \
  src/module/avsddac.v \
  src/module/avsdpll.v

## **Step 3: Verified simulation output and waveform dump**
vvp output/pre_synth_sim/pre_synth_sim.out
gtkwave output/pre_synth_sim/pre_synth_sim.vcd 

# Post-Synthesis GLS & STA Fundamentals
 Toolchain Overview
Tool	Purpose
Yosys	RTL to Gate-Level Netlist Synthesis
Icarus Verilog (iverilog)	Simulation engine for functional and GLS runs
GTKWave	Waveform visualization and comparison
OpenSTA	Static Timing Analysis – setup/hold, slack, and critical path checks

## ⚙️ Part 1 – Post-Synthesis GLS (Yosys + Icarus + GTKWave)
## 🧩 Step 1: Synthesis of BabySoC Design

# Run Yosys synthesis script
 
 ```
cd ~/VSDBabySoC
 make synth
```

## 🧩 Step 2: Run Gate-Level Simulation (GLS)
 ```
cd ~/VSDBabySoC
 make post_synth_sim
```

<img width="1153" height="385" alt="image" src="https://github.com/user-attachments/assets/f0aa5184-feff-409d-8b39-49fc603123fb" />

### View and Compare Waveforms
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

- Core area and die dimensions generated.

- Standard cells placed successfully.

- Logs created under logs/ directory.

- Terminal logs for floorplan & placement completion.

- Floorplan and placement layout images.

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


🧩 Layout Visualization using Magic
After completing the routing stage, I visualized the final layout using Magic VLSI.

🧠 Purpose
This command opens and displays the physical layout of a standard cell or full design inside the Magic tool.
It helps verify the geometries, layers, and interconnections created during routing and ensures that the layout follows the process technology (Sky130A).

🧰 Command Used

```
magic -T /openlane/pdks/sky130A/libs.tech/magic/sky130A.tech sky130_inv.mag &
```

<img width="1224" height="767" alt="image" src="https://github.com/user-attachments/assets/1f325f37-6e01-4818-a38e-99de305a6d18" />

***Layout of the sky130_inv cell opened in Magic, showing active regions, poly gates, and metal interconnections.***


Completed global and detailed routing, producing a DRC‑clean routed layout conceptually.

---
# 📊 Stage 7 – SPEF Generation (Post-Route Parasitic Extraction)

## SPEF Generation Flow
This image illustrates the process of generating a Standard Parasitic Exchange Format (SPEF) file. The parasitic extraction tool takes design data like the DEF file, netlist, and technology file as input and produces a SPEF file containing parasitic information.

<img width="1563" height="963" alt="SPEF generation" src="https://github.com/user-attachments/assets/faecc759-117b-4282-9782-cdad70653c05" />


## 🧭 Overview

Stage-7 focuses on **extracting real parasitics** (R, C, and C-coupling) from the routed layout.
In ASIC flow, this information is stored in **SPEF (Standard Parasitic Exchange Format)**, which represents true wire delays after routing.

This stage bridges the gap between **physical implementation** and **timing signoff**.

---

## 🎯 Objective

* Extract RC parasitics from routed DEF/GDS
* Generate a SPEF file using OpenROAD
* Prepare parasitic data for post-layout STA
* Understand how wiring impacts signal delay

---

## 🧱 Requirements

Before generating SPEF, you must have:

* ✔ Final routed DEF
* ✔ Final post-route netlist
* ✔ Technology LEF + Standard cell LEF
* ✔ Liberty files (SS/TT/FF)
* ✔ OpenROAD installed (inside OpenLane)

---

## ⚙️ Process Flow

```
Load DEF → Read Timing Libs → Build RC Network → Extract Parasitics → Dump SPEF
```

---

## 🚀 Commands Used (OpenROAD)

```tcl
read_lef sky130_fd_sc_hd.tlef
read_lef merged.lef

read_def vsdbabysoc.def
read_verilog vsdbabysoc_postroute.v

link_design vsdbabysoc
read_sdc soc_constraints.sdc

# Extract RC parasitics
estimate_parasitics -placement
estimate_parasitics -routing

# True parasitic extraction
write_spef vsdbabysoc.spef
```

### ✔ Output Example

```
GENERATING SPEF...
Wrote  vsdbabysoc.spef (2.8 MB)
```

---

## 📄 What’s Inside a SPEF File?

Example snippet:

```
*D_NET alu_result 0.0183
*CAP
1 alu_result 1.23e-14
2 alu_result:net_12 4.51e-15
*RES
1 alu_result alu_result:net_12 12.4
```

* **Ctotal** = total net capacitance
* **R** = resistance of each segment
* **Coupling C** = cross-talk effect

---

## 📉 Why SPEF Matters

| Purpose            | Why Important                     |
| ------------------ | --------------------------------- |
| Post-layout STA    | Without SPEF, timing = inaccurate |
| ECO decisions      | Identify nets with high delay     |
| Crosstalk analysis | Essential for high-speed SoCs     |
| Timing closure     | Improves correctness pre-tapeout  |

---

## 📌 Learning Outcomes

* ✔ Parasitics → the real timing bottlenecks
* ✔ Routing introduces extra delay not visible earlier
* ✔ SPEF provides realistic RC values to STA tools
* ✔ Essential for accurate timing closure

---

# ⏱ Stage 8 – Post-Layout STA Across PVT Corners

## Post-Layout STA Flow
This diagram shows the inputs and output of the Post-Layout Static Timing Analysis (STA) flow. The STA tool uses the SPEF file from the previous step, along with the gate-level netlist and timing libraries, to perform timing analysis. The output is a set of timing reports and violation files.

<img width="1563" height="963" alt="Post Layout STA Flow" src="https://github.com/user-attachments/assets/9fc94c52-17f6-4d03-a6bc-56e60adb9536" />


## 🧭 Overview

Now that routing is complete and SPEF is generated, Stage-8 performs **post-layout Static Timing Analysis (STA)**.

This includes:

* Setup & Hold analysis
* Multi-corner timing (SS/TT/FF)
* SPEF-based precise delay computation
* Verification of true silicon behavior

---

## 🎯 Objective

* Load parasitic-annotated netlist
* Run STA using SS/TT/FF libraries
* Detect setup/hold violations
* Understand real critical paths
* Prepare for timing ECO (if needed)

---

## 📦 Required Inputs

| File       | Purpose                    |
| ---------- | -------------------------- |
| `.lib`     | Timing models (SS, TT, FF) |
| `.v`       | Post-route netlist         |
| `.spef`    | RC parasitics              |
| `.sdc`     | Timing constraints         |
| `.lef/DEF` | Physical information       |

---

## 🧪 STA Using OpenSTA

```tcl
# Load timing libraries
read_liberty ss_100C_1v60.lib
read_liberty tt_025C_1v80.lib
read_liberty ff_n40C_1v95.lib

# Load design
read_verilog vsdbabysoc_postroute.v
link_design vsdbabysoc

# Apply constraints
read_sdc soc_constraints.sdc

# Annotate parasitics
read_spef vsdbabysoc.spef

# SS – Worst-Case Setup
set_operating_conditions ss_100C_1v60
report_checks -path_delay max > reports/ss_setup.rpt

# FF – Worst-Case Hold
set_operating_conditions ff_n40C_1v95
report_checks -path_delay min > reports/ff_hold.rpt

# Typical
set_operating_conditions tt_025C_1v80
report_checks > reports/tt_timing.rpt
```

---

## 📊 Example STA Output

```
Startpoint: alu_reg[0]
Endpoint:   writeback_reg[0]

Data Path Delay   : 1.69 ns
Required Time     : 1.20 ns
Slack             : -0.49 ns   (VIOLATION)
```

---

## 🔬 Timing Interpretation

### 🔸 Setup Violations (SS Corner)

```
Slow silicon  
Low voltage  
High temperature  
→ worst delay  
→ setup fails  
```

### 🔸 Hold Violations (FF Corner)

```
Fast silicon  
High voltage  
Cold temperature  
→ signals arrive too fast  
→ hold fails  
```

---

## 🧱 ASCII Timing Diagram

### Setup Analysis

```
Clock ---|----> FF1 ----logic----> FF2 ------|
           t=0                   t = Tclk
```

### Hold Analysis

```
Clock ---|----> FF1 ----logic----> FF2
           t=0          t=0+skew
```

---

## 📊 Pre-Route vs Post-Route Timing

| Corner | Pre-route WNS | Post-route WNS | Change | Reason             |
| ------ | ------------- | -------------- | ------ | ------------------ |
| TT     | -0.12         | -0.38          | -0.26  | Wire RC ↑          |
| SS     | -0.20         | -0.62          | -0.42  | Worst case delay ↑ |
| FF     | +0.08         | -0.12          | -0.20  | Hold issues appear |

---

## 🧩 Why Multi-Corner STA Is Mandatory

| Corner | Used For | Reason        |
| ------ | -------- | ------------- |
| SS     | Setup    | Slowest logic |
| TT     | Baseline | Nominal       |
| FF     | Hold     | Fastest logic |


## PVT Corners in STA

This conceptual image visualizes the idea of Process, Voltage, and Temperature (PVT) corners. These corners represent different environmental and manufacturing conditions. The diagram shows that the Static Timing Analysis is performed for each of these corners to ensure the design's robustness across all expected operating conditions.

<img width="1563" height="963" alt="PVT Corners" src="https://github.com/user-attachments/assets/7df1e1b2-3a08-4aea-8340-7d769fd32a35" />


---

## 🎓 Learning Outcomes

* **Pre-route timing ≠ post-route timing** → Wires introduce actual resistance & capacitance → delay increases
* **SPEF is required** → Without parasitic annotation, STA is **not usable for tapeout**
* **PVT corners matter** → Real silicon varies in speed → timing must be safe for all conditions
* **Routing changes critical paths** → Long wires, crosstalk, clock tree delays shift bottlenecks

---

## 📝 Final Reflections

Stage-7 and Stage-8 provide:

* Complete understanding of **post-route timing behavior**
* Real PVT effects on delays
* True critical path characterization
* Industry-level STA workflow knowledge

This completes the VSDBabySoC timing signoff learning path—from RTL to **post-layout, parasitic-accurate timing closure**.


---


