
---

# 🌍 **WEEK 6 – DAY 2: The Birth of Open-Source Chip Design**

### 🧠 **Imagine This:**

You’re an architect — but instead of buildings, you design chips.
Your “blueprint” is not a sketch; it’s a *Verilog file*.
Your “building site” is silicon, and your “construction tools” are EDA software.
That’s exactly what the **RTL-to-GDSII flow** does — it transforms your digital idea into a silicon reality.

But until recently, only big semiconductor companies could afford these tools.
Then came the **open-source revolution** — tools like **OpenLANE** and **Sky130 PDK** made chip design accessible to everyone, just like Linux made operating systems open for developers.

---

## 🧩 **1. The RTL-to-GDSII Flow — From Thought to Silicon**

Think of it as building a city:

| Stage                          | Real-World Analogy                            | Purpose                                          |
| :----------------------------- | :-------------------------------------------- | :----------------------------------------------- |
| **Synthesis**                  | Translating blueprints into individual bricks | Converts RTL into gate-level netlist             |
| **Floorplanning**              | Deciding where the buildings go               | Allocates space for blocks and power grids       |
| **Placement**                  | Actually placing each brick                   | Positions logic cells legally and efficiently    |
| **CTS (Clock Tree Synthesis)** | Setting up the city’s electricity poles       | Delivers synchronized clock signals              |
| **Routing**                    | Building roads to connect everything          | Connects signals using metal layers              |
| **Signoff**                    | Final city inspection before opening          | Ensures design follows all rules and constraints |

At the end of this process, your “city” becomes a **GDSII file**, the standard format used by fabrication foundries.

---

## 🚀 **2. The Open-Source Movement**

Earlier, chip design tools were **closed, expensive, and proprietary** — like building a house but paying rent for every hammer.
**OpenLANE** changed that. It’s a toolkit where every step — from design to layout — is handled by open tools:

* **Yosys** for logic synthesis
* **OpenROAD** for floorplanning, placement, CTS, and routing
* **Magic** and **Netgen** for physical verification
* **OpenSTA** for timing analysis

All of them work with **SkyWater SKY130**, an open fabrication process — the *“soil”* where your chip will grow.

---

## ⚙️ **3. What Really Happens Inside**

When you run OpenLANE, it’s like pressing a button that triggers a chain of intelligent machines:

### 🧱 **Step 1: Synthesis (Yosys + ABC)**

* Your Verilog code is “translated” into hardware gates.
* Redundant logic is removed, and the design is optimized.
* Output: A **gate-level netlist** — a low-level map of AND, OR, and flip-flop cells.

---

### 🏗️ **Step 2: Floorplanning**

* Defines the “canvas” of your chip.
* Major blocks (like ALUs or memory) are given reserved spaces.
* Power lines are drawn like highways — wide and strong to handle current.

---

### 🔩 **Step 3: Placement**

* Each gate (standard cell) finds its seat on the chip.
* First, they are roughly arranged (**global placement**).
* Then, they are finely adjusted (**detailed placement**) to fit the legal design grid.

---

### ⏰ **Step 4: Clock Tree Synthesis**

* Now every flip-flop needs a rhythm — the clock signal.
* CTS builds a balanced tree-like structure so every part of the circuit “ticks” together.
* Minimizing **clock skew** is crucial — even nanosecond differences can break synchronization.

---

### 🔌 **Step 5: Routing**

* Metal layers act as roads connecting the logic gates.
* The router ensures no wires overlap and obeys spacing rules.
* During this step, **antenna diodes** are added to protect delicate transistors from charge buildup during fabrication.

---

### 🧾 **Step 6: Physical Verification & Signoff**

* The layout is now checked like a quality inspection:

  * **DRC:** No design rule violations?
  * **LVS:** Does the physical layout match the schematic logic?
  * **STA:** Are timing requirements met everywhere?

Once passed, the chip is **“tape-out ready”**, meaning it can be fabricated.

---

## 🧪 **4. Inside OpenLANE – How You Run It**

You don’t need a powerful lab — just your system, Docker, and the PDKs.
Here’s what’s happening conceptually:

1. **Set up the workspace**

   * You move into your OpenLANE working folder.
2. **Launch the environment**

   * Docker creates a “sandbox” where OpenLANE tools live.
3. **Load your design**

   * `prep -design picorv32a` prepares directories and files.
4. **Run synthesis**

   * `run_synthesis` triggers Yosys to generate the netlist.
5. **Check reports**

   * The synthesis report shows logic cells, flip-flops, and flop ratio — a quick measure of how sequential your design is.

If **Flop Ratio = 0.108**, that means roughly 10% of your design consists of flip-flops — a mix of sequential and combinational logic.

---

## 📂 **5. Where the Magic Lives**

Once synthesis is complete, OpenLANE automatically stores your results:

* **Netlist:**
  `.../results/synthesis/picorv32a.synthesis.v`
* **Reports:**
  `.../reports/synthesis/yosys_4.stat.rpt`

Each report is like a health record of your design — showing how many cells exist, their area, power estimates, and timing summaries.

---

## 💡 **6. Why SKY130 and OpenLANE Matter**

Before OpenLANE, chip design was a closed castle guarded by NDAs and expensive licenses.
Now, with **SKY130 PDK** and **OpenLANE**, even students can build and fabricate a real chip — the *democratization of silicon*.

It’s not just about running commands — it’s about *understanding the journey* your design takes:

* From **logical imagination (RTL)**
* To **geometrical realization (GDSII)**
* And finally to **physical creation (fabrication)**

---

## 🏁 **Summary**

| Concept                     | Purpose                                              |
| :-------------------------- | :--------------------------------------------------- |
| **RTL to GDSII Flow**       | The full process of chip design from logic to layout |
| **OpenLANE**                | Open-source automation tool for ASIC design          |
| **SKY130 PDK**              | Open fabrication process for silicon implementation  |
| **Yosys, OpenROAD, Magic**  | Core tools that power OpenLANE                       |
| **Flop Ratio**              | Indicator of sequential logic density                |
| **Signoff (DRC, LVS, STA)** | Final verification before fabrication                |

---
