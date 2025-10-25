# 🌟 Week 5 – OpenROAD Flow Setup & Floorplan + Placement  
**Repository:** [RISC-V-Tapeout-Journey](https://github.com/avinashjaiswal1598/RISC-V-Tapeout-Journey)  
**Author:** Avinash Jaiswal  

---

## 🧭 Objective  
Set up the **OpenROAD Flow Scripts** environment and execute the **Floorplan** and **Placement** stages of the physical design flow.  
This marks the transition from transistor-level simulation (Week 4) to the physical backend, where RTL is turned into a silicon layout.

---

## 📑 Table of Contents
- [🧭 Objective](#-objective)
- [🔍 Why This Task Matters](#-why-this-task-matters)
- [📚 References](#-references)
- [⚙️ Task Components](#-%EF%B8%8F-task-components)
- [🖼️ Outputs](#-%EF%B8%8F-outputs)
- [🧰 Troubleshooting & Learnings](#-troubleshooting--learnings)
- [🧠 Summary](#-summary)
- [🚀 Next Steps](#-next-steps)
- [✅ Deliverables Checklist](#-deliverables-checklist)

---

## 🔍 Why This Task Matters  
- Understands how physical constraints affect timing, area, and performance.  
- Builds knowledge of backend flow (Floorplanning → Placement → Routing).  
- Strengthens the connection between transistor-level and layout-level VLSI concepts.  
- Forms the foundation for advanced steps like **Clock Tree Synthesis (CTS)** and **Routing**.

---

## 📚 References  
- [OpenROAD Flow Scripts (Official Repo)](https://github.com/The-OpenROAD-Project/OpenROAD-flow-scripts)  
- [VSD-HDP Reference (Day 14)](https://github.com/spatha0011/spatha_vsd-hdp/blob/main/Day14/README.md)  
- [Kunal Ghosh – VSD GitHub](https://github.com/kunalg123)  

---

## ⚙️ Task Components  

<details>
<summary><strong>1️⃣ Install OpenROAD Flow Scripts</strong></summary>

```bash
# Clone OpenROAD Flow Scripts
 git clone https://github.com/The-OpenROAD-Project/OpenROAD-flow-scripts.git
 cd OpenROAD-flow-scripts

# Install all prerequisites
 sudo ./setup.sh


# Verify installation
 ./openroad -version
```

✅ **Expected Result:** OpenROAD version displayed successfully.  

<img width="800" height="231" alt="2kaylayout" src="https://github.com/user-attachments/assets/0e875de7-3897-4b2e-920d-8f756867fb4f" />

<img width="600" height="672" alt="openroad installation" src="https://github.com/user-attachments/assets/bb182dfd-451d-46c6-9064-a3cfdeaa3f55" />

---

<details>
<summary><strong>2️⃣ Execute Floorplan + Placement (Only These Stages)</strong></summary>

```bash
 
```

✅ **Verify:**  
- Core area and die dimensions generated.  
- Standard cells placed successfully.  
- Logs created under `logs/` directory.  

 
- Terminal logs for floorplan & placement completion.  
- Floorplan and placement layout images.  
</details>

---

## 🖼️ Outputs  

### 🧩 Floorplan View  
![Floorplan view](./images/week5_floorplan.png)  
*Figure 1 – Core and die regions after floorplanning.*

### 🧱 Placement View  
![Placement view](./images/week5_placement.png)  
*Figure 2 – Standard cell placement showing proper utilization.*

### 🧾 Logs & Confirmation  
![Terminal log](./images/week5_terminal_log.png)  
*Figure 3 – OpenROAD log confirming completion of Floorplan & Placement.*

---

## 🧰 Troubleshooting & Learnings  

Setting up OpenROAD Flow Scripts was the most challenging yet insightful phase so far. The process tested my patience, problem-solving, and system-level understanding — skills essential for real-world VLSI development.  

- **🔧 Long Setup & Slow Build:**  
  The `sudo ./setup.sh` process took hours due to limited VM resources. I monitored logs patiently and learned about internal dependencies.  
  ➤ *Learning:* Toolchain compilation time reflects dependency depth; patience and system monitoring are key.  

- **⚙️ Dependency Conflicts (CMake, SWIG, TCL):**  
  Build failures due to version mismatches were resolved by verifying versions (`cmake --version`, `swig --version`) and updating `.bashrc` paths.  
  ➤ *Learning:* Environment consistency ensures smooth builds.  

- **💾 VM Storage & Partition Issue:**  
  While resizing `/dev/sda2`, I faced “device busy / invalid superblock” errors. Using `df -h`, I identified partition issues and safely restored GUI via TTY commands.  
  ➤ *Learning:* Linux disk operations require precision and calm troubleshooting.  

- **🚀 Memory Limitation During Flow Execution:**  
  Added swap memory to overcome physical RAM limits, enabling flow completion.  
  ➤ *Learning:* Efficient resource management is critical for EDA workloads.  

- **🧹 Clean Reinstallation Decision:**  
  When inconsistencies persisted, I started from scratch. This clean approach led to stable results.  
  ➤ *Learning:* A fresh start often saves more time than patching broken setups.  

> 🧠 *Overall Reflection:* This week enhanced my debugging mindset and reinforced the importance of patience, structured analysis, and logical problem-solving in complex toolchains.

---

## 🧠 Summary  
Successfully installed OpenROAD Flow Scripts and executed **Floorplan** and **Placement** stages.  
Overcame memory and dependency challenges with systematic debugging and clean environment handling.  
Generated valid DEF files and visual layouts confirming correct placement.  

---

## 🚀 Next Steps  
- Proceed to **Clock Tree Synthesis (CTS)** and **Routing** in Week 6.  
- Optimize floorplan parameters for congestion-free placement.  
- Study timing analysis after placement to improve QoR (Quality of Results).  

---

## ✅ Deliverables Checklist  
| Task | Evidence | Status |
|------|-----------|--------|
| OpenROAD installed successfully | Screenshot of `openroad -version` | ☐ |
| Floorplan executed | Terminal log + image | ☐ |
| Placement executed | Terminal log + layout image | ☐ |
| Summary + Observations added | Markdown updated | ☐ |

---

> 🏁 *By the end of Week 5, the OpenROAD setup and Floorplan + Placement stages were successfully executed, establishing a solid foundation for complete backend flow mastery.*

