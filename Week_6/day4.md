
---

# **WEEK 6 – Day 4: IO Pin Configurations, 16-Mask CMOS Process, and Layout Extraction using Magic**

---

## **1. IO Pin Configurations in OpenLANE**

IO pins define how signals enter and leave the chip. OpenLANE allows different pin placement configurations that determine how IO pins are arranged around the die boundary.

### **Steps to Visualize IO Pins in Magic**

First, navigate to your floorplan results directory:

```bash
cd ~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/29-10_00-31/results/floorplan
```

Now open the floorplan layout in **Magic**:

```bash
magic -T ~/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.floorplan.def &
```

🟢 **Inside Magic:**

* To zoom in, left-click and right-click to define a zoom box, then press `z`.
* You can view IO pins distributed along the periphery of the die.

---

### **FP_IO_MODE Parameter**

In OpenLANE, IO pin configurations are controlled by a parameter named `FP_IO_MODE`, defined in:

```
openlane/configurations/floorplan.tcl
```

* `FP_IO_MODE = 1` → Default configuration
* `FP_IO_MODE = 2` → Alternate pin arrangement
* Other values correspond to different placement patterns.

To switch configuration:

```tcl
set ::env(FP_IO_MODE) 2
run_floorplan
```

After running the command, open the new floorplan in Magic again:

```bash
cd ~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/30-10_11-30/results/floorplan
magic -T ~/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.floorplan.def &
```

🔍 **Observation:**
The arrangement of IO cells will differ from the earlier layout. Compare both configurations (FP_IO_MODE = 1 and FP_IO_MODE = 2) to observe the positional variation of IO pins around the die.

---

## **2. The 16-Mask CMOS Process**

The CMOS fabrication process typically uses **16 different masks** to define various layers and structures. Let’s go through each major step:

---

### **Step 1: Selecting the Substrate**

* Use **P-type silicon substrate**
* Resistivity: 5–50 Ω·cm
* Doping: ~10¹⁵ cm⁻³
* Crystal Orientation: (100)

---

### **Step 2: Creating the Active Region**

* Grow thin **SiO₂** (~40 nm)
* Deposit **Si₃N₄** (~80 nm)
* Apply and pattern **photoresist**
* Perform **LOCOS (Local Oxidation of Silicon)** to form isolation oxide.
* Remove Si₃N₄ to expose active transistor regions.

---

### **Step 3: N-Well and P-Well Formation**

* Use photolithography to define well regions.
* Implant **Boron** for P-Well (~200 keV).
* Implant **Phosphorus** for N-Well.
* Perform **high-temperature drive-in diffusion** to set final well depth.

---

### **Step 4: Gate Formation**

* Apply photoresist, expose gate area.
* Deposit **Polysilicon (~0.4 µm)** and dope it with phosphorus.
* Pattern polysilicon to form gate structures.

---

### **Step 5: LDD (Lightly Doped Drain) Formation**

* Perform light implantation near the gate:

  * **Phosphorus** for NMOS (n⁻ regions)
  * **Boron** for PMOS (p⁻ regions)
* Deposit oxide/nitride spacer and perform anisotropic etching.

---

### **Step 6: Source and Drain Formation**

* Perform heavy ion implantation:

  * **Arsenic** for NMOS (n⁺)
  * **Boron** for PMOS (p⁺)
* **Anneal** the wafer to activate dopants and repair crystal damage.

---

### **Step 7: Contact and Local Interconnect**

* Etch oxide openings for contacts.
* Deposit **Titanium (Ti)** and heat to form:

  * **TiSi₂** (ohmic contact)
  * **TiN** (local interconnect)
* Clean using **RCA solution** and remove excess TiN.

---

### **Step 8: Higher Metal Layers and Passivation**

* Deposit **SiO₂** for insulation.
* Perform **CMP (Chemical Mechanical Polishing)**.
* Open vias and deposit **Tungsten (W)** for connections.
* Add **Aluminium** for interconnects.
* Apply **Si₃N₄** dielectric as the final protective layer.

---

✅ **Result:**
After all 16 masking steps, the chip is ready for **packaging and testing**.

---

## **3. Layout Extraction and Simulation of CMOS Inverter**

Now, let’s move to hands-on work using **Magic** and **Ngspice**.

---

### **Step 1: Cloning Layout Repository**

```bash
cd ~/Desktop/work/tools/openlane_working_dir/openlane/
git clone https://github.com/nickson-jose/vsdstdcelldesign.git
```

Open the inverter layout:

```bash
magic -T ./lib/sky130A.tech sky130_inv.mag &
```

Inside Magic:

* Press `s` to select PMOS/NMOS.
* Type `what` in **tkcon** to identify components.

---

### **Step 2: Extracting the SPICE File**

```bash
pwd
extract all
ext2spice cthresh 0 rthresh 0
ext2spice
```

This generates the **sky130_inv.spice** file.

---

### **Step 3: Editing SPICE Parameters**

Enable grid and measure scale:

```
Window → Grid On
```

Select a box → type:

```bash
box
```

If box dimension = `0.010 x 0.010 µm`, then:

```bash
.scale 0.01u
```

Match transistor model names with **pshort.lib** (e.g., `pshort_model.0`).

---

### **Step 4: Running Simulation in Ngspice**

Run:

```bash
ngspice sky130_inv.spice
```

Plot waveform:

```bash
plot y vs time a
```

---

### **Step 5: Measuring Key Parameters**

#### **Rise Time (Tr):**

When output rises from 20% → 80% of Vdd.

[
Tr = T(80%) - T(20%) = 2.24 - 2.18 = 0.06ns
]

#### **Fall Time (Tf):**

When output falls from 80% → 20% of Vdd.

[
Tf = T(80%) - T(20%) = 6.24 - 4.09 = 2.15ns
]

#### **Rise Delay (Trd):**

[
Trd = Tout(50%) - Tin(50%) = 2.25 - 2.15 = 0.06ns
]

#### **Fall Delay (Tfd):**

[
Tfd = Tout(50%) - Tin(50%) = 6.21 - 6.15 = 0.06ns
]

---

## **4. Lab: DRC Rules and Tech File Modifications**

Download DRC test files:

```bash
wget http://opencircuitdesign.com/open_pdks/archive/drc_tests.tgz
tar xfz drc_tests.tgz
cd drc_tests
```

Open Magic:

```bash
magic -d XR &
load poly
```

Select and measure distance:

```bash
box
```

If spacing = 0.21 µm and rule requires 0.48 µm, it should trigger a **DRC violation**.
If not, update the **sky130A.tech** file for correct `poly.9` rule handling.

---

### **Fixing N-Well Tap DRC (nwell.4)**

Modify the technology file to detect untapped wells:

```tcl
templayer nwell_tapped
bloat-all nsc nwell

templayer nwell_untapped nwell
and-not nwell_tapped
```

Re-run:

```bash
drc check
drc why
```

🔍 Now, untapped N-wells are correctly detected as violations.
Once tapped using **nsubstratecontact**, rerun DRC — no errors should appear.

---

✅ **Final Observation:**

* IO pin configuration affects routing and IO accessibility.
* The 16-mask CMOS process defines the foundation of IC fabrication.
* Magic + Ngspice helps verify the physical layout and electrical behavior.
* Proper DRC rule enforcement ensures layout manufacturability.

---
