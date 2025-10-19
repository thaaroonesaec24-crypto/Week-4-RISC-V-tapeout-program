
# Table of Contents

* [Static Behaviour Evaluation — Power Supply Variation](#static-behaviour-evaluation--power-supply-variation)

  * [Sky130 Supply Variation Labs](#sky130-supply-variation-labs)
* [How to Calculate Gain from SPICE VTC Plot](#how-to-calculate-gain-from-spice-vtc-plot)
* [Device Variation and CMOS Inverter Robustness](#device-variation-and-cmos-inverter-robustness)

  * [Etching Variation](#etching-variation)
  * [Oxide Thickness (Tox) Variation](#oxide-thickness-tox-variation)
  * [Transistor Strength Definitions](#transistor-strength-definitions)
* [Overall Needs for Device Variation Analysis](#overall-needs-for-device-variation-analysis)
* [Benefits of Device Variation Analysis](#benefits-of-device-variation-analysis)
* [CMOS Inverter Robustness Summary](#cmos-inverter-robustness-summary)

---

# CMOS Power Supply and Device Variation Robustness Evaluation


## Static Behaviour Evaluation — Power Supply Variation

**Overview:**

Power supply scaling affects the **static behavior** of CMOS inverters, including:

* Switching threshold voltage (**Vm**)
* Noise margins (**NML/NMH**)
* Overall circuit robustness

**Simulation Setup:**

* CMOS inverter simulated at multiple supply voltages: `Vdd = 2.5V → 1V`
* PMOS/NMOS transistor sizes remain constant: `Wp = 0.9375 μm`, `Wn = 0.375 μm`

**Observations:**

| Parameter                    | Observation                                                           |
| ---------------------------- | --------------------------------------------------------------------- |
| **Switching Threshold (Vm)** | Moves toward center of supply range as Vdd decreases                  |
| **Noise Margins**            | Shrink with lower Vdd, reducing tolerance to input noise              |
| **Performance**              | Low Vdd reduces power consumption but may slow switching speed        |
| **Trade-off**                | High Vdd improves noise margin but increases static and dynamic power |

**Design Insight:**

* Power scaling is a key method for **low-power CMOS design**.
* Designers must balance **energy efficiency** with **noise immunity** and **speed**.

---

### Sky130 Supply Variation Labs

**Example SPICE Simulation Commands:**

```bash
ngspice day5_inv_vdd_2p5V.spice
plot out vs in

ngspice day5_inv_vdd_1V.spice
plot out vs in
```

**Purpose:** Observe VTC shifts and quantify noise margin changes under different supply voltages.

---

## Static Behaviour Evaluation — Device Variation

Device-level variations arise from **process inconsistencies** during fabrication and can significantly impact inverter behavior.

### Etching Variation

* Refers to **dimensional changes in transistor widths and lengths** due to fabrication tolerances.
* Affects **drive current**, **switching threshold**, and **rise/fall times**.
* Designers must ensure sizing margins accommodate etching variations.

### Oxide Thickness (Tox) Variation

* Oxide layer variations change **transistor gate capacitance**, affecting **threshold voltage (Vth)** and **switching speed**.
* Thicker oxide → reduced capacitance → slower switching
* Thinner oxide → higher leakage → potential reliability issues

### Transistor Strength Definitions

* Transistor strength is proportional to **W/L ratio** and **carrier mobility**.
* Stronger PMOS/NMOS affects **Vm**, rise/fall symmetry, and overall inverter timing.
* Strength adjustment is a primary design knob for mitigating PVT (Process, Voltage, Temperature) effects.

---

### Sky130 Device Variation Labs

**Example SPICE Commands for Device Variation Analysis:**

```bash
ngspice day5_inv_tox_variation.spice
plot out vs in

ngspice day5_inv_width_variation.spice
plot out vs in
```

**Purpose:** Evaluate inverter robustness to variations in **Tox**, transistor widths, and strengths.

---

## Benefits of Power and Device Variation Analysis

| **Benefit**                      | **Description / Impact**                                                                 |
| -------------------------------- | ---------------------------------------------------------------------------------------- |
| **Enhanced Design Robustness**   | Ensures inverter operates reliably under Vdd scaling and fabrication variations.         |
| **Noise Margin Optimization**    | Helps quantify and maximize NML and NMH under supply and process fluctuations.           |
| **Energy Efficiency Evaluation** | Enables low-power design by analyzing Vdd reduction effects on performance and gain.     |
| **Performance Assessment**       | Detects changes in rise/fall times and switching delays due to supply or device changes. |
| **Process Variation Mitigation** | Guides sizing and transistor strength adjustments to handle etching and Tox variations.  |
| **Pre-fabrication Validation**   | Confirms inverter stability under real-world conditions, reducing risk in tapeout.       |
| **Educational Insight**          | Demonstrates practical effects of supply and process variations on CMOS circuits.        |
| **Design Optimization**          | Balances trade-offs between power, speed, and noise margin for robust digital design.    |

---


## Sky130 Supply Variation Labs
 <details> <summary><strong>day5_inv_supplyvariation_Wp1_Wn036.spice</strong></summary>

*Model Description

.param temp=27


*Including sky130 library files

.lib "sky130_fd_pr/models/sky130.lib.spice" tt



*Netlist Description



XM1 out in vdd vdd sky130_fd_pr__pfet_01v8 w=1 l=0.15

XM2 out in 0 0 sky130_fd_pr__nfet_01v8 w=0.36 l=0.15



Cload out 0 50fF


Vdd vdd 0 1.8V

Vin in 0 1.8V


.control


let powersupply = 1.8

alter Vdd = powersupply

let voltagesupplyvariation = 0

dowhile voltagesupplyvariation < 6

dc Vin 0 1.8 0.01

let powersupply = powersupply - 0.2

alter Vdd = powersupply

let voltagesupplyvariation = voltagesupplyvariation + 1

      end

 
plot dc1.out vs in dc2.out vs in dc3.out vs in dc4.out vs in dc5.out vs in dc6.out vs in xlabel "input voltage(V)" ylabel "output voltage(V)" title "Inveter dc characteristics as a function of supply voltage"


.endc


.end

</details>

📈plot the waveforms in ngspice
```
ngspice day5_inv_supplyvariation_Wp1_Wn036.spice
plot out vs in
```

![](https://github.com/thaaroonesaec24-crypto/Week-4-RISC-V-tapeout-program/blob/main/DAY%205/images/1%20week%204%20day%205%20.png)
---

## How to Calculate Gain from SPICE VTC Plot

The **gain of a CMOS inverter** can be derived from the **Voltage Transfer Characteristic (VTC)** plot obtained in SPICE:

1. Click on the **PMOS slope** (left side of transition) — Terminal displays:
   `x0 = Vin`, `y0 = Vout`
2. Click on the **NMOS slope** (right side of transition) — Terminal displays:
   `x1 = Vin`, `y1 = Vout`
3. Compute the **gain**:

[
\text{Gain} = \frac{y0 - y1}{x0 - x1}
]

> Gain represents the **steepness of the VTC transition**, indicating how sharply the inverter switches from logic '1' to '0'.

---

## Device Variation and CMOS Inverter Robustness

Device variation is a **critical factor in determining inverter robustness**, caused by **process fluctuations** during fabrication. The most common variations include:

* **Etching Variation** (affects transistor dimensions W and L)
* **Oxide Thickness Variation (Tox)** (affects gate capacitance and threshold voltage)

These variations directly influence the **switching threshold (Vm)**, **noise margins**, and **overall reliability** of the inverter.

---

### Etching Variation

**Definition:**

Etching defines the **physical structures** of the transistor in CMOS layout, including:

* **Width (W)** and **Length (L)** of PMOS and NMOS gates
* Poly-silicon layer thickness (affects gate length)
* Metal layers and contacts

**Impact on Device Performance:**

| Effect                  | Description                                                            |
| ----------------------- | ---------------------------------------------------------------------- |
| **Drain Current (Id)**  | Changes proportionally with W/L ratio — directly affects current drive |
| **Switching Threshold** | Vm may shift due to altered PMOS/NMOS balance                          |
| **Noise Margins**       | NML/NMH affected by current imbalance                                  |
| **Delay / Speed**       | Rise/fall times may vary across inverters in a chain                   |
| **Robustness**          | Overall tolerance to process variation decreases                       |

**Key Need:** Designers must **account for W and L variations** in layout to maintain consistent performance.

---

### Oxide Thickness (Tox) Variation

**Definition:**

* Variations in **gate oxide thickness** occur during fabrication.
* Affects **gate capacitance (Cox)**, which impacts **MOSFET drain current (Id)** and inverter timing.

**Impact on Device Performance:**

| Effect                  | Description                                                       |
| ----------------------- | ----------------------------------------------------------------- |
| **Drain Current (Id)**  | Id ∝ Cox → thinner oxide increases Id, thicker oxide decreases Id |
| **Switching Threshold** | Vm may shift depending on oxide variation                         |
| **Rise/Fall Times**     | Can accelerate or slow down inverter response                     |
| **Noise Margins**       | NML/NMH may decrease if currents are unbalanced                   |

**Key Need:** Designers must **simulate Tox variation** to ensure robustness under process variability.

---

### Transistor Strength Definitions

Transistor **strength** is defined by the **ability to source/sink current**, which depends on **W/L ratio** and mobility:

| Transistor Type | Strength | Implementation in Layout | Effect on Inverter                      |
| --------------- | -------- | ------------------------ | --------------------------------------- |
| Strong PMOS     | High     | Wider PMOS               | Faster rising edge, stronger pull-up    |
| Weak PMOS       | Low      | Narrower PMOS            | Slower rising edge                      |
| Strong NMOS     | High     | Wider NMOS               | Faster falling edge, stronger pull-down |
| Weak NMOS       | Low      | Narrower NMOS            | Slower falling edge                     |

**Key Need:** Balancing PMOS/NMOS strengths ensures **symmetric rise/fall times** and **stable Vm**.

---

## Overall Needs for Device Variation Analysis

1. **Accurate SPICE Modeling:** Include W, L, and Tox variations in simulations.
2. **VTC Characterization:** Determine Vm shifts and gain reduction due to device variations.
3. **Noise Margin Verification:** Ensure NML/NMH remain within acceptable ranges.
4. **Transistor Sizing Optimization:** Adjust Wp/Wn ratio to compensate for fabrication deviations.
5. **Delay and Speed Analysis:** Evaluate rise/fall time changes across inverter chains.
6. **Robust Layout Practices:** Minimize process sensitivity through design margins.

---

## Benefits of Device Variation Analysis

| **Benefit**                      | **Description / Impact**                                                               |
| -------------------------------- | -------------------------------------------------------------------------------------- |
| **Improved Robustness**          | Maintains inverter operation under process variations like etching and Tox deviations. |
| **Accurate Vm Prediction**       | Ensures switching threshold remains stable across fabricated devices.                  |
| **Balanced Timing**              | Symmetric rise/fall times reduce duty cycle distortion in sequential circuits.         |
| **Noise Margin Preservation**    | NML/NMH are maintained despite process fluctuations, preventing logic errors.          |
| **Enhanced Reliability**         | Reduces risk of circuit failure due to device mismatch or process variations.          |
| **Pre-fabrication Validation**   | Identifies potential issues in layout and sizing before tapeout.                       |
| **Design Optimization Guidance** | Helps designers choose optimal transistor sizing and strength for robust performance.  |
| **Educational Insight**          | Demonstrates how real-world fabrication impacts device-level CMOS behavior.            |

---


## CMOS Inverter Robustness Summary

CMOS inverter performance is affected by **power supply scaling** and **device variations**:

* **Power Supply Variation:** Lower Vdd reduces power but shrinks noise margins; higher Vdd improves robustness at the cost of increased power.
* **Device Variation:** Fabrication differences in **transistor dimensions (W/L)** and **oxide thickness (Tox)** affect switching threshold (Vm), noise margins, gain, and speed.
* **Transistor Strength:** Balancing PMOS/NMOS sizes ensures symmetric rise/fall times and stable inverter operation.
* **SPICE Simulations:** DC and transient sweeps help quantify VTC shifts, gain, and noise margins.

**Benefits:** Improves reliability, optimizes noise margins, supports low-power design, and guides pre-fabrication validation.


