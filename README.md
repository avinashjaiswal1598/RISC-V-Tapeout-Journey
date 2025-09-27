# 🚀 RISC-V Tapeout Program 
<img width="733" height="491" alt="image" src="https://github.com/user-attachments/assets/71c03229-039a-4104-b87d-4fdc287845ad" />

Dive into my **RISC-V Tapeout Journey** — a hands-on exploration of chip design and tapeout fundamentals.
This repository neatly captures my **week-by-week** and **day-by-day** progress, making it easy to follow the complete learning path from basics to implementation.

## 📑 Quick Navigation
- [Week 0 - Toolchain Installation](#week-0--Toolchain Installation)  
- [Week 1 – RTL Design & Synthesis Foundations](#week-1--RTL Design & Synthesis Foundations)  
  - [Day 1 –Introduction to Verilog RTL Design & Synthesis](#day-1--Introduction to Verilog RTL Design & Synthesis)  
  - [Day 2 – Timing Libs & Flop Coding Styles](#day-2--timing-libs--flop-coding-styles)
  - [Day 3 – Combinational & Sequential Optimizations](#day-3--combinational--sequential-optimizations)  
  - [Day 4 – GLS & Blocking vs Non-Blocking](#day-4--gls--blocking-vs-non-blocking)  
  - [Day 5 – DFF Designs, Resets & Testbenches](#day-5--dff-designs-resets--testbenches)  

---

## 📦 Week 0 –: Toolchain Installation

Welcome to the Week 0 setup! This week focuses on installing essential EDA tools for the RISC-V tapeout project.

<details>
  <summary>Expand to View Details</summary>
## 🚦 Objective
Get your environment ready for the RISC-V tapeout journey by installing and testing the full toolchain for this course including Yosys, GTKWave, Iverilog, OpenSTA, Ngspice, Magic, OpenLANE, this repository aims to empower learners to:

- Understand and navigate the complete EDA tool ecosystem used in open-source VLSI design and RISC-V tapeout workflows.
- Confidently use each tool for tasks such as static timing analysis (OpenSTA), circuit simulation (Ngspice), physical layout design (Magic), and RTL-to-GDSII flow automation (OpenLANE).
- Apply foundational principles of digital design, verification, and backend physical implementation through hands-on tool usage.
- Develop troubleshooting skills to identify and resolve common setup and runtime issues.


## 🗂️ Tasks
- Install Yosys, GTKWave, Iverilog, OpenSTA, Ngspice, Magic, OpenLANE on Ubuntu
- Validate installations with sample command runs
- Document any issues faced and their fixes
  

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
sudo apt install gtkwave
```

### Iverilog
**Iverilog:** A free Verilog simulation and synthesis tool that compiles and runs hardware description language designs for testing and verification.
```
sudo apt install iverilog
```

## OpenSTA - Static Timing Analyzer

OpenSTA is a fast and open-source Static Timing Analyzer for VLSI designs.

### Installation (Ubuntu 22.04+)
```
sudo apt update
sudo apt install opensta
opensta --version # Verify installation
```

### Quick Start
Run `opensta` on your SDC and SPEF files to analyze timing.

---

## Ngspice - Circuit Simulator

Ngspice simulates analog electronic circuits using SPICE.

### Installation
```
sudo apt update
sudo apt install ngspice
ngspice -v # Check version
```

### Basic Test
Run `ngspice` and load a sample test circuit file `.cir`.

---

## Magic VLSI - Layout Tool

Magic is a classic, easy-to-use VLSI layout tool.

### Installation from source
```
sudo apt update
sudo apt install git build-essential tcl-dev tk-dev libx11-dev
git clone https://github.com/RTimothyEdwards/magic.git
cd magic
./configure
make
sudo make install
magic -version
```

### Usage
Launch `magic` and start creating or editing layouts.

---

## OpenLANE - RTL to GDSII Flow Automation

OpenLANE automates the full digital ASIC flow.

### Installation via Docker
```
git clone https://github.com/The-OpenROAD-Project/OpenLane.git
cd OpenLane
./quick_start.sh
```

### Running a Design
Follow [OpenLANE Docs](https://openlane.readthedocs.io/) for detailed workflows.

---

## 🛠️ Troubleshooting & Tips
- Common errors & how to solve them
  - If a package is missing, try updating your package list or check tool documentation.
- For source installation, see respective official guides.

## 🌟 Skills Gained and Deliverables
- [x] Linux package management
- [x] EDA tools environment setup
- [x] Basic verification of setup

## 📚 References & Further Reading
- [VSD Official Resources](https://www.vlsisystemdesign.com/docs/)
- [Yosys Manual](https://yosyshq.readthedocs.io/)
- [GTKWave Guide](http://gtkwave.sourceforge.net/)
- [Icarus Verilog Docs](http://iverilog.icarus.com/)


</details>
---
# RISC-V Tapeout Journey - Week 1

## Week 1: RTL Design & Synthesis Foundations

### DAY 1 – Introduction to Verilog RTL Design & Synthesis

<details>
#### a. Introduction to Open Source Simulator: Icarus Verilog (Iverilog)
- **Objective:**  
  Gain familiarity with Icarus Verilog as an open-source tool for compiling and simulating Verilog RTL designs.
- **Key Concepts:**  
  Icarus Verilog enables simulation of hardware modules before synthesis, helps in design verification, and works seamlessly with VCD output for visualization.

#### b. Labs: Using Iverilog and GTKWave
- **Simulation Workflow:**  
  - Write Verilog design and appropriate testbench files.
  - Compile both using `iverilog`, producing a simulation executable.  
    ```
    iverilog -o sim_out my_design.v my_testbench.v
    ```
  - Run the simulation and generate a VCD (Value Change Dump) file:  
    ```
    vvp sim_out
    ```
  - View waveforms with GTKWave for in-depth signal analysis:  
    ```
    gtkwave dump.vcd
    ```
- **Deliverable:**  
  

#### c. Introduction to Yosys and Logic Synthesis
- **Overview:**  
  Yosys is an open-source framework for Verilog RTL synthesis, transforming Verilog code into a gate-level netlist.
- **Flow:**  
  - Install Yosys and clone the Sky130 PDKs for open-source cell libraries.
  - Run Yosys scripts to synthesize designs targeting `sky130` libraries.
    ```
    yosys
    # In the Yosys prompt or via script:
    read_verilog my_design.v
    synth -top my_design
    dfflibmap -liberty sky130_fd_sc_hd__tt_025C_1v80.lib
    write_verilog my_design_synth.v
    ```
- **Lab Activity:**  
  Synthesize a sample Verilog design, examine synthesized netlist, and include a brief note on synthesis optimizations made by Yosys.
</details>
---

### DAY 2 – Timing Libraries, Synthesis Strategies, and Flop Coding Styles

<details>
#### a. Introduction to Timing Libraries
- **Purpose:**  
  Timing libraries (e.g., Liberty format `.lib` files) provide technology-specific timing, power, and functional details for each standard cell.
- **Importance:**  
  Essential for accurate gate-level synthesis and subsequent STA (Static Timing Analysis).

#### b. Hierarchical Vs Flat Synthesis
- **Hierarchical Synthesis:**  
  - Synthesis happens module-wise, preserving design partitions and module boundaries.
  - Easier for debugging large designs, but may miss some cross-boundary optimizations.
- **Flat Synthesis:**  
  - All RTL is flattened before synthesis, enabling cross-module optimizations.
  - Can result in better timing/area but at the cost of complexity in tracing.
- **Key Takeaway:**  
  Know when to choose hierarchical vs. flat based on design size and optimization goals.

#### c. Various Flop Coding Styles and Optimization
- **Flop (D Flip-Flop) Coding Styles:**  
  - Different ways to model synchronous (clocked) sequential circuits in RTL.
  - Example:  
    ```
    always @(posedge clk)
      q <= d;
    ```
  - Efficient coding simplifies synthesis and physical timing closure.
- **Optimization Notes:**  
  - Prefer synchronous resets, use consistent coding styles, and avoid latches unless explicitly needed.
  - Review the synthesized netlist to confirm flops are correctly inferred.
</details>
---

### DAY 3 – Combinational and Sequential Optimization

<details>
#### a. Introduction to Optimization
- Objective: Understand the importance of optimizing Verilog RTL code for better performance, area, and power.
- Optimization ensures efficient hardware implementation during synthesis.

#### b. Combinational Logic Optimization
- Focus on reducing logic complexity.
- Use Boolean algebra and synthesis tool optimizations.
- Example: Minimize gates and logic levels for speed and area.

#### c. Sequential Logic Optimization
- Focus on flip-flops and registers.
- Optimize clock enabling, redundant registers removal, and retiming.

#### d. Sequential Optimization for Unused Output
- Remove or optimize sequential logic driving unused output signals.
- Helps reduce unnecessary power and area overhead in the design.
</details>
---

### DAY 4 – GLS Blocking vs Non-blocking and Synthesis Simulation Mismatch

<details>
#### a. GLS, Synthesis Simulation Mismatch and Blocking/Non-blocking Statements
- Gate Level Simulation (GLS) verifies the post-synthesis netlist using the same testbench.
- Synthesis-simulation mismatch occurs due to:
  - Missing sensitivity lists in `always` blocks.
  - Improper use of blocking (`=`) vs non-blocking (`<=`) assignments.
  - Non-standard Verilog coding styles.
- Blocking assignments execute sequentially within an `always` block; non-blocking executes all RHS first then updates LHS, enabling parallelism.
- Incorrect use can lead to simulation mismatches and inferred latches.

#### b. Labs on GLS and Synthesis Simulation Mismatch
- Perform GLS on synthesized netlists.
- Compare RTL simulation waveforms to netlist waveforms in GTKWave.

#### c. Labs on Synthesis Simulation Mismatch for Blocking Statements
- Experiment by deliberately coding with only blocking assignments.
- Observe mismatches and learn best coding practices to avoid them.

</details>
---

### DAY 5 – Optimization in Synthesis
<details>
#### a. If Case Construct
- How conditional statements are handled and optimized during synthesis.

#### b. Labs on Incomplete If Case
- Understand problems when if-else statements do not cover all conditions.
- Observe synthesis impact and unintended latch inference.

#### c. Labs on Incomplete Overlapping Case
- Study how overlapping case statements can lead to mismatches or unpredictable behavior.
- Learn to write mutually exclusive cases for reliable synthesis.

#### d. For Loop and For Generate
- Using `for` loops for replicating hardware blocks.
- `for-generate` construct for generating repetitive RTL structures efficiently.

#### e. Labs on For Loop and For Generate
- Write and synthesize designs leveraging loops and generate blocks.
- Measure the impact on design size and synthesis results.


---
</details>
---

## Week 1: Additional Resources & Best Practices

### 📚 References & Further Reading

- **Verilog HDL and RTL Design**  
  - *“Verilog HDL”* by Samir Palnitkar — A foundational book for Verilog syntax and design principles.  
  - *“Digital Design and Computer Architecture”* by Harris & Harris — Covers RTL design concepts clearly.  

- **Synthesis and Optimization**  
  - [Yosys Open SYnthesis Suite Documentation](https://yosyshq.net/yosys/) — Official Yosys documentation for logic synthesis.  
  - [IEEE Std 1800-2017 SystemVerilog Specification](https://ieeexplore.ieee.org/document/8299595) — For advanced RTL coding concepts.

- **Simulation Mismatch and Coding Guidelines**  
  - "Nonblocking vs Blocking Assignments in Verilog" by Sunburst Design — Detailed explanation of blocking vs non-blocking assignment behaviors.  
  - [ZipCPU Blog: Why Synthesis Might Not Match Simulation](https://zipcpu.com/blog/2018/08/04/sim-mismatch.html) — Practical discussion on sim-synthesis mismatches.

- **Skywater Sky130 PDK & Open Source ASIC Tools**  
  - [Skywater PDK GitHub](https://github.com/google/skywater-pdk) — For standard cell libraries and design kits referencing Sky130.  
  - [The OpenROAD Project](https://theopenroadproject.org/) — Automated RTL-to-GDSII open-source flow tools.


---

**Thank you for exploring the RISC-V Tapeout Journey!**

*Feel free to contribute, raise issues, or suggest improvements to make this learning resource even better.*

---



