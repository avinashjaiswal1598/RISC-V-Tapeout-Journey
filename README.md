# 🚀 RISC-V Tapeout Program – Week 0: Toolchain Installation
<img width="733" height="491" alt="image" src="https://github.com/user-attachments/assets/71c03229-039a-4104-b87d-4fdc287845ad" />


Welcome to the Week 0 setup! This week focuses on installing essential EDA tools for the RISC-V tapeout project.

## 🚦 Objective
Get your environment ready for the RISC-V tapeout journey by installing and testing **Yosys**, **GTKWave**, and **Iverilog**.

## 🗂️ Tasks
- Install Yosys, GTKWave, Iverilog on Ubuntu
- Validate installations with sample command runs
- Document any issues faced and their fixes
  

## 📝 Step-by-Step Guide

### Yosys Installation
sudo apt update
sudo apt install yosys
yosys -V # Check version
<img width="733" height="491" alt="image (1)" src="https://github.com/user-attachments/assets/017f4415-a26b-40a1-b152-132e001119e8" />

### GTKWave
sudo apt install gtkwave


### Iverilog
sudo apt install iverilog

## OpenSTA - Static Timing Analyzer

OpenSTA is a fast and open-source Static Timing Analyzer for VLSI designs.

### Installation (Ubuntu 22.04+)
sudo apt update
sudo apt install opensta
opensta --version # Verify installation

### Quick Start
Run `opensta` on your SDC and SPEF files to analyze timing.

![OpenSTA Logo](https://opensta.org/img/opensta-logo.png) 

---

## Ngspice - Circuit Simulator

Ngspice simulates analog electronic circuits using SPICE.

### Installation
sudo apt update
sudo apt install ngspice
ngspice -v # Check version

### Basic Test
Run `ngspice` and load a sample test circuit file `.cir`.

---

## Magic VLSI - Layout Tool

Magic is a classic, easy-to-use VLSI layout tool.

### Installation from source
sudo apt update
sudo apt install git build-essential tcl-dev tk-dev libx11-dev
git clone https://github.com/RTimothyEdwards/magic.git
cd magic
./configure
make
sudo make install
magic -version

### Usage
Launch `magic` and start creating or editing layouts.

---

## OpenLANE - RTL to GDSII Flow Automation

OpenLANE automates the full digital ASIC flow.

### Installation via Docker
git clone https://github.com/The-OpenROAD-Project/OpenLane.git
cd OpenLane
./quick_start.sh

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

---

