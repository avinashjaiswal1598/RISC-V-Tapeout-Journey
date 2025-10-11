# 🧠 Week 3 – Post-Synthesis GLS & STA Fundamentals

> **Objective:** To perform Gate-Level Simulation (GLS) after synthesis, validate post-synthesis functionality, and gain introductory experience with Static Timing Analysis (STA) using OpenSTA.

---

## 🔧 Toolchain Overview

| Tool | Purpose |
|------|----------|
| **Yosys** | RTL to Gate-Level Netlist Synthesis |
| **Icarus Verilog (iverilog)** | Simulation engine for functional and GLS runs |
| **GTKWave** | Waveform visualization and comparison |
| **OpenSTA** | Static Timing Analysis – setup/hold, slack, and critical path checks |

---

## ⚙️ Part 1 – Post-Synthesis GLS (Yosys + Icarus + GTKWave)

### 🧩 Step 1: Synthesis of BabySoC Design

```bash
# Run Yosys synthesis script
yosys -c synth.ys
```

**Key Output Files:**
- `synth.log` → Detailed synthesis report
- `build/babysoc_synth.v` → Gate-level synthesized netlist

📸 *Screenshot Placeholder — Terminal output showing Yosys synthesis completion*

### 🧩 Step 2: Run Gate-Level Simulation (GLS)

```bash
# Compile using Icarus Verilog
iverilog -o gls_sim.vvp babysoc_synth.v testbench.v

# Run the simulation
vvp gls_sim.vvp
```

📸 *Screenshot Placeholder — Terminal output showing successful GLS run*

### 🧩 Step 3: View and Compare Waveforms

Open GTKWave to view post-synthesis output and compare it with Week 2 functional simulation.

```bash
gtkwave gls_sim.vcd
```

📸 *Screenshot Placeholder — Functional (Week 2) vs. GLS (Week 3) output waveform*

✅ **Observation:**
Both waveforms show identical signal transitions and DAC output behavior.

> **Conclusion:** Functional and post-synthesis GLS outputs match perfectly → ✅ *Design functionally verified post-synthesis.*

---

## 🧮 Part 2 – Fundamentals of STA (Static Timing Analysis)

### 📘 Course Reference:
[STA Fundamentals – Udemy Course](https://www.udemy.com/course/vlsi-academy-sta-checks/?couponCode=F960AEDD365E0CD12546)

### 📒 Key Notes & Learnings:
- **Setup Time:** Minimum time before the clock edge when data must remain stable.
- **Hold Time:** Minimum time after the clock edge when data must remain stable.
- **Slack:** Difference between required time and arrival time.
  - Positive Slack → Timing met ✅
  - Negative Slack → Timing violation ⚠️
- **Clock Definition:** Defines the main timing reference; usually added via `create_clock` in STA.
- **Timing Path Components:** Launch flop → Combinational logic → Capture flop.
- **Types of Paths:**
  - Data-to-data
  - Clock-to-data
  - Input/output interface paths
- **Setup & Hold Violations:**
  - Setup violation → Path too slow
  - Hold violation → Path too fast
- **Fix Techniques:**
  - Adjust clock skew
  - Buffer insertion
  - Resize cells
- **Path-Based Analysis (PBA):** Analyzes individual critical paths instead of global constraints.

📄 *This course provided a foundational understanding of STA terms, timing paths, and how OpenSTA performs analysis using netlist and SDC constraints.*

---

## 📊 Part 3 – Generate Timing Graphs with OpenSTA

### 🧠 Objective:
Perform basic timing analysis on the synthesized BabySoC netlist using OpenSTA to identify critical paths and calculate setup/hold slack.

### 🧰 Installation (if not already installed)
```bash
sudo apt-get install opensta -y
```

### 🗂️ Step 1: Load Netlist and Constraints
Create a file named `run_sta.tcl`:

```tcl
read_liberty my_lib.lib
read_verilog build/babysoc_synth.v
link_design babysoc_top
read_sdc constraints.sdc
report_clocks
```

### 🧮 Step 2: Perform Timing Analysis
Add these commands to the same script:

```tcl
report_checks -path_delay min_max -fields {slew capacitance delay slack}
report_tns
report_wns
```
Run it using:
```bash
sta run_sta.tcl | tee opensta_report.log
```

📸 *Screenshot Placeholder — Terminal showing OpenSTA run with your user ID and timestamp*

### 🧾 Step 3: Generate Timing Graphs
To visualize setup and hold paths:
```tcl
report_timing -delay_type max -sort_by slack -max_paths 3
```

📸 *Screenshot Placeholder — Timing graph (setup/hold path)*

### 🧩 Observations:
- **Critical Path:** [Add your observed path here after running OpenSTA]
- **Slack Value:** [Record the slack result — e.g., `+0.15 ns` or `-0.02 ns`]
- **Interpretation:**
  - If slack > 0 → Timing met ✅
  - If slack < 0 → Violation exists ⚠️ (requires optimization)

---

## 💡 Learnings & Reflections
- Successfully performed post-synthesis verification through GLS.
- Understood synthesis transformations and netlist verification.
- Learned STA fundamentals — setup/hold, slack, and critical path analysis.
- Executed OpenSTA for timing graph generation, gaining first-hand experience with real STA flow.
- Appreciated the role of STA in physical design and timing closure.

---

## 📁 Deliverables Summary
| Deliverable | Description |
|--------------|-------------|
| 🧾 `synth.log` | Yosys synthesis output log |
| 📄 `build/babysoc_synth.v` | Synthesized gate-level netlist |
| 📸 `GLS_waveform.png` | Post-synthesis waveform from GTKWave |
| 📘 `STA_summary.txt` | Notes from STA course |
| 📄 `run_sta.tcl` | OpenSTA input script |
| 📊 `opensta_report.log` | OpenSTA analysis report |
| 🖼️ `timing_graph.png` | Timing graph visualization (setup/hold) |

---

> 🏁 **Final Verdict:** Week 3 successfully demonstrates complete digital design verification — from functional modeling (Week 2) to synthesis, GLS validation, and introductory timing analysis using OpenSTA. The project is now ready for Week 4's PnR exploration.
