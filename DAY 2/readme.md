##  Lab with sky130 models
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
