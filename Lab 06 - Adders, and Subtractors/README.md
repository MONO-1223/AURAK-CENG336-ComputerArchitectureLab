# <p align="center">Adders and Subtractors</p>

// anchor

---

// anchor

## Part 1: 

In this section, we design an 8-bit accumulator, a digital circuit commonly used in computing and digital signal processing for sequential addition operations. An accumulator combines an arithmetic logic unit (ALU) function with storage capabilities, allowing it to retain and update a cumulative result of additions over time. Specifically, each cycle adds a new 8-bit value to the current sum stored in the accumulator. This design includes a carry output for the adder, which signals when the sum exceeds the maximum value representable by 8 bits, and an overflow output to indicate arithmetic overflow, helping manage conditions where the result exceeds the accumulator’s signed representation limits.

As shown in [Figure 1](Photos/Accumulator.png), the internal structure of the accumulator consists of an 8-bit adder, a register for holding the accumulated sum, and D flip-flops for storing the carry and overflow signals. The 8-bit input `A`, connected to switches `SW 0 to 7`, provides the value to be added in each cycle. The accumulated sum `S` is stored in a register and fed back into the adder, enabling continuous addition operations. Each clock cycle, triggered manually using `KEY1`, updates both the `register` and `D flip-flops`, ensuring that the carry and overflow flags reflect the current arithmetic status. An active-low asynchronous reset, controlled by `KEY0`, clears all values in the accumulator when needed.

The output of the accumulator is displayed using `LED` indicators and `7-segment displays` for easy monitoring. The sum from the adder is shown on the red lights `LEDR 0 to 7`, while the registered carry and overflow signals are displayed on `LEDR 8 and LEDR 9`, respectively. Additionally, the registered values of `A` and `S` are displayed as hexadecimal numbers on the `7-segment displays` `HEX 2 to 3` and `HEX 0 to 1`, providing a complete view of the current state of the accumulator.

<details>
  <summary>VHDL Code Implementation on the FPGA Board</summary>
<br>

```VHDL

```

<p align="center">
  <img src="Photos/part1.gif" style="width: 1000px" title="Testing all counting cases." />
</p>

// anchor

</details>


<details>
  <summary>Waveform Simulation</summary>
	
<br>

<p align="center">
  <img src="Photos/part1wave.png" title="Testing all counting cases." />
</p>

// anchor
<br>
	
</details>





## Part 2:

// anchor

<details>
<summary>VHDL Code Implementation on the FPGA Board</summary>
<br>

``` VHDL


```

<p align="center">
  <img src="Photos/part21.gif" style="width: 333px; height: 250px; object-fit: cover;" title="Allowing the timer to run freely up to 1 minute with manual timer pausing" />
  <img src="Photos/part22.gif" style="width: 333px; height: 250px; margin: 0 10px; object-fit: cover;" title="Setting a starting minute for the timer on HEX4 using SW[3 DOWNTO 0]" />
  <img src="Photos/part23.gif" style="width: 333px; height: 250px; object-fit: cover;" title="Setting a starting minute for the timer on both HEX5 and HEX4 using SW[7 DOWNTO 4] and SW[3 DOWNTO 0] respectively" />
</p>



// anchor
</details>

<details>
  <summary>Waveform Simulation</summary>
	<br>

<p align="center">
  <img src="Photos/part2wave.png" title="Testing different cases of button settings" />
</p>

// anchor

<br>


</details>

## Conclusion

// anchor

## Resources

|1| Ashenden, P. J. (2008). The designer’s guide to VHDL (3rd ed). Morgan Kaufmann Publishers.  

<br>

```mermaid
gantt
    title Work Division Gantt Chart
    tickInterval 1day
    todayMarker off
    axisFormat %a-%Y-%m-%d
    section Preparation         
        Nour Mostafa : active, 2024-10-29 00:00, 01h
        Mohamed Abouissa : 2024-10-29 00:00, 01h
    section Quartus         
        Nour Mostafa : active, 2024-10-30 00:00, 1d
        Mohamed Abouissa : 2024-10-30 00:00, 1d
    section ModelSim       
        Nour Mostafa : active,2024-10-30 00:00, 1d
        Mohamed Abouissa : 2024-10-30 00:00, 1d
    section Results       
        Nour Mostafa : active,2024-10-30 00:00, 1d
        Mohamed Abouissa : 2024-10-30 00:00, 1d
    section Report
        Nour Mostafa : active,2024-11-2 00:00, 2d
        Mohamed Abouissa : 2024-11-3 00:00, 2d
```

We extend our sincere appreciation to Eng. Umar Adeel for his insightful feedback which has significantly contributed to the successful completion of this experiment.

This publication adheres to all regulatory laws and guidelines established by the American University of Ras Al Khaimah (AURAK) regarding the dissemination of academic materials.




