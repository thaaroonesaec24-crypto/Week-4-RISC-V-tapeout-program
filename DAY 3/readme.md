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
