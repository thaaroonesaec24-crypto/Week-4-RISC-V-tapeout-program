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
