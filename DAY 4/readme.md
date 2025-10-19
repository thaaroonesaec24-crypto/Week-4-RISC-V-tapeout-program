
# Table of Contents

* [Introduction to Noise Margin](#introduction-to-noise-margin)
* [Noise Margin Definition – VTC and Undefined Region](#noise-margin-definition--vtc-and-undefined-region)
* [Noise Margin Summary – Handling Input "Bumps"](#noise-margin-summary--handling-input-bumps)
* [Sky130 Noise Margin Labs](#sky130-noise-margin-labs)
* [Benefits of Noise Margin Analysis](#benefits-of-noise-margin-analysis)
* [Summary: CMOS Inverter Noise Margin](#summary-cmos-inverter-noise-margin)

---


## Static Behavior Evaluation: CMOS Inverter Robustness and Noise Margin


### Introduction to Noise Margin

Noise margin is the **maximum noise voltage** a CMOS circuit can tolerate without causing logic errors.

* **Ideal Inverter**: Infinite slope at `Vdd/2` — instantaneous switching.
* **Actual CMOS Inverter**: Finite slope — gradual transition region, causing an undefined region where logic levels are unstable.

**Key Points:**

| Parameter              | Description                                                   |
| ---------------------- | ------------------------------------------------------------- |
| **VIL**                | Input voltage threshold for logic '0'.                        |
| **VIH**                | Input voltage threshold for logic '1'.                        |
| **VOL**                | Output voltage considered as logic '0'.                       |
| **VOH**                | Output voltage considered as logic '1'.                       |
| **Undefined Region**   | Input range between `VIL` and `VIH` — output logic undefined. |
| **Noise Margin (NML)** | NML = VIL − VOL — tolerance for logic '0' noise.              |
| **Noise Margin (NMH)** | NMH = VOH − VIH — tolerance for logic '1' noise.              |

Noise margins ensure that **signals remain correctly interpreted** despite small voltage disturbances.

---

### Noise Margin Definition — VTC and Undefined Region

The **Voltage Transfer Characteristic (VTC)** of a CMOS inverter allows us to extract key noise margin parameters:

| Parameter | Definition                                                        | Significance                                      |
| --------- | ----------------------------------------------------------------- | ------------------------------------------------- |
| **VIL**   | Input voltage at the lower point where slope = −1                 | Max input voltage recognized as logic '0'         |
| **VIH**   | Input voltage at the upper point where slope = −1                 | Min input voltage recognized as logic '1'         |
| **VOL**   | Minimum output voltage when input is high enough to turn off NMOS | Output logic '0'                                  |
| **VOH**   | Maximum output voltage when input is low enough to turn off PMOS  | Output logic '1'                                  |
| **NML**   | VIL − VOL                                                         | Low noise margin — tolerance for input '0' noise  |
| **NMH**   | VOH − VIH                                                         | High noise margin — tolerance for input '1' noise |

**Undefined Region**:

* Between `VIL` and `VIH`, the output cannot be guaranteed as logic '0' or '1'.
* Noise in this region can propagate errors downstream.

**Designer Goal:** Maximize **NML** and **NMH** to enhance inverter robustness.

---

### Noise Margin Summary — Handling Input "Bumps"

Noise margins ensure that small **transient noise voltages** (or "bumps") do not cause logic errors.

| Input Bump Range | Output Interpretation | Reliability   |
| ---------------- | --------------------- | ------------- |
| 0 → VIL          | Treated as logic '0'  | Safe          |
| VIL → VIH        | Undefined output      | Risk of error |
| VIH → VOH        | Treated as logic '1'  | Safe          |

**Example:**

| Parameter | Value Example (Vdd = 1.8 V) |
| --------- | --------------------------- |
| VOL       | 0.05 V                      |
| VIL       | 0.2 V                       |
| VIH       | 1.6 V                       |
| VOH       | 1.75 V                      |
| NML       | 0.2 − 0.05 = 0.15 V         |
| NMH       | 1.75 − 1.6 = 0.15 V         |

The signal must remain **within NML or NMH** for proper logic recognition.

---

### Sky130 Noise Margin Labs

**DC Sweep Simulation Example (VTC):**

| SPICE File                        | Purpose                            | Key Observation                         |
| --------------------------------- | ---------------------------------- | --------------------------------------- |
| `day4_inv_vtc_Wp084_Wn036.spice`  | DC Sweep to plot Vout vs Vin (VTC) | Extract VIL, VIH, VOL, VOH, NML, NMH    |
| `day4_inv_tran_Wp084_Wn036.spice` | Transient analysis                 | Observe voltage response to pulse input |

**NGSPICE Command Examples:**

```bash
ngspice day4_inv_vtc_Wp084_Wn036.spice
plot out vs in
```

```bash
ngspice day4_inv_tran_Wp084_Wn036.spice
plot out vs time
```

These simulations **quantify noise margins** and verify inverter robustness under realistic load and input conditions.

---

### Benefits of Noise Margin Analysis

| **Benefit**                         | **Description / Impact**                                                               |
| ----------------------------------- | -------------------------------------------------------------------------------------- |
| **Enhanced Logic Reliability**      | Determines tolerance for noise without logic errors.                                   |
| **Design Robustness**               | Ensures CMOS circuits function correctly under voltage fluctuations.                   |
| **Optimization of Sizing**          | Guides Wp/Wn ratio adjustment to maximize noise margins.                               |
| **Error Prevention**                | Identifies undefined input regions where errors can occur.                             |
| **Educational Insight**             | Demonstrates practical effect of voltage noise and input variations on CMOS logic.     |
| **Verification Before Fabrication** | Confirms inverter meets noise tolerance requirements, avoiding costly errors post-fab. |

---

### Key Takeaways:

1. Noise margins are critical for **robust digital CMOS design**.
2. VTC analysis allows precise extraction of **VIL, VIH, VOL, VOH, NML, NMH**.
3. Undefined regions highlight the **danger zone for logic instability**.
4. Proper sizing and layout ensure **balanced noise margins** and reliable operation.

---

## Sky130 Noise margin labs
 <details> <summary><strong>day4_inv_noisemargin_wp1_wn036.spice</strong></summary>

**Model Description
.param temp=27


*Including sky130 library files
.lib "sky130_fd_pr/models/sky130.lib.spice" tt


*Netlist Description


XM1 out in vdd vdd sky130_fd_pr__pfet_01v8 w=1 l=0.15
XM2 out in 0 0 sky130_fd_pr__nfet_01v8 w=0.36 l=0.15


Cload out 0 50fF

Vdd vdd 0 1.8V
Vin in 0 1.8V

*simulation commands

.op

.dc Vin 0 1.8 0.01

.control
run
setplot dc1
display
.endc

.end

</details>

📈plot the waveforms in ngspice
```
ngspice day4_inv_noisemargin_wp1_wn036.spice
plot out vs in
```
![](https://github.com/thaaroonesaec24-crypto/Week-4-RISC-V-tapeout-program/blob/main/DAY%204/images/week%204%20day%204.png)


Here’s a **concise GitHub-ready summary** for your “CMOS Inverter Noise Margin” section, keeping it clear, professional, and informative:

---

## Summary: CMOS Inverter Noise Margin

| **Topic**                   | **Key Points / Description**                                                                                                                                         |
| --------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Noise Margin Definition** | Maximum voltage noise a CMOS inverter can tolerate without causing logic errors. Measured using VTC (Voltage Transfer Characteristic).                               |
| **Key Parameters**          | **VIL** (input low threshold), **VIH** (input high threshold), **VOL** (output low), **VOH** (output high), **NML** (low noise margin), **NMH** (high noise margin). |
| **Undefined Region**        | Input range between `VIL` and `VIH` where output logic is unpredictable; noise in this region can propagate errors.                                                  |
| **Handling Input Noise**    | Input bumps within NML or NMH are tolerated; bumps in undefined region risk logic errors.                                                                            |
| **Sky130 Labs**             | DC sweep (`.dc`) extracts VIL, VIH, VOL, VOH, NML, NMH; transient (`.tran`) simulations validate inverter response to pulse inputs.                                  |



