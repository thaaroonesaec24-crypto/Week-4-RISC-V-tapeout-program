
Perfect ✅ — since your section headers already use Markdown headings (like `#`, `##`, `###`), GitHub automatically generates **anchors** based on them.

Here’s your **final Table of Contents** with working clickable links (for GitHub README navigation).
Each link directly matches the `#` headers in your document 👇

---

#  **Table of Contents**

1. [SPICE Simulation for Lower Technology Nodes](#-spice-simulation-for-lower-technology-nodes)
2. [Device Parameters](#-device-parameters)
3. [NMOS Output Characteristics (Id–Vds Behavior)](#-nmos-output-characteristics-idvds-behavior)
4. [Observation 1: Long-Channel vs Short-Channel Behavior](#-observation-1-long-channel-vs-short-channel-behavior)
5. [Velocity Saturation Explained](#-velocity-saturation-explained)
6. [Operating Regions Summary](#-operating-regions-summary)
7. [Observation 2: Peak Current Comparison](#-observation-2-peak-current-comparison)
8. [Summary of Key Findings](#-summary-of-key-findings)
9. [Practical Implications for Lower Node Technologies](#-practical-implications-for-lower-node-technologies)
10. [Lab with sky130 models](#-lab-with-sky130-models)
11. [CMOS Voltage Transfer Characteristics (VTC)](#-cmos-voltage-transfer-characteristics-vtc)
12. [Inverter Operation](#-inverter-operation)
13. [Switch-Level Representation](#-switch-level-representation)
14. [Load Line Analysis and VTC Derivation](#-load-line-analysis-and-vtc-derivation)
15. [Key Insights from CMOS VTC Analysis](#-key-insights-from-cmos-vtc-analysis)
16. [Benefits of Performing CMOS VTC Analysis](#-benefits-of-performing-cmos-vtc-analysis)
17. [Summary Table](#-summary)

---

### 🧩 **Tip**

When you paste this at the top of your README:

* GitHub automatically turns each link into a **scrollable anchor**.
* The anchors are **case-insensitive** and **ignore bold/italics/special characters**, so this format will work perfectly with your headers.


#  SPICE Simulation for Lower Technology Nodes

This section presents a detailed analysis of **NMOS output characteristics** obtained through **SPICE simulations**, focusing on **long-channel** and **short-channel** behavior in lower technology nodes.

---

###  Device Parameters

Two NMOS devices were simulated using identical **W/L ratios**, but different absolute dimensions:

| Device Type | Width (W) | Length (L) | W/L Ratio | Classification |
| ----------- | --------- | ---------- | --------- | -------------- |
| Device 1    | 1.8 μm    | 1.2 μm     | 1.5       | Long-channel   |
| Device 2    | 0.375 μm  | 0.25 μm    | 1.5       | Short-channel  |

This setup allows a fair comparison between **long-channel** and **short-channel** characteristics while maintaining the same aspect ratio.

---

###  NMOS Output Characteristics (Id–Vds Behavior)

The **drain current (Id)** vs **drain-to-source voltage (Vds)** was simulated for multiple **gate-to-source voltages (Vgs)**.

#### Linear Region

* Occurs when:
  [
  V_{DS} < (V_{GS} - V_T)
  ]
* In this region, **Id** increases linearly with **Vds**.
* The MOSFET behaves like a **voltage-controlled resistor**.
* Drain current is approximately:
  [
  I_D = μ_n C_{ox} \frac{W}{L} \left[ (V_{GS} - V_T)V_{DS} - \frac{V_{DS}^2}{2} \right]
  ]

#### Saturation Region

* Occurs when:
  [
  V_{DS} ≥ (V_{GS} - V_T)
  ]
* In this region, **Id** becomes almost constant and depends primarily on **Vgs**.
* However, due to **channel length modulation**, Id slightly increases with Vds.
* The expression becomes:
  [
  I_D = \frac{1}{2} μ_n C_{ox} \frac{W}{L} (V_{GS} - V_T)^2 (1 + λV_{DS})
  ]

Thus, the **transition point** between linear and saturation regions occurs at:
[
V_{DS} = V_{GS} - V_T
]

---

###  Observation 1: Long-Channel vs Short-Channel Behavior

Both devices were simulated under the same bias conditions, varying **Vgs** and observing **Id–Vds** curves.

#### Long-Channel Device (L = 1.2 μm)

* Exhibits **ideal quadratic dependence** of Id on Vgs.
* Drain current increases smoothly and predictably with Vgs.
* No significant velocity saturation observed.

#### Short-Channel Device (L = 0.25 μm)

* At **low Vgs**, Id shows quadratic dependence, similar to long-channel behavior.
* At **high Vgs**, the Id–Vgs curve transitions to a **linear dependence**.
* This is caused by **velocity saturation**, where carrier velocity stops increasing with electric field due to high field effects.

---

###  Velocity Saturation Explained

In **long-channel devices**, carrier velocity (v) is proportional to the electric field (E):

[
v = μ_n E
]

However, as the **electric field increases** in **short-channel devices**, carriers reach a **maximum velocity** ( v_{sat} ), and further increases in E do not significantly affect v.

[
v =
\begin{cases}
μ_n E, & E < E_{crit} \
v_{sat}, & E ≥ E_{crit}
\end{cases}
]

This phenomenon limits the **drain current** at high Vgs, leading to **reduced Id** and a **flattened Id–Vgs** curve.

---

###  Operating Regions Summary

| Channel Type                 | Channel Length | Operating Modes                                           |
| :--------------------------- | :------------- | :-------------------------------------------------------- |
| **Long Channel (> 250 nm)**  | 1.2 μm         | Cutoff → Resistive → Saturation                           |
| **Short Channel (< 250 nm)** | 0.25 μm        | Cutoff → Resistive → **Velocity Saturation** → Saturation |

The appearance of the **velocity saturation region** is unique to **short-channel devices**, and it profoundly affects performance metrics like current drive, transconductance, and output resistance.

---

###  Observation 2: Peak Current Comparison

To understand the impact of velocity saturation on drain current, the **peak Id** values were compared between the two devices.

| Device Type | W (μm) | L (μm) | Channel Type  | Peak Drain Current (Id) | Observation                                       |
| ----------- | ------ | ------ | ------------- | ----------------------- | ------------------------------------------------- |
| Device 1    | 1.8    | 1.2    | Long-channel  | **410 μA**              | High current due to unrestricted carrier velocity |
| Device 2    | 0.375  | 0.25   | Short-channel | **210 μA**              | Lower current due to velocity saturation          |

#### Key Insight

Although short-channel devices offer **smaller area** and **faster switching**, they suffer from **reduced peak drain current** because the carrier velocity saturates at high electric fields.

---

### Summary of Key Findings

1. **Linear vs Saturation Regions:**

   * Linear region: ( I_D \propto V_{DS} )
   * Saturation region: ( I_D \propto (V_{GS} - V_T)^2 )

2. **Velocity Saturation:**

   * Causes Id–Vgs transition from quadratic to linear at high gate voltages.
   * Prominent in short-channel devices (< 250 nm).

3. **Peak Current Reduction:**

   * Short-channel devices exhibit **~50% lower** peak Id compared to long-channel devices.

4. **Performance Trade-offs:**

   * Short channels → faster switching but limited current.
   * Long channels → higher current but slower operation.

---

### Practical Implications for Lower Node Technologies

* As device geometries scale down, **velocity saturation** becomes dominant, affecting both **analog performance** and **digital timing**.
* Designers must account for:

  * Reduced drive current
  * Increased delay variability
  * Need for velocity saturation models in SPICE

---

#  Lab with sky130 models
<details> <summary><strong>day2_nfet_idvds_L015_W039.spice </strong></summary>

 *Model Description
  
.param temp=27


*Including sky130 library files

.lib "sky130_fd_pr/models/sky130.lib.spice" tt


*Netlist Description



XM1 Vdd n1 0 0 sky130_fd_pr__nfet_01v8 w=0.39 l=0.15

R1 n1 in 55

Vdd vdd 0 1.8V
Vin in 0 1.8V

*simulation commands

.op
.dc Vdd 0 1.8 0.1 Vin 0 1.8 0.2

.control

run
display
setplot dc1
.endc

.end

</details>


**plot the waveforms in ngspice**

```
ngspice day2_nfet_idvds_L015_W039.spice 
plot -vdd#branch
```

![](https://github.com/thaaroonesaec24-crypto/Week-4-RISC-V-tapeout-program/blob/main/DAY%202/images/day2_nfet_idvds_L015_W039.spice%20week%204%20day%202.png)


<details> <summary><strong>day2_nfet_idvgs_L015_W039.spice </strong></summary>

*Model Description
.param temp=27


*Including sky130 library files
.lib "sky130_fd_pr/models/sky130.lib.spice" tt


*Netlist Description

XM1 Vdd n1 0 0 sky130_fd_pr__nfet_01v8 w=0.39 l=0.15

R1 n1 in 55

Vdd vdd 0 1.8V
Vin in 0 1.8V

*simulation commands

.op
.dc Vin 0 1.8 0.1

.control

run
display
setplot dc1
.endc

.end
</details>

**plot the waveforms in ngspice**

```
ngspice day2_nfet_idvgs_L015_W039.spice 
plot -vdd#branch
```

![](https://github.com/thaaroonesaec24-crypto/Week-4-RISC-V-tapeout-program/blob/main/DAY%202/images/day2_nfet_idvgs_L015_W039.spice%20week%204%20day%202.png)


Here’s a **clear and detailed summary** of your section on **CMOS Voltage Transfer Characteristics (VTC)** and **Inverter Analysis**, rewritten in **GitHub README style** — concise, technical, and professional.
It includes a short conceptual explanation **plus the benefits of performing this analysis**.

---

#  CMOS Voltage Transfer Characteristics (VTC)

The **CMOS inverter** is the most fundamental building block in digital VLSI design.
Its **Voltage Transfer Characteristic (VTC)** defines how the output voltage (**Vout**) responds to changes in the input voltage (**Vin**), determining the circuit’s **switching threshold**, **noise margins**, and **logic behavior**.

---

### Inverter Operation

At the **transistor level**, the inverter consists of:

* **PMOS** connected to **Vdd**
* **NMOS** connected to **Vss (GND)**
* Both gates tied to **Vin**
* Output (**Vout**) taken from the common drain node

#### Logic Behavior:

| Input (Vin) | PMOS | NMOS | Output (Vout)  |
| ----------- | ---- | ---- | -------------- |
| 0 (LOW)     | ON   | OFF  | **Vdd (HIGH)** |
| Vdd (HIGH)  | OFF  | ON   | **0 (LOW)**    |

This complementary operation ensures:

* **High noise immunity**
* **Low static power consumption**
* **Full rail-to-rail voltage swing**

---

### Switch-Level Representation

At the **switch level**, each transistor acts as a voltage-controlled switch:

* **NMOS:** Conducts when gate = HIGH
* **PMOS:** Conducts when gate = LOW

Hence:

* When **Vin = 0**, PMOS ON → output pulled up to **Vdd**
* When **Vin = Vdd**, NMOS ON → output pulled down to **0**

This behavior produces a **sharp transition** in VTC, which defines the **switching point (Vm)** where both transistors conduct simultaneously.

---

### Load Line Analysis and VTC Derivation

To obtain the **Voltage Transfer Characteristic**:

1. **Express PMOS current (Ip)** and **NMOS current (In)** in terms of Vin and Vout.
2. **Convert** PMOS equations into the same voltage reference as NMOS (Vin, Vout, Vdd).
3. **Plot load line curves** for both transistors — the intersection represents the **operating point**.
4. **Sweep Vin** and determine **Vout** for each step → generates the **VTC curve**.

The resulting curve shows:

* Logic HIGH region
* Transition (switching) region
* Logic LOW region

This curve provides insight into **threshold voltage behavior**, **noise margins**, and **inverter performance**.

---

### Key Insights from CMOS VTC Analysis

| Concept                      | Explanation                                                                   |
| ---------------------------- | ----------------------------------------------------------------------------- |
| **Noise Margins**            | Determine how much input noise the inverter can tolerate without logic error. |
| **Switching Threshold (Vm)** | Input voltage where Vout = Vin; both transistors conduct.                     |
| **Propagation Delay**        | Time for Vout to respond to changes in Vin.                                   |
| **Symmetry**                 | Balanced PMOS/NMOS sizing ensures equal rise and fall transitions.            |
| **Power Efficiency**         | CMOS inverter consumes power only during switching (dynamic power).           |

---

### Benefits of Performing CMOS VTC Analysis

| Benefit                              | Description                                                                                                                            |
| ------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------- |
| **Fundamental Understanding**     | Builds intuition about how CMOS logic gates operate at the device level.                                                               |
| **Design Optimization**            | Helps choose transistor sizes to balance switching threshold, delay, and power.                                                        |
| **Noise Margin Estimation**       | Enables accurate calculation of NML (Noise Margin Low) and NMH (Noise Margin High).                                                    |
| **Performance Evaluation**        | Reveals how voltage scaling or process variation affects inverter stability.                                                           |
| **Transition Region Analysis**    | Identifies the region where both transistors conduct, impacting short-circuit power.                                                   |
| **Foundation for Complex Gates** | Understanding inverter VTC aids in designing NAND, NOR, and other CMOS logic gates.                                                    |




### 🧩 **Summary**

| **Topic**                                                    | **Description**                                                                              |
| ------------------------------------------------------------ | -------------------------------------------------------------------------------------------- |
| [**NMOS SPICE Simulation**](#nmos-spice-simulation)          | Simulated Id–Vds and Id–Vgs curves for NMOS using sky130 models to study device behavior.    |
| [**Long vs Short Channel**](#long-vs-short-channel-behavior) | Compared characteristics; short-channel shows velocity saturation and reduced drain current. |
| [**Velocity Saturation**](#velocity-saturation)              | Explained carrier velocity limiting effect at high electric fields in scaled devices.        |
| [**CMOS Inverter (VTC)**](#cmos-inverter-vtc-analysis)       | Analyzed voltage transfer characteristics, switching point, and logic transitions.           |
| [**Key Insights**](#key-observations--insights)              | Highlighted design trade-offs: speed vs current, noise margins, and power efficiency.        |
