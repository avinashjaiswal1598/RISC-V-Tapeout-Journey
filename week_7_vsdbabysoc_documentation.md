# VSDBabySoC – Week 7 Documentation

A **detailed, beginner‑friendly, and professionally structured** guide for understanding, synthesizing, and preparing the *VSDBabySoC* design for physical design using OpenROAD. This documentation follows the Week‑7 task requirements and expands each section with clarity, conceptual depth, and practical steps.

---

# 📘 Table of Contents

1. [Introduction to VSDBabySoC](#introduction-to-vsdbabysoc)
2. [Why VSDBabySoC?](#why-vsdbabysoc)
3. [SoC Architecture Overview](#soc-architecture-overview)
4. [Repository Setup](#repository-setup)
5. [Directory Structure](#directory-structure)
6. [Understanding RTL Components](#understanding-rtl-components)
7. [Macro Files (.lib, .lef, .gds)](#macro-files-lib-lef-gds)
8. [Include Files (.vh) and Their Role](#include-files-vh-and-their-role)
9. [Preparing the Workspace](#preparing-the-workspace)
10. [Creating Synthesizable `rvmyth.v`](#creating-synthesizable-rvmythv)
11. [Final Yosys Synthesis Script](#final-yosys-synthesis-script)
12. [Running Synthesis](#running-synthesis)
13. [Generated Outputs](#generated-outputs)
14. [Understanding the SPEF File](#understanding-the-spef-file)
15. [Conclusion](#conclusion)

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

# 🏛️ SoC Architecture Overview

The VSDBabySoC integrates:

### ✔ RVMyth RISC-V Core
Originally written in TL-Verilog → compiled into synthesizable Verilog.

### ✔ Clock Gating Logic
Used for low-power operation.

### ✔ Pseudo Random Generator
Provides random inputs for DAC testing.

### ✔ Hard Macros
- **avsdpll** → Generates system clock
- **avsddac** → Converts digital random values into analog output

**⚠ Note:** PLL & DAC have their *own layouts*, timing models, and behavioral models. They cannot be synthesized.

---

# 📁 Repository Setup

Before synthesis, a clean workspace was created:

```
BabySoC/
 ├── rtl/
 ├── libs/
 ├── include/
 ├── constraints/
 ├── scripts/
 ├── outputs/reports/
```

Each folder holds logically separated assets for the ASIC flow.

---

# 📂 Directory Structure Explained

### `rtl/`
Contains only **synthesizable RTL**:
- `vsdbabysoc.v`
- `rvmyth.v`
- `clk_gate.v`
- `pseudo_rand.sv`
- `pseudo_rand_gen.sv`

### `libs/`
Contains timing models:
- Standard cell library: `sky130_fd_sc_hd__tt_025C_1v80.lib`
- Macro timing: `avsdpll.lib`, `avsddac.lib`

### `include/`
Contains TL-Verilog expansion headers:
- `sp_verilog.vh`
- `sp_default.vh`
- `sandpiper.vh`
- `sandpiper_gen.vh`

### `scripts/`
Contains the Yosys synthesis script.

### `outputs/`
Contains synthesis outputs & reports.

---

# 🔍 Understanding RTL Components

### **1. `vsdbabysoc.v`** (Top module)
Integrates the entire SoC:
- connects RVMyth core
- feeds pseudo-random signals
- interacts with DAC & PLL

### **2. RVMyth (`rvmyth.v`)**
Simplified RISC‑V-like processing element.

### **3. Clock Gate**
Used to demonstrate low-power clocking.

### **4. `pseudo_rand.sv` / `pseudo_rand_gen.sv`**
Generates random data for the DAC.

### **5. DAC/PLL RTL are NOT synthesizable**
They remain **black boxes** using:
- `.lib` → timing
- `.lef` → placement
- `.gds` → final layout

---

# 🧱 Macro Files (.lib, .lef, .gds)
These represent the **hard macros** of the design.

### ✔ `.lib` – Timing Models
Used in synthesis and OpenSTA for:
- delay estimation
- setup/hold checks

### ✔ `.lef` – Layout Template
Used in floorplanning for:
- block dimensions
- pin locations

### ✔ `.gds` – Final Layout
Used in final chip-level GDS stitching.

---

# 📌 Include Files (.vh) and Their Role
The BabySoC uses TL-Verilog originally.
During RVMyth expansion, these headers are referenced.

Examples:
- `sp_verilog.vh`
- `sandpiper.vh`

They contain:
- Macro expansions
- Simulation constructs
- Code parameters

Without them, Yosys will throw *include not found* errors.

---

# 🛠️ Preparing the Workspace

### Copy RTL
```bash
cp ~/vsdbabysoc/src/module/*.v ~/BabySoC/rtl/
cp ~/vsdbabysoc/src/module/*.sv ~/BabySoC/rtl/
```

### Copy Libraries
```bash
cp ~/vsdbabysoc/src/lib/*.lib ~/BabySoC/libs/
```

### Copy Include Files
```bash
mkdir -p ~/BabySoC/include
cp ~/vsdbabysoc/src/include/*.vh ~/BabySoC/include/
```

---

# 🧪 Creating Synthesizable `rvmyth.v`

Since SandPiper tool may not be installed locally, a **precompiled synthesizable `rvmyth.v`** was added.

This version:
- does not require TL-Verilog
- works directly with Yosys
- matches the official VSD workshop flow

---

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

Week‑7 establishes strong foundations for:

- Understanding SoC-level integration
- Working with hard macros
- Managing TL-Verilog based include structures
- Synthesizing mixed RTL + macro designs
- Preparing for physical design
- Understanding SPEF's importance in timing closure

This repository now contains a **clean, professional, and educational** structure suitable for ASIC learning, portfolio use, and further extension into full RTL-to-GDS flows.

---

💡 *Next steps in later weeks: OpenROAD floorplanning, placement, CTS, routing, parasitic extraction, SPEF generation, STA, and GDS export.*

