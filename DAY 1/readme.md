# Introduction to Circuit Design and SPICE simulations
Circuit design is the creation of electronic circuits to perform specific functions by connecting components in an optimized way. SPICE simulation is a computer-based tool that models and analyzes these circuits mathematically before physical construction, predicting their behavior under various conditions for verification and optimization.
---
## Why do we need SPICE simulation ?

| **Purpose**                 | **What SPICE Does**                                | **Example Application**                   |
| --------------------------- | -------------------------------------------------- | ----------------------------------------- |
| **Functional Verification** | Confirms circuit logic and analog behavior         | Check CMOS inverter switching             |
| **Performance Analysis**    | Measures speed, delay, and waveform integrity      | Evaluate timing of digital paths          |
| **Power Estimation**        | Calculates energy and leakage consumption          | Optimize dynamic power in logic gates     |
| **Design Optimization**     | Tunes parameters for best performance              | Adjust bias or feedback for stability     |
| **PVT Validation**          | Tests circuit under multiple real-world conditions | Compare `tt`, `ff`, `ss` corner responses |
---
## Circuit Diagram Of Inverter
![](https://github.com/thaaroonesaec24-crypto/Week-4-RISC-V-tapeout-program/blob/main/DAY%201/images/Screenshot%202025-10-15%20230043.png)

## SPICE Simulation 
![](https://github.com/thaaroonesaec24-crypto/Week-4-RISC-V-tapeout-program/blob/main/DAY%201/images/Screenshot%202025-10-15%20225424.png)

---

## Understanding Delay Tables in Digital Timing Analysis

In modern digital design, **cell delay** is not a constant parameter. Instead, it varies dynamically depending on multiple electrical conditions. Two primary factors influence the propagation delay of a standard cell:

1. **Input Slew** – The rate at which the input signal transitions between logic levels.
2. **Output Load** – The total capacitance that the output node must drive.

To model these dependencies, **delay information is represented in two-dimensional lookup tables (2D-LUTs)**, commonly stored within Liberty (`.lib`) files used by Static Timing Analysis (STA) tools.

---
![Delay table]()
---
### Structure of a Delay Lookup Table

Each standard cell, such as a buffer (e.g., `CBUF`), includes delay tables that define how propagation delay changes with varying input and output conditions.

| **Table Axis** | **Represents** | **Example Values**                    |
| -------------- | -------------- | ------------------------------------- |
| **Rows**       | Input Slew     | 20 ps, 40 ps, 60 ps, 80 ps            |
| **Columns**    | Output Load    | 10 fF → 110 fF                        |
| **Entries**    | Delay Values   | Interpolated delay for each condition |

For instance:

* **CBUF1** might have delay entries labeled *d1–d24*.
* **CBUF2** might have delay entries labeled *e1–e24*.

During STA, the tool interpolates between these values to determine the **actual delay** for a given combination of slew and load conditions. This ensures realistic and process-aware timing estimation.

---

### Calculating Total Output Capacitance

When a cell output drives multiple fanout cells or nets, the **total output capacitance** seen by that cell is the sum of all capacitive loads connected to its output node. These loads include:

* Input capacitances of driven cells
* Capacitance from interconnect (wire segments)
* Intrinsic output capacitance of the driving cell

In CMOS-based designs, only input pins contribute capacitive loads, as they do not draw DC current.

---

#### Example: Total Capacitance Estimation

| **Node** | **Connected Loads**         | **Capacitance Formula** | **Total Capacitance** |
| -------- | --------------------------- | ----------------------- | --------------------- |
| **A**    | Two buffer inputs (`CBUF2`) | 30 fF + 30 fF           | **60 fF**             |
| **B**    | Load capacitors C1 and C2   | 25 fF + 25 fF           | **50 fF**             |
| **C**    | Load capacitors C3 and C4   | 25 fF + 25 fF           | **50 fF**             |

---

### Key Takeaway

Delay tables bridge the gap between **library characterization** and **timing verification**, allowing STA tools like OpenSTA to perform accurate delay interpolation. By considering both **input slew** and **output load**, designers can evaluate true circuit performance under realistic operating conditions.

---
Here’s a **professionally rewritten and unidentifiable** version of your “Delay Calculation from LUTs” section — polished for use in a GitHub README or technical report (fits perfectly after the “Understanding Delay Tables” section):

---

### Example 1: Interpolating Delay for Intermediate Load Values

Consider a buffer cell (`CBUF1`) where we need to determine the propagation delay under the following conditions:

* **Input Slew:** 40 ps
* **Output Load:** 60 fF

However, 60 fF does **not** exactly appear in the table. The closest available load points are:

| **Load (fF)** | **Delay Value** |
| ------------- | --------------- |
| 50 fF         | x₉              |
| 70 fF         | x₁₀             |

To estimate the delay for 60 fF, we apply **linear interpolation** between these two entries:

[
\text{Delay}*{60fF} = x_9 + \frac{(60 - 50)}{(70 - 50)} \times (x*{10} - x_9)
]

This formula proportionally estimates the delay between the two known points, providing an accurate timing value for intermediate conditions.

---

### Example 2: Direct Lookup for Exact Match

Now consider another buffer cell (`CBUF2`) with the following parameters:

* **Input Slew:** 60 ps
* **Output Load:** 50 fF

In this case, the 50 fF column exists directly in the LUT, so **no interpolation** is required. The delay can be extracted straight from the table:

| **Condition** | **Value** |
| ------------- | --------- |
| Input Slew    | 60 ps     |
| Output Load   | 50 fF     |
| LUT Entry     | y₁₅       |

Hence, the propagation delay for `CBUF2` under these conditions is **y₁₅**.

---

### ⚠️ Important Consideration

If either the input slew or output load lies **outside the characterized LUT range** (for example, <10 fF or >110 fF), the STA tool performs **extrapolation** to predict delay values.
However, extrapolated results are **less accurate** and may deviate from actual transistor-level (SPICE) behavior. Therefore, designers should strive to operate within the defined characterization limits for reliable timing closure.

---

## DAY 1 LAB
Step 1 : Clone the repo
```
git clone https://github.com/kunalg123/sky130CircuitDesignWorkshop.git
```
Step 2 : DOWNLOAD ngspice 
  ```
  sudo apt install ngspice 
```
Sstep 3 : To Plot in ngspice Use the command 
```
ngspice day1_nfet_idvds_L2_W5.spice
plot -vdd#branch
```

![](https://github.com/thaaroonesaec24-crypto/Week-4-RISC-V-tapeout-program/blob/main/DAY%201/images/week%204%20day%201%20lab.png)

**Explanation of NGSPICE Commands**

| **Command**                           | **Description**                                                                                                                                                       | **Purpose / Output**                                                                                                                 |
| ------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| `ngspice day1_nfet_idvds_L2_W5.spice` | Launches the NGSPICE simulator and loads the specified SPICE netlist file (`day1_nfet_idvds_L2_W5.spice`).                                                            | Starts simulation for the NMOS ID–VDS characteristic defined in the given file.                                                      |
| `plot -vdd#branch`                    | After simulation, this command plots the **current flowing through the VDD voltage source**. The minus sign (`-`) indicates current direction (from source to drain). | Displays the **ID–VDS curve** or output characteristics of the NMOS transistor based on the sweep conditions defined in the netlist. |

