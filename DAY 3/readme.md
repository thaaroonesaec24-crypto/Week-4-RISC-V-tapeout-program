

##  CMOS Switching Threshold and Dynamic Simulations
---
###  **Voltage Transfer Characteristics and SPICE Simulations**

The CMOS inverter’s SPICE simulation analyzes how **input voltage (Vin)** affects **output voltage (Vout)** through **Voltage Transfer Characteristics (VTC)**.
This helps determine **switching behavior**, **noise margins**, and **threshold voltage** — key for reliable digital design.

---

###  **CMOS Inverter SPICE Deck Structure**

| **Section**               | **Description**                                     | **Example Command**                       |
| ------------------------- | --------------------------------------------------- | ----------------------------------------- |
| **Transistor Definition** | Defines PMOS and NMOS with geometry and connections | `M1 out in vdd vdd pmos W=0.375u L=0.25u` |
| **Load Capacitance**      | Models the inverter’s output capacitance            | `cload out 0 10f`                         |
| **Voltage Sources**       | Provides DC supply and input bias                   | `Vdd vdd 0 2.5`, `Vin in 0 2.5`           |
| **Simulation Controls**   | Commands for analysis types                         | `.op`, `.dc Vin 0 2.5 0.05`               |
| **Model Files**           | Includes process-specific transistor models         | `.include tsmc_025um_model.mod`           |
| **End Statement**         | Marks simulation termination                        | `.end`                                    |

---

###  **Simulation Objectives and Insights**

| **Simulation Type**          | **Purpose**                            | **Expected Output**                        |
| ---------------------------- | -------------------------------------- | ------------------------------------------ |
| **.op (Operating Point)**    | Finds DC bias point of inverter        | Static voltages and currents               |
| **.dc Sweep**                | Plots Vout vs Vin to get **VTC curve** | Switching threshold (Vm) and noise margins |
| **.tran (Transient)**        | Studies inverter’s dynamic response    | Rise/fall times, propagation delay         |
| **Switching Threshold (Vm)** | Voltage where Vin = Vout               | Defines logic transition midpoint          |

---

###  **Key Takeaways**

* The **SPICE deck** captures essential electrical behavior of a CMOS inverter.
* **Voltage Transfer Characteristics (VTC)** reveal the inverter’s **switching point** and **noise robustness**.
* **Transient simulations** provide dynamic performance metrics — essential for high-speed CMOS design.
* These simulations help **optimize transistor sizing**, **power efficiency**, and **signal integrity** in real CMOS circuits.



### **Benefits of CMOS Switching Threshold and Dynamic Simulations**

| **Benefit**                                    | **Description / Impact**                                                                                          |
| ---------------------------------------------- | ----------------------------------------------------------------------------------------------------------------- |
| **1. Accurate Switching Point Identification** | Helps determine the **inverter threshold voltage (Vm)**, crucial for defining logic levels and signal integrity.  |
| **2. Improved Noise Margin Analysis**          | Evaluates **VIL, VIH, VOL, and VOH**, ensuring stable operation against input noise and voltage fluctuations.     |
| **3. Performance Optimization**                | Guides optimization of **rise/fall times**, **propagation delay**, and **transistor sizing** for faster circuits. |
| **4. Power Efficiency Evaluation**             | Allows analysis of **static and dynamic power consumption** under different load and voltage conditions.          |
| **5. Reliability Testing**                     | Helps predict **robustness under PVT variations** (Process, Voltage, Temperature), improving circuit stability.   |
| **6. Educational Insight**                     | Enhances understanding of **device-level behavior** and **CMOS switching physics** for learners and researchers.  |
| **7. Design Validation**                       | Validates that the **simulated inverter** meets digital design standards before layout or fabrication.            |
| **8. Visualization of Dynamic Response**       | Enables observation of **transient waveforms** (charging/discharging), reinforcing timing behavior understanding. |

---

Would you like me to add a **“Why include this section in your GitHub repo”** paragraph after the table (to make it sound more project-oriented)?


# Labs Sky130 SPICE simulation for CMOS

  <details> <summary><strong> day3_inv_vtc_Wp084_Wn036.spice</strong></summary>

*Model Description

.param temp=27


*Including sky130 library files

.lib "sky130_fd_pr/models/sky130.lib.spice" tt


*Netlist Description


XM1 out in vdd vdd sky130_fd_pr__pfet_01v8 w=0.84 l=0.15

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
ngspice day3_inv_vtc_Wp084_Wn036.spice
plot out vs in
```

![](https://github.com/thaaroonesaec24-crypto/Week-4-RISC-V-tapeout-program/blob/main/DAY%203/images/day3_inv_vtc_Wp084_Wn036.spice%20week%204%20day%203.png)




  <details> <summary><strong> day3_inv_tran_Wp084_Wn036.spice</strong></summary>


*Model Description
.param temp=27


*Including sky130 library files
.lib "sky130_fd_pr/models/sky130.lib.spice" tt


*Netlist Description


XM1 out in vdd vdd sky130_fd_pr__pfet_01v8 w=0.84 l=0.15
XM2 out in 0 0 sky130_fd_pr__nfet_01v8 w=0.36 l=0.15


Cload out 0 50fF

Vdd vdd 0 1.8V
Vin in 0 PULSE(0V 1.8V 0 0.1ns 0.1ns 2ns 4ns)

*simulation commands

.tran 1n 10n

.control
run
.endc

.end

</details>


📈plot the waveforms in ngspice
```
ngspice day3_inv_vtc_Wp084_Wn036.spice
plot out vs in
```

![](https://github.com/thaaroonesaec24-crypto/Week-4-RISC-V-tapeout-program/blob/main/DAY%203/images/day3_inv_tran_Wp084_Wn036.spice%20week%204%20day%203.png)



# CMOS Switching Threshold and Dynamic Simulations

This section describes **transient analysis of CMOS inverters**, **comparison with DC voltage transfer characteristics**, and evaluation of **switching threshold voltage (Vm)**. These analyses are crucial for understanding **timing behavior, rise/fall delays, and inverter robustness** in CMOS circuits.

---

## Voltage Transfer Characteristics vs. Transient Analysis

Transient analysis helps visualize how a CMOS inverter responds to **time-varying inputs**, measuring **rise and fall delays** and the dynamic switching behavior.

### Rise and Fall Time Calculation

* **Output Rise Time (tr)** = Time at 50% of rising edge − Time at 50% of previous falling edge
* **Output Fall Time (tf)** = Time at 50% of falling edge − Time at 50% of previous rising edge

### Comparison: DC Sweep vs. Transient Simulation

| Aspect                 | Voltage Transfer Characteristics (DC Sweep) | Transient Response (Dynamic Simulation)               |
| ---------------------- | ------------------------------------------- | ----------------------------------------------------- |
| **Simulation Type**    | DC Sweep (VTC curve)                        | Transient analysis over time (dynamic behavior)       |
| **Input Signal (Vin)** | Fixed DC sweep (0V → Vdd in steps)          | Pulse waveform (0V → Vdd) with defined width & period |
| **Simulation Command** | `.dc Vin 0 Vdd step`                        | `.tran Tstep Tstop`                                   |
| **Purpose**            | Plot static inverter VTC                    | Analyze rise/fall times and propagation delays        |
| **Typical Output**     | Vout vs Vin                                 | Vout vs Time, Vin vs Time                             |
| **Analysis Goal**      | Switching threshold, noise margins          | Timing parameters: tr, tf, propagation delay          |

---

## Static Behavior Evaluation — CMOS Inverter Robustness

The **robustness of a CMOS inverter** depends on:

* Switching Threshold Voltage (**Vm**)
* Noise Margin
* Power Supply Variation
* Device Variations

### Switching Threshold Voltage (Vm)

* Defined as the input voltage where **Vin = Vout**.
* At Vm, **NMOS and PMOS** transistors conduct simultaneously, typically in **saturation**, providing high voltage gain.
* Vm is influenced by **transistor sizing (Wp/Lp vs Wn/Ln)** and mobility differences.

**Example of Vm with different sizing:**

| Wp / Wn Ratio | Vm (Switching Threshold) | Observation on Delay                        |
| ------------- | ------------------------ | ------------------------------------------- |
| 1:1           | ~0.98 V                  | Slightly faster fall time, slower rise time |
| 3.75:1        | ~1.2 V                   | Balanced rise and fall delays (~80 ps)      |

---

### Regions of Operation Around Vm

* PMOS Linear / NMOS OFF
* PMOS Linear / NMOS Saturation
* PMOS Saturation / NMOS Saturation → **Vm located here**
* PMOS Saturation / NMOS Linear
* PMOS OFF / NMOS Linear

At Vm, the **currents satisfy the condition**:

[
I_{D(PMOS)} + I_{D(NMOS)} = 0
]

This ensures **current balance** and defines the **switching point**.

---

### Current Balancing and Sizing Ratios

* Vm depends on the **PMOS/NMOS sizing ratio** and transistor mobility.
* By choosing an appropriate **Wp/Wn ratio**, designers can achieve **balanced rise and fall times**.
* Balanced rise/fall delays prevent **duty cycle distortion** in clock buffers, which is critical for synchronous digital circuits.

---

### Benefits of CMOS Switching Threshold and Dynamic Simulations

| **Benefit**                          | **Description / Impact**                                                                     |
| ------------------------------------ | -------------------------------------------------------------------------------------------- |
| **Accurate Threshold Determination** | Determines Vm for stable logic transitions, ensuring signal integrity.                       |
| **Timing Characterization**          | Measures rise/fall delays, propagation delay, and switching speed.                           |
| **Design Optimization**              | Guides transistor sizing to balance speed, delay, and power consumption.                     |
| **Noise Margin Evaluation**          | Ensures robust operation under input noise and voltage variations.                           |
| **Dynamic Behavior Analysis**        | Visualizes inverter response to pulse inputs for realistic operational conditions.           |
| **Power Efficiency Assessment**      | Helps estimate short-circuit and dynamic power during switching.                             |
| **Duty Cycle Maintenance**           | Ensures clock buffers operate with minimal distortion in digital systems.                    |
| **Robustness Against Variations**    | Accounts for process, voltage, and temperature (PVT) variations affecting inverter behavior. |

---
## Summary

| **Topic**                                  | **Description / Key Points**                                                                                                                                                                          |
| ------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **CMOS Inverter SPICE Simulation**         | SPICE deck models PMOS/NMOS, load, and voltage sources; enables VTC and transient simulations.                                                                                                        |
| **Voltage Transfer Characteristics (VTC)** | Plots Vout vs Vin; determines switching threshold (Vm), logic levels, and noise margins.                                                                                                              |
| **Transient Analysis**                     | Pulse input applied; measures rise time (tr), fall time (tf), and propagation delay.                                                                                                                  |
| **Switching Threshold Voltage (Vm)**       | Input voltage where Vin = Vout; defines logic midpoint; influenced by PMOS/NMOS sizing.                                                                                                               |
| **Regions of Operation**                   | PMOS/NMOS linear, saturation, OFF regions; Vm occurs when currents balance (Idp + Idn = 0).                                                                                                           |
| **Current Balancing & Sizing**             | Appropriate Wp/Wn ratio ensures balanced rise/fall delays, reduces duty cycle distortion.                                                                                                             |
| **Comparison DC Sweep vs Transient**       | DC: static VTC, switching point & noise margins. Transient: dynamic delays and waveform timing.                                                                                                       |
| **Benefits of Simulations**                | Accurate threshold, timing characterization, design optimization, noise margin evaluation, power efficiency, dynamic behavior visualization, duty cycle maintenance, robustness under PVT variations. |

