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
 cd ~/VSDBabySoC
 make synth
```


### 🧩 Step 2: Run Gate-Level Simulation (GLS)

```bash
 cd ~/VSDBabySoC
 make post_synth_sim
```

📸 *<img width="1153" height="385" alt="Screenshot from 2025-10-11 14-36-43" src="https://github.com/user-attachments/assets/9d04d596-c226-44e5-87bc-bcdafa326cec" />
*

### 🧩 Step 3: View and Compare Waveforms

Open GTKWave to view post-synthesis output and compare it with Week 2 functional simulation.

```bash
 gtkwave output/post_synth_sim/post_synth_sim.vcd
```

📸 *<img width="1280" height="800" alt="Screenshot from 2025-10-11 14-46-18" src="https://github.com/user-attachments/assets/9799a4a8-b1a2-4f28-927b-4c4aa38338f0" />
*

# Yosys report
<img width="959" height="350" alt="Screenshot from 2025-10-11 14-25-40" src="https://github.com/user-attachments/assets/ee5c15f2-0590-4ddd-a630-0bb370cffdf2" />

# clkgate
<img width="820" height="522" alt="Screenshot from 2025-10-11 14-27-02" src="https://github.com/user-attachments/assets/4c69abfd-d7d7-4473-957b-c05d80097b2f" />

# rvmyth
<img width="491" height="469" alt="Screenshot from 2025-10-11 14-27-41" src="https://github.com/user-attachments/assets/8772cdb4-3fac-406d-827b-17d0e145318e" />

# vsdbabysoc
<img width="791" height="306" alt="Screenshot from 2025-10-11 14-29-00" src="https://github.com/user-attachments/assets/82010178-98d0-4f67-9dbf-b962d4f46d73" />

# design hierarchy
<img width="932" height="639" alt="Screenshot from 2025-10-11 14-29-35" src="https://github.com/user-attachments/assets/06a88e94-2856-47c0-898f-b4860135d2a8" />

#checkpass
<img width="872" height="184" alt="Screenshot from 2025-10-11 14-30-21" src="https://github.com/user-attachments/assets/8f4122f0-4ef1-4238-bc34-f7cadd55a6ba" />

# printing stastistics
# vsdbabysoc
<img width="842" height="690" alt="Screenshot from 2025-10-11 14-33-39" src="https://github.com/user-attachments/assets/44e727ab-408b-4b18-a8b5-a05e828011aa" />

<img width="836" height="615" alt="Screenshot from 2025-10-11 14-35-08" src="https://github.com/user-attachments/assets/1553117e-a613-46f9-9ac4-b4b35946039b" />


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
- **Slack:** Difference between arrival time and expected time.
- min difference(min slack)= Arrival time - Required time
- max difference(max slack)=  Required time - Arrival time
  - Positive Slack → Timing met ✅
  - Negative Slack → Timing violation ⚠️
- **Clock Definition:** Defines the main timing reference; usually added via `create_clock` in STA.
- **Timing Path Components:** Launch flop → Combinational logic → Capture flop.
- **Types of Setup/hold analysis:**
  - reg2reg
  - in2reg
  - reg2out
  - in2out
  - clock gating
  - recovery/removal
  - data-to-data
  - latch(time borrow/time given)
- **Setup & Hold Violations:**
  - Setup violation → Path too slow
  - Hold violation → Path too fast
- **Fix Techniques:**
  - Adjust clock skew
  - Buffer insertion
  - Resize cells
- **Path-Based Analysis (PBA):** Analyzes individual critical paths instead of global constraints(it is the best time at node).
- **Graph Based Analysis (GBA):** It is the worst time at node.

📄 *This course provided a foundational understanding of STA terms, timing paths, and how OpenSTA performs analysis using netlist and SDC constraints.*

---

## 📊 Part 3 – Generate Timing Graphs with OpenSTA

### 🧠 Objective:
Perform basic timing analysis on the synthesized BabySoC netlist using OpenSTA to identify critical paths and calculate setup/hold slack.

### 🧰 Installation (if not already installed)
```bash
sudo apt-get install opensta -y
```

### 🗂️ Step 1: Load Netlist and Constraints and Perform Timing Analysis
Create a file named `run_sta.tcl`:

```bash
 cd ~/VSDBabySoC
 make sta
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



### 🧾 Step 3: Generate Timing Graphs
To visualize setup and hold paths:
```tcl
report_timing -delay_type max -sort_by slack -max_paths 3
```



### 🧩 Observations:
- **Critical Path:** 
- **Slack Value:** 
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
