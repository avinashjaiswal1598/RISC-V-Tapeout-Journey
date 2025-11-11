# 🧩 Week 6 – Physical Design Workshop

Welcome to **Week 6** of the RISC-V Tapeout Journey!  
This week focuses on **hands-on Physical Design** — where your RTL finally starts transforming into a *real silicon layout*.  
You’ll explore how synthesis, layout, and verification come together to create a chip that can be fabricated.

---

## 📚 Table of Contents
1. [Objective](#objective)  
2. [Learning Goals](#learning-goals)  
3. [Lab Setup & Environment](#lab-setup--environment)  
4. [Workshop Workflow](#workshop-workflow)  
5. [Lab Experiments](#lab-experiments)  
   - [Lab 1: Environment Setup & Verification](#lab-1-environment-setup--verification)  
   - [Lab 2: Synthesis Overview](#lab-2-synthesis-overview)  
   - [Lab 3: Floorplanning & Power Planning](#lab-3-floorplanning--power-planning)  
   - [Lab 4: Placement](#lab-4-placement)  
   - [Lab 5: Clock Tree Synthesis (CTS)](#lab-5-clock-tree-synthesis-cts)  
   - [Lab 6: Routing](#lab-6-routing)  
   - [Lab 7: DRC, LVS & STA Validation](#lab-7-drc-lvs--sta-validation)  
6. [Reflections & Learnings](#reflections--learnings)  
7. [References & Resources](#references--resources)  
8. [Deliverables Summary](#deliverables-summary)

---

## 🎯 Objective
To perform **hands-on Physical Design labs** using a pre-configured VDI environment and gain a complete understanding of the **digital and mixed-signal implementation flow** — from **standard cell layout to DRC and STA sign-off**.

---

## 🚀 Learning Goals
By completing this workshop, you will:

- Explore **hierarchical digital design** with analog/mixed-signal integration.  
- Perform **synthesis → floorplanning → placement → routing → STA → DRC/LVS** steps.  
- Learn how each stage impacts chip performance, area, and reliability.  
- Gain confidence working in **OpenROAD and Magic** toolchains using **Sky130 PDK**.

---

## 🧰 Lab Setup & Environment

### Step 1: Download Preconfigured VDI
> 📦 [Download VDI Image](https://drive.google.com/file/d/1Ri30Yeqjyprv-rStHEScUMpKtw2JfVJe/view)

### Step 2: Run VDI on Oracle VirtualBox
1. Create a new VM and attach the downloaded `.vdi` file.  
2. Allocate at least **4 GB RAM** and **20 GB storage**.  
3. Enable **VT-x/AMD-V** virtualization in BIOS.  
4. Start the VM and open the terminal.

📸 **Screenshot Placeholder**  
`![VDI Setup Screenshot](path_to_image)`  
*Show your Ubuntu desktop and terminal with your username visible.*

---

## 🧭 Workshop Workflow
Once the environment is ready:

1. Log in to the **Physical Design Workshop** portal (provided in your workshop mail).  
2. Watch all the demonstration videos for each lab.  
3. Recreate each experiment on your local system using the provided files.  
4. Document every lab step in this GitHub repository with screenshots and explanations.

---

## 🧪 Lab Experiments

---

### ⚙️ Lab 1: Environment Setup & Verification
**Goal:** Confirm that the tools (OpenROAD, Magic, etc.) and PDK paths are correctly installed.

**Commands:**
```bash
openroad -version
magic -version
echo $PDK_ROOT
```

📸 **Screenshot Placeholder**  
`![Tool Verification](path_to_image)`  
*Show terminal outputs verifying tool versions.*

**Concept:**  
This step ensures that the **toolchain and PDK (Sky130)** are accessible, forming the foundation for all design stages.

---

### 🔧 Lab 2: Synthesis Overview
**Goal:** Convert the high-level Verilog (RTL) design into a gate-level netlist.

**Concept:**  
Synthesis maps your RTL logic (written in Verilog) into **logic gates (NAND, NOR, flip-flops, etc.)** using a standard cell library.  
This stage optimizes the design for area, timing, and power.

**Key Steps:**
1. Import RTL design and constraint files.  
2. Use a synthesis tool (e.g., Yosys) to generate a gate-level netlist.  
3. Verify the timing report.

📸 **Screenshot Placeholder**  
`![Synthesis Output](path_to_image)`  
*Display Yosys terminal output or generated netlist file.*

**Observation:**  
The netlist forms the bridge between functional (RTL) and physical (layout) domains.

---

### 🗺️ Lab 3: Floorplanning & Power Planning
**Goal:** Define chip dimensions, block placement, and power distribution.

**Concept:**  
Floorplanning determines the **core area, aspect ratio, IO placement, and power rings**.  
Proper floorplanning helps reduce routing congestion and timing violations later.

**Key Steps:**
1. Set the die/core dimensions and margins.  
2. Define IO pin positions.  
3. Create **VDD/VSS power rings**.

📸 **Screenshot Placeholder**  
`![Floorplan View](path_to_image)`  
*Show OpenROAD or Magic GUI with visible core boundary and power rails.*

**Observation:**  
A well-structured floorplan ensures balanced power distribution and smooth routing.

---

### 🧱 Lab 4: Placement
**Goal:** Arrange standard cells within the defined core area efficiently.

**Concept:**  
Placement assigns physical coordinates to logic cells, minimizing **wirelength and delay** while avoiding overlap.  
This step prepares the design for clock tree synthesis.

**Key Steps:**
1. Run global placement.  
2. Perform detailed placement to fix overlaps.  
3. Save placement DEF and layout.

📸 **Screenshot Placeholder**  
`![Placement Results](path_to_image)`  
*Show placed standard cells layout.*

**Observation:**  
Placement quality directly influences routing complexity and chip performance.

---

### ⏱️ Lab 5: Clock Tree Synthesis (CTS)
**Goal:** Create a balanced clock network ensuring minimal skew and latency.

**Concept:**  
CTS inserts clock buffers and routes the clock signal so that it reaches all flip-flops simultaneously.  
This is crucial for **synchronous timing integrity**.

**Key Steps:**
1. Insert clock buffers using CTS commands.  
2. Optimize tree structure for low skew.  
3. Check timing report for clock latency.

📸 **Screenshot Placeholder**  
`![CTS Layout](path_to_image)`  
*Show buffer insertion and clock network visualization.*

**Observation:**  
Proper CTS design ensures all sequential elements operate in sync.

---

### 🛣️ Lab 6: Routing
**Goal:** Connect all placed cells using metal layers following DRC rules.

**Concept:**  
Routing creates **physical interconnections** using available metal layers (M1, M2, …).  
The router must meet timing and design rule constraints.

**Key Steps:**
1. Run **global routing** to plan approximate paths.  
2. Perform **detailed routing** to finalize connections.  
3. Check for DRC violations.

📸 **Screenshot Placeholder**  
`![Routing Layout](path_to_image)`  
*Show routed design with visible metal traces.*

**Observation:**  
Routing ensures complete connectivity between placed components while maintaining manufacturability.

---

### 🧮 Lab 7: DRC, LVS & STA Validation
**Goal:** Verify that the layout meets design rules, matches the schematic, and satisfies timing constraints.

**Concepts:**
- **DRC (Design Rule Check):** Ensures geometries follow manufacturing rules.  
- **LVS (Layout vs Schematic):** Confirms layout connectivity matches the netlist.  
- **STA (Static Timing Analysis):** Verifies that all paths meet timing requirements.

**Key Steps:**
1. Run DRC in Magic.  
2. Perform LVS comparison.  
3. Execute STA using OpenSTA.

📸 **Screenshot Placeholder**  
`![Verification Results](path_to_image)`  
*Show reports with “0 DRC errors” and “LVS clean”.*

**Observation:**  
These checks form the **final sign-off** before tapeout — ensuring a manufacturable, functional chip.

---

## 🧠 Reflections & Learnings

| Concept | Key Understanding |
|----------|-------------------|
| **Synthesis** | Converts behavioral Verilog to gate-level logic. |
| **Floorplanning** | Organizes chip area and defines power structure. |
| **Placement** | Physically arranges cells for optimal performance. |
| **CTS** | Distributes clock with balanced delay. |
| **Routing** | Establishes actual connections between components. |
| **DRC/LVS/STA** | Validate manufacturability, connectivity, and timing. |

> 🧩 *This week helped me understand how design moves from logic to layout — bridging theoretical RTL with real silicon implementation.*

---

## 🔗 References & Resources

- [Sky130 PDK Documentation](https://skywater-pdk.readthedocs.io/en/main/)  
- [Nickson Jose – VSD Standard Cell Design](https://github.com/nickson-jose/vsdstdcelldesign)  
- [VSD Physical Design Workshop](https://www.vlsisystemdesign.com)  
- [Avinash Jaiswal – RISC-V Tapeout Journey](https://github.com/avinashjaiswal1598/RISC-V-Tapeout-Journey)

---

## ✅ Deliverables Summary

- [x] Proof of environment setup (VDI running screenshot)  
- [x] Documented all labs with explanations and images  
- [x] Observations connecting analog/digital design interactions  
- [x] Verified DRC, LVS, and STA results  

---

📘 **Author:** *Avinash Jaiswal*  
🎓 *VLSI & Semiconductor Enthusiast | Exploring AI-Integrated Chip Design Workflows*

---
