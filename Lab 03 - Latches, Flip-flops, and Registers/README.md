# <p align="center">Latches, Flip-flops, and Registers</p>

abstract

---

introduction

## Part 1: Design and Implementation of an RS Latch

In the first part, we will create an RS latch, which is a basic memory element used in digital circuits to store a single bit of information. The RS latch operates with two main inputs: Set (S) and Reset (R), and produces two outputs: Q and its complement Q'. The function of the latch is straightforward: when the Set (S) input is activated, the output Q becomes 1, and Q' becomes 0, effectively setting the latch. Conversely, when the Reset (R) input is activated, the output Q becomes 0, and Q' becomes 1, resetting the latch. If both inputs are inactive (set to 0), the latch holds its current state, remembering the last set or reset condition. Refer to [figure](Photos/SRlatch.png) for a visual representation of the RS latch.

In this part, we will also explore how the RS latch behaves when both inputs are activated simultaneously, a situation that leads to an invalid state where the outputs Q and Q' are inconsistent. We will implement the RS latch using two cross-coupled NOR gates, which provide the necessary feedback loop to maintain the stored bit of data when both inputs are inactive. This simple latch design will form the foundation for understanding more advanced memory elements in digital systems. Refer to [figure](Photos/Part1Q.png)

<details>
  <summary>VHDL Code Implementation on the FPGA Board (RS Latch)</summary>
<br>

``` VHDL
                   
```

</details>

<details>
  <summary>VHDL Testbench Code Simulation in ModelSim</summary>
<br>

</details>

<details>
  <summary>VHDL Testbench Code Simulation in ModelSim</summary>
<br>

</details>
