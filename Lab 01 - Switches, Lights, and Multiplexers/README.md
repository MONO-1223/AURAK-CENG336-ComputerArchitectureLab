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
3. [Part 1: Daily Driver OS (Arch Linux)](#part-1-daily-driver-os-arch-linux)<br>
&nbsp;3.1. [Processes and Threads](#processes-and-threads)<br>
&nbsp;3.2. [Process Scheduling](#process-scheduling)<br>
&nbsp;3.3. [Synchronization](#synchronization)<br>
&nbsp;3.4. [Deadlock](#deadlock)<br>
&nbsp;3.5. [Memory Management](#memory-management)<br>
&nbsp;3.6. [File Management](#file-management)<br>
4. [Part 2: Cloud Computing OS](#part-2-cloud-computing-os)<br>
&nbsp;4.1. [Trends in Operating Systems](#trends-in-operating-systems)<br>
&nbsp;4.2. [What Constitutes a Cloud Computing OS?](#what-constitutes-a-cloud-computing-os)<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.2.1. [Arch Linux as a Cloud OS](#arch-linux-as-a-cloud-os)<br>
&nbsp;4.3. [Comparison with OS of Part 1](#comparison-with-os-of-part-1)<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.3.1. [Implementation-wise](#implementation-wise)<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.3.2. [Performance-wise](#performance-wise)<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.3.3. [Hardware Support](#hardware-support)<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.3.4. [Community and Support](#community-and-support)<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.3.5. [Updates and Stability](#updates-and-stability)<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.3.6. [Licensing and Cost](#licensing-and-cost)<br>
5. [Part 3: Additional Considerations (Best Practices in OS)](#part-3-additional-considerations-best-practices-in-os)<br>
&nbsp;5.1. [Legal and Ethical Issues](#legal-and-ethical-issues)<br>
&nbsp;5.2. [Solutions](#solutions)<br>
6. [Conclusion](#conclusion)<br>
&nbsp;6.1. [Overview](#overview)<br>
&nbsp;6.2. [Linux vs. Our Current Daily Drivers](#linux-vs-our-current-daily-drivers)<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;6.2.1. [Linux vs. Windows](#linux-vs-windows)<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;6.2.2. [Linux vs. macOS](#linux-vs-macos)<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;6.2.3. [Takeaways](#takeaways)<br>
&nbsp;6.3. [Further Research](#further-research)<br>
&nbsp;6.4. [Building Our Own Distro!](#building-our-own-distro)<br>
&nbsp;6.5. [Getting Started with Linux: Essential Tools for Navigating the Command Line](#getting-started-with-linux-essential-tools-for-navigating-the-command-line)<br>
8. [Resources](#resources)<br>

### Part 1
The DE2-115 provides eighteen switches and lights. The switches can be used to provide inputs, and the lights can be used as output devices. 

VHDL entity that uses ten switches and shows their
states on the LEDs. Since there are multiple switches and lights it is convenient to represent them as vectors in the
VHDL code



### Part 2

### Part 3

### Part 4
