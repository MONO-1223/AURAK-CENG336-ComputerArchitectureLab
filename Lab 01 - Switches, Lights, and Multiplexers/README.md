# <p align="center">Experiment 1</p>
## <p align="center">Switches, Lights, and Multiplexers</p>

![](Photos/AURAK-Banner.png)

The aim of this experiment is to explore how to interface basic input and output devices with an FPGA chip and implement a functional circuit utilizing these components. The switches on the DE-series boards will serve as inputs to the circuit while light-emitting diodes (LEDs) and 7-segment displays will be employed as output devices.



## Work Distribution & Acknowledgements
| | Name            | ID        | Task |
|-|-------------------|-----------|------|
|1| [Nour Mostafa](mailto:nour.mohamed@aurak.ac.ae)    | 2021004938 | 1. Conducted research on synchronization. <br> 2. Conducted research on deadlock. <br> 3. Prepared outstanding presentation slides. |
|2| [Mohamed Abouissa](mailto:mohamed.abouissa@aurak.ac.ae)  | 2021005188| 1. Documenting experiment and simulation results. <br> 2. Conducted research on memory management. <br> 3. Conducted research on file management. |

We extend our sincere appreciation to Eng. Umar Adeel for his insightful feedback which has significantly contributed to the successful completion of this experiment.

```mermaid
gantt
    title Work Distribution Gantt Chart
    dateFormat YYYY-MM-DD
    section Nour Mostafa
        Documenting experiment results : a1,2024-09-11, 1d
        Authoring Report          :after a1, 2d
    section Mohamed Abouissa
        Quartus Implementation :2024-09-11, 1d
        Documenting simulation results : 2024-09-11, 1d

```

## Table of Contents

1. [Introduction](#introduction)<br>
2. [Part 1: Daily Driver OS (Arch Linux)](#part-1-daily-driver-os-arch-linux)<br>
&nbsp;2.1. VHDL Code<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.1.1. Implementation Results<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.1.2. Discussion<br>
&nbsp;2.2. Testbench Code<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.2.1. Simulation Results<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.2.2. Discussion<br>
3. [Part 2: Cloud Computing OS](#part-2-cloud-computing-os)<br>
&nbsp;3.1. VHDL Code<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.1.1. Implementation Results<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.1.2. Discussion<br>
&nbsp;3.2. Testbench Code<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.2.1. Simulation Results<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.2.2. Discussion<br>
4. [Part 3: Additional Considerations (Best Practices in OS)](#part-3-additional-considerations-best-practices-in-os)<br>
&nbsp;4.1. VHDL Code<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.1.1. Implementation Results<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.1.2. Discussion<br>
&nbsp;4.2. Testbench Code<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.2.1. Simulation Results<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.2.2. Discussion<br>
5. [Part 4: Additional Considerations (Best Practices in OS)](#part-3-additional-considerations-best-practices-in-os)<br>
&nbsp;5.1. VHDL Code<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;5.1.1. Implementation Results<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;5.1.2. Discussion<br>
&nbsp;5.2. Testbench Code<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;5.2.1. Simulation Results<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;5.2.2. Discussion<br>
6. [Conclusion](#conclusion)<br>
7. [Resources](#resources)<br>

### Part 1
The DE2-115 provides eighteen switches and lights. The switches can be used to provide inputs, and the lights can be used as output devices. 

VHDL entity that uses ten switches and shows their
states on the LEDs. Since there are multiple switches and lights it is convenient to represent them as vectors in the
VHDL code



### Part 2

### Part 3

### Part 4
