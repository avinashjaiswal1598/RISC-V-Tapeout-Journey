# 🚀 RISC-V Tapeout Journey – picorv32a ASIC Flow Documentation

This document summarizes the ASIC design flow of the `picorv32a` RISC-V core using OpenLane and Sky130 PDK, following the automated flow from RTL to GDSII, including synthesis, floorplanning, placement, CTS, routing, and signoff.

---

## 📚 Table of Contents
1. [🎯 Objective](#objective)  
2. [🎓 Learning Goals](#learning-goals)  
3. [🛠 Lab Setup & Environment](#lab-setup--environment)  
4. [🧩 Workshop Workflow](#workshop-workflow)  
   1. [💻 RTL Synthesis](#rtl-synthesis)  
   2. [📐 Floorplanning](#floorplanning)  
   3. [📦 Placement](#placement)  
   4. [⏱ Clock Tree Synthesis (CTS)](#cts)  
   5. [🔌 Routing](#routing)  
   6. [✅ Signoff / Verification](#signoff)  
   7. [📊 Summary & Metrics](#summary)  
5. [💡 Reflections & Learnings](#reflections--learnings)  
6. [📖 References & Resources](#references--resources)  
7. [📝 Deliverables Summary](#deliverables-summary)  

---

## 🎯 1. Objective

Document and understand the ASIC design flow for the `picorv32a` RISC-V core, using OpenLane and Sky130 PDK, from RTL to signoff.

---

## 🎓 2. Learning Goals

- Understand the complete ASIC flow: Synthesis → Floorplanning → Placement → CTS → Routing → Signoff.
- Learn to analyze log files at each step.
- Gain experience in OpenLane automated flow.
- Learn to prepare GitHub-ready documentation with screenshots and logs.

---

## 🛠 3. Lab Setup & Environment

**Prerequisites:**

- OpenLane Docker container (v1.0.2) or local installation.
- Sky130 PDK installed and environment variables set.
- RTL design `picorv32a.v` placed in `designs/picorv32a/src/`.

**Environment Variables:**
```bash
export PDK_ROOT=/home/avinash/.ciel/ciel/sky130/versions/0fe599b2afb6708d281543108caf8310912f54af
export PDK=sky130A
export STD_CELL_LIBRARY=sky130_fd_sc_hd
export DESIGN_DIR=/openlane/designs/picorv32a
export OPENLANE_ROOT=/openlane
source ~/.bashrc
```

**Lab Setup Steps:**
1. Launch OpenLane container 🐳.
2. Verify environment variables with `echo $PDK_ROOT` and `echo $OPENLANE_ROOT`.
3. Place RTL and config files in the design directory.
4. Prepare a `screenshots/` folder 📸 and `logs/` folder 📂 for documentation.

---

## 🧩 4. Workshop Workflow

The ASIC flow is executed using OpenLane automated scripts. Below is the structured workflow from synthesis to signoff.

### 💻 4.1 RTL Synthesis (Steps 1–2)
**Purpose:** Convert RTL Verilog to gate-level netlist while optimizing for timing and area.

**Commands:**
```bash
./flow.tcl -design picorv32a -tag hd_run -overwrite
```

**Steps and Logs:**
| Step | Description | Log File |
|------|-------------|----------|
| 1 | Synthesis | [1-synthesis.log](logs/synthesis/1-synthesis.log) |
| 2 | Single-Corner STA | [2-sta.log](logs/synthesis/2-sta.log) |

**Screenshot Placeholder:**
`![Synthesis Step](screenshots/synthesis_step2.png)`

### 📐 4.2 Floorplanning (Steps 3–6)
**Purpose:** Define chip layout area, IO placement, and power grid planning.

**Steps and Logs:**
| Step | Description | Log File |
|------|-------------|----------|
| 3 | Initial Floorplanning | [3-initial_fp.log](logs/floorplan/3-initial_fp.log) |
| 4 | IO Placement | N/A |
| 5 | Tap / Decap insertion | [5-tap.log](logs/floorplan/5-tap.log) |
| 6 | Power Distribution Network (PDN) | [6-pdn.log](logs/floorplan/6-pdn.log) |

**Screenshot Placeholder:**
`![Floorplan Step](screenshots/floorplan_step6.png)`

### 📦 4.3 Placement (Steps 7–11)
**Purpose:** Place standard cells and macros while maintaining timing and routability.

**Steps and Logs:**
| Step | Description | Log File |
|------|-------------|----------|
| 7 | Global Placement | [7-global.log](logs/placement/7-global.log) |
| 8 | STA after GPL | [8-gpl_sta.log](logs/placement/8-gpl_sta.log) |
| 9 | Placement Resizer | [9-resizer.log](logs/placement/9-resizer.log) |
| 10 | Detailed Placement | [10-detailed.log](logs/placement/10-detailed.log) |
| 11 | STA after DPL | [11-dpl_sta.log](logs/placement/11-dpl_sta.log) |

**Screenshot Placeholder:**
`![Placement Step](screenshots/placement_step11.png)`

### ⏱ 4.4 Clock Tree Synthesis (CTS) (Steps 12–14)
**Purpose:** Generate balanced clock tree to minimize skew and insertion delay.

**Steps and Logs:**
| Step | Description | Log File |
|------|-------------|----------|
| 12 | CTS | [12-cts.log](logs/cts/12-cts.log) |
| 13 | STA after CTS | [13-cts_sta.log](logs/cts/13-cts_sta.log) |
| 14 | Placement Resizer Timing Optimization | [14-resizer.log](logs/cts/14-resizer.log) |

**Screenshot Placeholder:**
`![CTS Step](screenshots/cts_step14.png)`

### 🔌 4.5 Routing (Steps 15–28)
**Purpose:** Connect all placed cells and macros electrically while satisfying design rules and timing constraints.

**Steps and Logs (selected):**
| Step | Description | Log File |
|------|-------------|----------|
| 15 | Global Routing Resizer – Design | [15-resizer_design.log](logs/routing/15-resizer_design.log) |
| 16 | STA | [16-rsz_design_sta.log](logs/routing/16-rsz_design_sta.log) |
| 17 | Global Routing Resizer – Timing | [17-resizer_timing.log](logs/routing/17-resizer_timing.log) |
| 18 | STA | [18-rsz_timing_sta.log](logs/routing/18-rsz_timing_sta.log) |
| 19 | I/O Diode Insertion | [19-io_diodes.log](logs/routing/19-io_diodes.log) |
| 27 | Detailed Routing | [27-detailed.log](logs/routing/27-detailed.log) |
| 28 | Wire Length Check | [28-wire_lengths.log](logs/routing/28-wire_lengths.log) |

**Screenshot Placeholder:**
`![Routing Step](screenshots/routing_step28.png)`

### ✅ 4.6 Signoff / Verification (Steps 29–46)
**Purpose:** Verify design correctness, DRC/LVS compliance, parasitic extraction, IR drop, and generate final GDSII.

**Steps and Logs (selected):**
| Step | Description | Log File |
|------|-------------|----------|
| 29 | SPEF Extraction (min) | [29-parasitics_extraction.min.log](logs/signoff/29-parasitics_extraction.min.log) |
| 33 | SPEF Extraction (nom) | [33-parasitics_extraction.nom.log](logs/signoff/33-parasitics_extraction.nom.log) |
| 34 | Multi-Corner STA (nom) | [34-rcx_mcsta.nom.log](logs/signoff/34-rcx_mcsta.nom.log) |
| 35 | STA (nom) | [35-rcx_sta.log](logs/signoff/35-rcx_sta.log) |
| 36 | IR Drop | [36-irdrop.log](logs/signoff/36-irdrop.log) |
| 37 | Magic GDSII & LEF | [37-gdsii.log](logs/signoff/37-gdsii.log), [37-lef.log](logs/signoff/37-lef.log) |
| 44 | Magic DRC | [44-drc.log](logs/signoff/44-drc.log) |
| 46 | ERC | [46-erc_screen.log](logs/signoff/46-erc_screen.log) |

**Screenshot Placeholder:**
`![Signoff Step](screenshots/signoff_step46.png)`

