
---

# 🌆 **WEEK 6 – DAY 3: The Art of Floorplanning and Library Cells**

Welcome to Day 2 of our silicon journey!
If yesterday was about understanding how ideas become silicon (RTL → GDSII), today is about **how we arrange that silicon city** — planning where every building, road, and light pole will go before construction begins.

---

## 🏙️ **1. Good vs Bad Floorplan – The Blueprint of a Silicon City**

Think of your chip as a **mini city**.
Every logic block, memory, and pin is a building that must fit into the right spot.
A **good floorplan** is like a well-organized city — roads are short, traffic flows smoothly, and power reaches every corner.
A **bad floorplan**? Think messy streets, long cables, power drops, and chaos.

### 🚦 What Makes a Good Floorplan

* Balanced **aspect ratio** — not too long or too wide (a square core is often best).
* Optimal **utilization** — cells fill the area efficiently but not too tightly (usually 50–70%).
* Smart **placement of big blocks** like memory and I/O ports to reduce wire congestion.
* Robust **power grid** to distribute current evenly.
* Clear **routing channels** between major blocks.

---

## 📐 **2. Core, Die, and Utilization – Setting Up the Chip Canvas**

When designing, we define two important boundaries:

* **Die Area:** The total area of the chip (the outer wall of our city).
* **Core Area:** The active region inside the die where all standard cells live.

### ⚙️ **Utilization Factor**

This tells how much of your core is filled with cells.
If utilization is too high → cells are jammed → routing fails.
Too low → wasted area → inefficient chip.

[
Utilization = \frac{Area_{netlist}}{Area_{core}}
]

### 🧭 **Aspect Ratio**

Like the shape of your land:
[
Aspect\ Ratio = \frac{Height}{Width}
]
A 1:1 ratio makes routing balanced in both directions.

---

## 🧱 **3. Pre-Placed Cells – The Landmarks**

Before automatic placement begins, some buildings are already fixed — these are **pre-placed cells**.
They’re big and special — think of **memory blocks, PLLs, analog IPs**, or **macros**.
Their positions affect routing and timing, so we pin them down early — like placing hospitals and schools before houses.

---

## ⚡ **4. Decoupling Capacitors – The Shock Absorbers**

Digital logic switches millions of times per second, causing small voltage fluctuations.
To prevent the city’s lights from flickering, we place **decoupling capacitors (decaps)** — tiny energy reservoirs that release charge when needed.
They keep the supply voltage stable and minimize **IR drop** and noise.

---

## 🔋 **5. Power Planning – The City’s Electrical Grid**

Now we lay the power lines!
A **Power Distribution Network (PDN)** is built like a **metal mesh** connecting all VDD (power) and VSS (ground) lines.
These are arranged as:

* **Rings** around the core
* **Straps** (horizontal/vertical highways) distributing power
* **Vias** connecting multiple metal layers

Without good power planning, parts of the chip might “brown out” — losing voltage and failing timing.

---

## 📡 **6. Pin Placement – The City’s Gates**

Pins are where signals enter and leave the chip — like city gates connecting to highways.
Their placement along the die boundary ensures easy access for external signals.
We group them based on function — inputs on one side, outputs on another — for minimal routing complexity.

---

## 🚧 **7. Placement Blockages – No-Entry Zones**

Not every area inside the core is free for placement.
Certain zones must remain clear — around pre-placed macros or along the edges for I/O routing.
These are called **placement blockages**, ensuring cells don’t spill into restricted regions.

---

## 🖥️ **8. Running Floorplan in OpenLANE**

When we execute:

```bash
run_floorplan
```

OpenLANE:

* Calculates the core and die size
* Sets utilization and aspect ratio
* Places pins and tap cells
* Builds the power grid

The output files record all dimensions, pin placements, and PDN details.

Example from DEF file:

```
DIE AREA (0,0) to (660685,671405);
```

That translates to:

* Width = 660.685 μm
* Height = 671.405 μm
* Area = 443,520.41 μm²

A snapshot of your chip’s skeleton!

---

## 🧭 **9. Visualizing the Floorplan in Magic**

We can now open the floorplan in **Magic Layout Tool**:

```bash
magic -T sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.floorplan.def &
```

Once inside Magic:

* Press `v` to fit the layout
* Use `z` to zoom
* Use `what` in the `tkcon` console to identify selected components

You’ll spot:

* **Tap Cells:** placed diagonally across the core (for well and substrate ties)
* **Standard Cells:** at the boundary, neatly aligned and waiting for placement

---

## 🧩 **10. Placement – Building the City**

Now that the floorplan is ready, we move to **placement** — arranging all the standard cells on the grid.

### 🪜 **Step 1: Binding Logic to Physical Cells**

Each logical component in the netlist is linked to a real, physical cell from the standard library (like connecting your city blueprint to actual buildings).

### 🧮 **Step 2: Placement Stages**

* **Global Placement:** Roughly positions cells to minimize total wirelength and congestion.
* **Detailed Placement:** Aligns cells legally to rows with no overlaps.

### 🧠 **Step 3: Optimization**

After placement, the tool:

* Inserts buffers (repeaters) for long routes.
* Reduces wire congestion.
* Maintains signal quality for next stages (clocking and routing).

Command:

```bash
run_placement
```

---

## 🏗️ **11. Observing Placement in Magic**

View your layout again:

```bash
magic -T sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.placement.def &
```

You’ll see a dense grid of neatly arranged standard cells — your chip’s city now fully populated!

---

## 🧬 **12. Library Cells – The Building Blocks of Silicon**

Library cells are the **DNA** of digital design.
Every AND, OR, NAND, NOR, and D flip-flop comes from a *library* that defines its shape, timing, and power.

### 📘 **Library Cell Design Flow**

1. **Circuit Design:** Create transistor-level schematics.
2. **Layout Design:** Convert the schematic into geometric layers.
3. **Characterization:** Simulate and extract parameters like timing, power, and noise.

**Outputs:**

* GDSII (layout geometry)
* LEF (abstract physical info)
* CDL/SPICE netlist
* Liberty (.lib) files with timing and power models

---

## ⚡ **13. Timing Characterization Example – CMOS Inverter**

Imagine characterizing a simple inverter:

1. Read transistor models (PMOS, NMOS).
2. Apply power supply and load capacitance.
3. Stimulate with rising/falling edges.
4. Measure:

   * **Propagation delay (t_pd)** → how long output lags after input changes
   * **Transition time (t_rise / t_fall)** → how fast the output switches

Defined using voltage thresholds:

| Parameter                   | Meaning                  | Typical Value |
| --------------------------- | ------------------------ | ------------- |
| in_rise_thr / in_fall_thr   | Input crossing point     | 50%           |
| out_rise_thr / out_fall_thr | Output crossing point    | 50%           |
| slew_high / low thresholds  | Define rise/fall regions | 20%–80%       |

Tools like **GUNA** perform these simulations and generate timing models that OpenLANE uses during synthesis and STA.

---

## 🧭 **14. Why Floorplanning Matters**

Floorplanning defines the destiny of your chip.
A poor plan leads to congestion, voltage drops, and timing failures.
A well-crafted floorplan ensures:
✅ Balanced power and signal flow
✅ Shorter routing paths
✅ Better performance and yield

It’s like city planning — once the roads are built, moving them is nearly impossible!

---

## 🏁 **Summary Table**

| Concept              | Meaning / Purpose                 |
| -------------------- | --------------------------------- |
| **Core & Die**       | Define chip boundaries            |
| **Utilization**      | Controls cell density             |
| **Aspect Ratio**     | Sets chip shape                   |
| **Pre-Placed Cells** | Fixed large blocks                |
| **Decaps**           | Power stabilizers                 |
| **PDN**              | Power delivery network            |
| **Pins**             | I/O interfaces                    |
| **Blockages**        | Keep-out zones                    |
| **Placement**        | Positioning of standard cells     |
| **Library Cells**    | Standard building blocks of logic |
| **Characterization** | Deriving timing and power models  |

---
