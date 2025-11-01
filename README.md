
---

# 🌟 Week 6 – Hardware Meets Open Source

## 🏁 Welcome to Week 6!
This week, we explore how **hardware and software come together**, starting from Arduino microcontrollers to open-source chip design using **OpenLANE** and **Sky130 PDK**.  
We’ll uncover everything — from the structure of a chip to how code becomes hardware logic!

---

## ⚙️ Day 1 – Inception of Open-Source EDA, OpenLANE, and Sky130 PDK

### 🧩 RTL → GDSII Flow (Simplified)
This flow converts a hardware design (RTL) into a physical layout (GDSII) ready for fabrication.

1. **Synthesis** – RTL (Verilog/VHDL) → Gate-level netlist using standard cells.  
2. **Floorplanning & Power Planning** – Define block placement and power networks.  
3. **Placement** – Global + Detailed placement of cells.  
4. **Clock Tree Synthesis (CTS)** – Build balanced clock distribution.  
5. **Routing** – Global and detailed routing for all nets.  
6. **Sign-Off** – DRC, LVS, and STA before fabrication.

### 🧠 OpenLANE ASIC Flow
OpenLANE automates the full ASIC flow using open-source tools:
- **Yosys** + **ABC** → Synthesis  
- **OpenSTA** → Timing Analysis  
- **OpenROAD** → Placement, CTS, Routing  
- **Magic** → DRC & GDSII Export  
- **Netgen** → LVS Check  

### ⚙️ Key Commands
```bash
cd ~/Desktop/work/tools/openlane_working_dir/openlane
alias docker='docker run -it -v $(pwd):/openLANE_flow -v $PDK_ROOT:$PDK_ROOT \
-e PDK_ROOT=$PDK_ROOT -u $(id -u $USER):$(id -g $USER) efabless/openlane:v0.21'
docker
./flow.tcl -interactive
package require openlane 0.9
prep -design picorv32a
run_synthesis
````

---

## 🧠 Day 2 – The Birth of Open-Source Chip Design

Discover how open-source initiatives revolutionized semiconductor design.

* Introduction to **Open-Source EDA Tools**
* Role of **SkyWater 130 nm PDK** in democratizing chip fabrication
* Overview of **Google & Efabless** Open MPW Programs
* Benefits of community-driven design and collaborative development

---

## 🧱 Day 3 – The Art of Floorplanning and Library Cells

Learn how physical design takes shape:

* Understanding **Core Area**, **IO Area**, and **Die Layout**
* Placement of **Standard Cells**, **Macros**, and **Power Grids**
* Concept of **Library Cells (LEF/DEF)** and their importance in synthesis
* Practical insights using **Magic Layout Editor** and **OpenROAD Tools**

---

## 🧩 Day 4 – IO Pin Configurations, 16-Mask CMOS Process & Layout Extraction

### 🔌 IO Pin Configurations

* Learn how IO pins are defined and connected to the core.
* Understand pin constraints, metal layers, and power routing.

### 🧪 16-Mask CMOS Fabrication Process

Each mask defines a step in chip manufacturing:

1. Active Area Definition
2. Well Formation
3. Gate Oxidation
4. Poly Deposition
5. Source/Drain Implantation
6. Metal Layer Formation
7. Via Contacts
8. Passivation Layer
   …and more up to 16 masks for a full CMOS process.

### 🧰 Layout Extraction Using Magic

* Use **Magic EDA** to extract parasitics (R & C) from layouts.
* Perform **DRC** & **LVS** verification before tape-out.
* Generate **GDSII** for fabrication.

---

## 📚 Summary

By completing Week 6, you’ve explored:

* How software translates into hardware logic
* The structure of processors, chips, and packages
* The end-to-end ASIC flow using open-source tools
* Real-world chip fabrication concepts from floorplan to layout

---
