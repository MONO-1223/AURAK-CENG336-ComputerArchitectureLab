# <p align="center">Number and Displays</p>

*abstract*

---
introduction

## Part 1: 7-Segment Displays

The task at hand involves controlling two 7-segment displays (HEX0 and HEX1) to show numbers, with 8 input bits in total. The first 4 bits control HEX0, the most right display, while the next 4 bits control HEX1. The goal is to display numbers from 0 to 9 on these two displays. Each number must be represented by its corresponding 4-bit binary input. For example, to display the number 0 on a 7-segment display, the input must be "0000," and the display's segment code should be "00000010," which turns on the appropriate segments to form the digit 0. Similarly, the numbers 1 through 9 are represented by their respective 4-bit inputs and corresponding segment patterns on the 7-segment display.

For inputs in the range of "1010" to "1111," which correspond to the hexadecimal values A to F, we treat these inputs as "don't care" conditions in this part of the task, as the displays are only meant to show numbers 0 to 9. This simplifies the design by ignoring these higher inputs for this specific portion, ensuring that only valid decimal numbers are displayed on HEX0 and HEX1.

Once this is set up for displaying numbers on HEX0 and HEX1, the second part of the task extends the functionality to display hexadecimal values, from 0 to F, using First two 7-segment displays available on the board (HEX0 and HEX1). For this part, the 4-bit inputs will represent numbers from "0000" (0) to "1111" (F), with each display showing its respective character. For example, the input "1010" would light up the segments to display the letter "A" on one of the displays. By mapping each 4-bit input to the correct segment code for both decimal and hexadecimal values.

<details>
  <summary> 7-Segment Display Working From (0 to 9)</summary>
<br>

<details>
  <summary>VHDL Code Implementation on the FPGA Board</summary>
<br>

``` VHDL
LIBRARY ieee;
USE ieee.std_logic_1164.all;

ENTITY part1 IS
   PORT ( SW : IN  STD_LOGIC_VECTOR(3 DOWNTO 0);
          HEX0,HEX1 : OUT STD_LOGIC_VECTOR(0 TO 6));
END part1;

ARCHITECTURE Structure OF part1 IS
BEGIN
	
	HEX0 <=  "0000001" when SW <= "0000" else
				"1001111" when SW <= "0001" else
				"0010010" when SW <= "0010" else
				"0000110" when SW <= "0011" else
				"1001100" when SW <= "0100" else
				"0100100" when SW <= "0101" else
				"0100000" when SW <= "0110" else
				"0001111" when SW <= "0111" else
				"0000000" when SW <= "1000" else
				"0000100" when SW <= "1001" else
				"0000000";
				
	HEX1 <=  "0000001" when SW <= "0000" else
				"1001111" when SW <= "0001" else
				"0010010" when SW <= "0010" else
				"0000110" when SW <= "0011" else
				"1001100" when SW <= "0100" else
				"0100100" when SW <= "0101" else
				"0100000" when SW <= "0110" else
				"0001111" when SW <= "0111" else
				"0000000" when SW <= "1000" else
				"0000100" when SW <= "1001" else
				"0000000";
	
	
END Structure;
```

``` VHDL



```

<p align="center">
  <img src="Photos/8.jpg" style="width: 49%; height: 300px;" title="" /> <img src="Photos/21.jpg" style="width: 49%; height: 300px;" title=""/>  
  <img src="Photos/32.jpg" style="width: 49%; height: 300px;" title="" /> <img src="Photos/19.jpg" style="width: 49%; height: 300px;" title=""/>
</p>



</details>
</details>


