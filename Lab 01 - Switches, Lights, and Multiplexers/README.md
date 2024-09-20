# <p align="center">Experiment 1</p>
## <p align="center">Switches, Lights, and Multiplexers</p>

![](Photos/Logo.jpg)

The aim of this experiment is to explore how to interface basic input and output devices with an FPGA chip and implement a functional circuit utilizing these components. The switches on the DE-series boards will serve as inputs to the circuit while light-emitting diodes (LEDs) and 7-segment displays will be employed as output devices.


## Table of Contents

1. [Introduction](#introduction)<br>
2. [Part 1: ](#part-1-daily-driver-os-arch-linux)<br>
&nbsp;2.1. VHDL Code<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.1.1. Implementation Results<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.1.2. Discussion<br>
&nbsp;2.2. Testbench Code<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.2.1. Simulation Results<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.2.2. Discussion<br>
3. [Part 2: ](#part-2-cloud-computing-os)<br>
&nbsp;3.1. VHDL Code<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.1.1. Implementation Results<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.1.2. Discussion<br>
&nbsp;3.2. Testbench Code<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.2.1. Simulation Results<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.2.2. Discussion<br>
4. [Part 3: ](#part-3-additional-considerations-best-practices-in-os)<br>
&nbsp;4.1. VHDL Code<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.1.1. Implementation Results<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.1.2. Discussion<br>
&nbsp;4.2. Testbench Code<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.2.1. Simulation Results<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.2.2. Discussion<br>
5. [Part 4: ](#part-3-additional-considerations-best-practices-in-os)<br>
&nbsp;5.1. VHDL Code<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;5.1.1. Implementation Results<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;5.1.2. Discussion<br>
&nbsp;5.2. Testbench Code<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;5.2.1. Simulation Results<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;5.2.2. Discussion<br>
6. [Conclusion](#conclusion)<br>
7. [Work Division](#conclusion)<br>
8. [Resources](#resources)<br>

### Part 1
The DE2-115 provides eighteen switches and lights. The switches can be used to provide inputs, and the lights can be used as output devices. 

VHDL entity that uses ten switches and shows their
states on the LEDs. Since there are multiple switches and lights it is convenient to represent them as vectors in the
VHDL code



### Part 2

### Part 3

### Part 4

### Work Division
```mermaid
gantt
    title Gantt Chart
    tickInterval 1day
    todayMarker off
    axisFormat %a-%Y-%m-%d
    section Preparation         
        Nour Mostafa : crit, 2024-09-10, 2h
        Mohamed Abouissa : 2024-09-10, 2h
    section Quartus         
        Nour Mostafa : crit, href "mailto:nour.mohamed@aurak.ac.ae", 2024-09-11, 2d
        Mohamed Abouissa : href "mailto:mohamed.abouissa@aurak.ac.ae", 2024-09-11, 2d
    section ModelSim       
        Nour Mostafa : crit,2024-09-11, 2d
        Mohamed Abouissa : 2024-09-11, 3d
    section Results       
        Nour Mostafa : crit,2024-09-11, 2d
        Mohamed Abouissa : 2024-09-11, 3d
    section Practice 1         
        Nour Mostafa : crit,2024-09-12, 4h
        Mohamed Abouissa : 2024-09-12, 4h
    section Practice 2         
        Nour Mostafa : crit, 2024-09-19, 2h
        Mohamed Abouissa : 2024-09-19, 2h
    section Report
        Nour Mostafa : crit,2024-09-18, 3d
        Mohamed Abouissa : 2024-09-19, 1d
```

We extend our sincere appreciation to Eng. Umar Adeel for his insightful feedback which has significantly contributed to the successful completion of this experiment.