# Introduction to Circuit Design and SPICE simulations
Circuit design is the creation of electronic circuits to perform specific functions by connecting components in an optimized way. SPICE simulation is a computer-based tool that models and analyzes these circuits mathematically before physical construction, predicting their behavior under various conditions for verification and optimization.
---
## 1. Why do we need SPICE simulation ?

| **Purpose**                 | **What SPICE Does**                                | **Example Application**                   |
| --------------------------- | -------------------------------------------------- | ----------------------------------------- |
| **Functional Verification** | Confirms circuit logic and analog behavior         | Check CMOS inverter switching             |
| **Performance Analysis**    | Measures speed, delay, and waveform integrity      | Evaluate timing of digital paths          |
| **Power Estimation**        | Calculates energy and leakage consumption          | Optimize dynamic power in logic gates     |
| **Design Optimization**     | Tunes parameters for best performance              | Adjust bias or feedback for stability     |
| **PVT Validation**          | Tests circuit under multiple real-world conditions | Compare `tt`, `ff`, `ss` corner responses |
---
### Circuit Diagram Of Inverter
![](https://github.com/thaaroonesaec24-crypto/Week-4-RISC-V-tapeout-program/blob/main/DAY%201/images/Screenshot%202025-10-15%20230043.png)

### SPICE Simulation 
![](https://github.com/thaaroonesaec24-crypto/Week-4-RISC-V-tapeout-program/blob/main/DAY%201/images/Screenshot%202025-10-15%20225424.png)

---

Here’s your content refined to a **professional GitHub-ready format**, maintaining all your original sections and structure while improving tone, consistency, formatting, and readability — ideal for documentation or technical report inclusion.

---

## Understanding Delay Tables in Digital Timing Analysis

In modern digital design, **cell delay** is not constant; it dynamically depends on electrical conditions. Two major parameters influence the propagation delay of a standard cell:

1. **Input Slew** – The rate at which an input signal transitions between logic levels.
2. **Output Load** – The total capacitance driven by the output node.

To capture these dependencies, delay information is stored in **two-dimensional lookup tables (2D-LUTs)**, typically within Liberty (`.lib`) files used by Static Timing Analysis (STA) tools.

---

![Delay table]()

---

### Structure of a Delay Lookup Table

Each standard cell, such as a buffer (e.g., `CBUF`), includes delay tables defining how propagation delay varies with input and output conditions.

| **Table Axis** | **Represents** | **Example Values**                    |
| -------------- | -------------- | ------------------------------------- |
| **Rows**       | Input Slew     | 20 ps, 40 ps, 60 ps, 80 ps            |
| **Columns**    | Output Load    | 10 fF → 110 fF                        |
| **Entries**    | Delay Values   | Interpolated delay for each condition |

For instance:

* **CBUF1** may contain delay entries labeled *d1–d24*.
* **CBUF2** may contain delay entries labeled *e1–e24*.

During STA, the timing tool interpolates between these entries to determine the **effective delay** for a given combination of slew and load. This ensures realistic, process-aware delay modeling for accurate analysis.

---

### Calculating Total Output Capacitance

When a cell output drives multiple fanout cells or nets, the **total output capacitance** observed at that output node equals the sum of all connected loads. These include:

* Input capacitances of driven cells
* Capacitance from interconnects (wire segments)
* Intrinsic output capacitance of the driving cell

In CMOS technology, input pins contribute only capacitive loads, as they draw negligible DC current.

```text
C_total = C_pin_output + Σ(C_pin_input_fanouts) + Σ(C_wire_segments)
```

#### Example: Total Capacitance Estimation

| **Node** | **Connected Loads**         | **Capacitance Formula** | **Total Capacitance** |
| -------- | --------------------------- | ----------------------- | --------------------- |
| **A**    | Two buffer inputs (`CBUF2`) | 30 fF + 30 fF           | **60 fF**             |
| **B**    | Load capacitors C1 and C2   | 25 fF + 25 fF           | **50 fF**             |
| **C**    | Load capacitors C3 and C4   | 25 fF + 25 fF           | **50 fF**             |

---

### Key Takeaway

Delay tables provide the bridge between **library characterization** and **timing verification**, enabling STA tools like OpenSTA to perform accurate interpolation of delay values. By accounting for both **input slew** and **output load**, designers can achieve precise timing closure and realistic circuit performance evaluation.

---

## Module 2: NMOS Transistor Physics

### Transistor Structure and Operation

An NMOS transistor has four terminals:

* **Gate (G)** – Controls the channel conductivity
* **Drain (D)** – Acts as current output
* **Source (S)** – Serves as current input
* **Bulk/Body (B)** – Substrate connection

<p align="center">
  <img src="Images/nmos_structure.png" alt="NMOS Structure" width="500"/>
  <br>
  <em>Figure 6: NMOS Transistor Physical Structure</em>
</p>

---

### Operating Regions

Let:

> **V_GS** → Gate-to-source voltage
> **V_th** → Threshold voltage

**Surface Inversion:**
Occurs when a strong positive gate voltage inverts the p-type substrate surface to n-type, forming a conductive channel.

| **Region**        | **Condition**                       | **Description**                                               |
| ----------------- | ----------------------------------- | ------------------------------------------------------------- |
| **Cut-off**       | `V_GS < V_th`                       | No inversion; drain current ≈ 0 (leakage only)                |
| **Linear/Triode** | `V_GS > V_th`, `V_DS < V_GS - V_th` | Channel forms; behaves as voltage-controlled resistor         |
| **Saturation**    | `V_GS > V_th`, `V_DS ≥ V_GS - V_th` | Channel pinches off; current mainly depends on (V_GS - V_th)² |

---

### Modified Threshold Voltage (Body Effect)

<p align="center">
  <img src="Images/threshold_eqn.png" alt="Body Effect Equation" width="400"/>
</p>

```text
V_th(V_SB) = V_th0 + γ × [√(|2φ_F| + V_SB) - √(|2φ_F|)]
```

Where:

* **γ** – Body effect coefficient (process-dependent)
* **φ_F** – Fermi potential
* **V_th0** – Zero-bias threshold voltage

Increasing body-source voltage widens the depletion region, raising the effective threshold voltage — a key factor in stacked transistor behavior.

---

### Current Equations

#### Cutoff Region

```text
V_GS < V_th:
No inversion layer formed → negligible current
Only leakage through reverse-biased junctions (pA–nA)
```

#### Linear Region

```spice
I_D = μ_n × C_ox × (W/L) × [(V_GS - V_th) × V_DS - (V_DS² / 2)]
```

#### Saturation Region

```spice
I_Dsat = (μ_n × C_ox × W) / (2L) × (V_GS - V_th)² × (1 + λ × V_DS)
```

| Symbol   | Meaning                              |
| -------- | ------------------------------------ |
| **μ_n**  | Electron mobility                    |
| **C_ox** | Gate oxide capacitance per unit area |
| **W/L**  | Transistor aspect ratio              |
| **λ**    | Channel-length modulation factor     |

---

### Drift Current Mechanism

Current arises from **carrier drift** under an applied electric field:

```text
v_drift = μ_n × E_field
I_D = Q_i(x) × v(x) × W
```

This forms the physical foundation for MOSFET current equations derived from semiconductor physics.

---

## Module 3: Hands-on SPICE Simulation

### Simulation Hierarchy

VLSI design employs multiple abstraction layers during simulation and analysis.

<p align="center">
  <img src="Images/spice_working.png" alt="Simulation Hierarchy" width="650"/>
  <br>
  <em>Figure 12: VLSI Design Simulation Hierarchy</em>
</p>

| **Level**        | **Example Tools**      | **Purpose**                          |
| ---------------- | ---------------------- | ------------------------------------ |
| **Process**      | SUPREME, TCAD          | Model fabrication and doping effects |
| **Circuit**      | SPICE, Spectre, HSPICE | Transistor-level electrical analysis |
| **Logic**        | ModelSim, VCS, Questa  | Gate-level and RTL verification      |
| **Architecture** | Gem5, SystemC          | System-level performance estimation  |

---

### SPICE Analysis Types

| **Analysis Type**      | **Command** | **Description / Use Case**                         |
| ---------------------- | ----------- | -------------------------------------------------- |
| **DC Operating Point** | `.op`       | Calculates steady-state node voltages and currents |
| **DC Sweep**           | `.dc`       | Produces I–V characteristics over a voltage range  |
| **Transient**          | `.tran`     | Analyzes time-domain responses and waveforms       |
| **AC**                 | `.ac`       | Determines small-signal frequency response         |
| **Noise**              | `.noise`    | Characterizes device noise behavior                |

---

### SPICE Netlist Syntax

#### Component Declaration

**MOSFET:**

```spice
M<n> <drain> <gate> <source> <bulk> <model> W=<width> L=<length>
```

**Resistor:**

```spice
R<n> <node1> <node2> <resistance>
```

**Voltage Source:**

```spice
V<n> <positive_node> <negative_node> <DC_value>
```

<p align="center">
  <img src="Images/example_for_spice_netlist_syntax.png" alt="SPICE Netlist Syntax Example" width="500"/>
  <br>
  <em>Figure 13: Example of SPICE Netlist Syntax</em>
</p>

---

### Example: NMOS Characterization Circuit

<p align="center">
  <img src="Images/nmos_inverter.png" alt="NMOS Test Circuit" width="500"/>
  <br>
  <em>Figure 14: NMOS Characterization Test Circuit</em>
</p>

```spice
* Model Description
.param temp=27
.lib "sky130_fd_pr/models/sky130.lib.spice" tt

* NMOS Characterization Circuit
XM1 Vdd n1 0 0 sky130_fd_pr__nfet_01v8 w=5 l=2
R1 n1 in 55
Vdd vdd 0 1.8V
Vin in 0 1.8V

* Simulation Commands
.op
.dc Vdd 0 1.8 0.1 Vin 0 1.8 0.2

.control
run
display
setplot dc1
.endc
.end
```

---

### Netlist Breakdown

| **Line**            | **Component**   | **Description**                          |
| ------------------- | --------------- | ---------------------------------------- |
| `M1 vdd n1 0 0 ...` | NMOS transistor | Drain=vdd, Gate=n1, Source=GND, Bulk=GND |
| `R1 in n1 55`       | Gate resistor   | 55 Ω resistor limits input current       |
| `Vdd vdd 0 1.8`     | Power source    | DC supply voltage                        |
| `Vin in 0 1.8`      | Input voltage   | Controls gate bias                       |
| `.dc Vdd 0 1.8 0.1` | DC sweep        | Sweeps V_DS with 0.1V steps              |

---

Would you like me to format the **SPICE command explanation table** (`ngspice day1_nfet_idvds_L2_W5.spice`, `plot -vdd#branch`) in the same professional GitHub style to follow this section?


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

