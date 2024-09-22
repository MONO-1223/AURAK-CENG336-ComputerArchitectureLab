# <p align="center">Number and Displays</p>

*abstract*

---
The top-level design serves as the primary module that orchestrates the overall functionality and interconnections of various submodules or components. It typically encapsulates the entire system's architecture, defining how different parts communicate and interact to achieve a specified goal. The top-level design is responsible for instantiating lower-level modules, such as functional blocks, state machines, or data paths, and it manages their connections, inputs, and outputs In contrast, other types of design refer to the individual components or modules that perform specific tasks within the system. These can include simple combinational logic circuits, sequential circuits like flip-flops and counters, or more complex entities like arithmetic logic units (ALUs) and memory elements. Each of these modules is designed to fulfill a particular function, and they are often reused across different top-level designs.

While the top-level design provides a high-level view of the entire system's architecture, the lower-level designs focus on the implementation details of specific functionalities. This hierarchical approach enables designers to manage complexity more effectively, allowing for easier debugging, testing, and modifications as requirements evolve. Overall, the top-level design is crucial for integrating and managing the entire system, while the other types of design focus on the granular details of individual components.

## Part 1: 7-Segment Displays

The task at hand involves controlling two 7-segment displays (HEX0 and HEX1) to show numbers, with 8 input bits in total. The first 4 bits control HEX0, the most right display, while the next 4 bits control HEX1. The goal is to display numbers from 0 to 9 on these two displays. Each number must be represented by its corresponding 4-bit binary input. For example, to display the number 0 on a 7-segment display, the input must be "0000," and the display's segment code should be "00000010," which turns on the appropriate segments to form the digit 0. Similarly, the numbers 1 through 9 are represented by their respective 4-bit inputs and corresponding segment patterns on the 7-segment display.

For inputs in the range of "1010" to "1111," which correspond to the hexadecimal values A to F, we treat these inputs as "don't care" conditions in this part of the task, as the displays are only meant to show numbers 0 to 9. This simplifies the design by ignoring these higher inputs for this specific portion, ensuring that only valid decimal numbers are displayed on HEX0 and HEX1.

Once this is set up for displaying numbers on HEX0 and HEX1, the second part of the task extends the functionality to display hexadecimal values, from 0 to F, using First two 7-segment displays available on the board (HEX0 and HEX1). For this part, the 4-bit inputs will represent numbers from "0000" (0) to "1111" (F), with each display showing its respective character. For example, the input "1010" would light up the segments to display the letter "A" on one of the displays. By mapping each 4-bit input to the correct segment code for both decimal and hexadecimal values.

<details>
  <summary>7-Segment Display Working From (0 to 9)</summary>
<br>

<details>
  <summary>VHDL Code Implementation on the FPGA Board</summary>
<br>

``` VHDL
-- Easy Code (Not the main one)

LIBRARY ieee;
USE ieee.std_logic_1164.all;

ENTITY part1 IS
   PORT ( SW : IN  STD_LOGIC_VECTOR(3 DOWNTO 0);       -- My input (SW) have 4 bit as input
          HEX0,HEX1 : OUT STD_LOGIC_VECTOR(0 TO 6));   -- My Output (HEX0 and HEX1) having 7 bit as output
END part1;

ARCHITECTURE Structure OF part1 IS
BEGIN
	
	HEX0 <=  "0000001" when SW <= "0000" else                   -- Write every case for HEX0 depending on the input (SW)
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
				
	HEX1 <=  "0000001" when SW <= "0000" else                   -- Write every case for HEX1 depending on the input (SW)
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
-- (My main)

LIBRARY ieee;                  -- This is the top level design (because im calling other entity)
USE ieee.std_logic_1164.all;

ENTITY part1 IS               -- Defind My Main entity

   PORT ( SW         : IN  STD_LOGIC_VECTOR(7 DOWNTO 0);        -- SW my input (8 bit)
          LEDR       : OUT STD_LOGIC_VECTOR(7 DOWNTO 0);        -- LEDR my output (to show my inputs)
          HEX1, HEX0 : OUT STD_LOGIC_VECTOR(0 TO 6));           -- HEX0 and HEX1 is my two 7-segment dispaly that i will use
			  
END part1;

ARCHITECTURE Structure OF part1 IS     -- Here if im using an outside entity i need to declare my inputs and outputs
				       -- under (COMPONENT) you can think about it as C++ function signature

   COMPONENT bcd7seg
	
      PORT ( B : IN  STD_LOGIC_VECTOR(3 DOWNTO 0);    -- Same as above
             H : OUT STD_LOGIC_VECTOR(0 TO 6));
				 
   END COMPONENT;
	
	
BEGIN

   LEDR <= SW;                             -- Just assign the LEDR to match the input (SW)
   
   digit0: bcd7seg PORT MAP (SW(3 DOWNTO 0), HEX0); -- digit0 it just a varible name, here we calling the entity (bcd7seg) and 
   digit1: bcd7seg PORT MAP (SW(7 DOWNTO 4), HEX1); -- map it, the maping is like the first thing which is the SW it will go to my

-- entity as B because in the entity my B is the input so we pass the first four bit to entity
-- Then my Entity take the input and do the steps then save the
-- output on H then the H is maped with the HEX0 the why we need to becareful when we write this
-- line becaues we need to follow the same order as we write my entity, so if i satrt with input, here also start with input etc.
	
END Structure;

-- (defining my Entity)


LIBRARY ieee;
USE ieee.std_logic_1164.all;

ENTITY bcd7seg IS                             -- Here im definding my Entity (bcd7seg) Which i will use it on the main code later

   PORT ( B : IN  STD_LOGIC_VECTOR(3 DOWNTO 0);    -- It have input B which have 4 bits
          H : OUT STD_LOGIC_VECTOR(0 TO 6));       -- It have output H which have 7 bit (So i can use it as the 7-Segment Display)
			 
END bcd7seg;

ARCHITECTURE Structure OF bcd7seg IS           -- Here im just assign my output depending on my output
BEGIN
	
	H <=  "0000001" when B <= "0000" else
			"1001111" when B <= "0001" else
			"0010010" when B <= "0010" else
			"0000110" when B <= "0011" else
			"1001100" when B <= "0100" else
			"0100100" when B <= "0101" else
			"0100000" when B <= "0110" else
			"0001111" when B <= "0111" else
			"0000000" when B <= "1000" else
			"0000000";
	
	
END Structure;    -- Its just like a function that calculate something for you
                  -- insted of write it in you main code so you can just call it
                            
```

<p align="center">
  <img src="Photos/8.jpg" style="width: 49%; height: 300px;" title="0000 1000 = 08" /> <img src="Photos/21.jpg" style="width: 49%; height: 300px;" title="0101 0000 = 50"/>  
  <img src="Photos/32.jpg" style="width: 49%; height: 300px;" title="1000 1000 = 88" /> <img src="Photos/19.jpg" style="width: 49%; height: 300px;" title="0011 0000 = 30"/>
</p>

We can observe that when we set our 8-bit binary value from 0 to 9, the corresponding numbers are displayed on the 7-segment display. The four rightmost switches control the first 7-segment display, while the remaining four switches control the second 7-segment display. For instance, if we input the binary value '1000 1000', the output displayed on the 7-segment displays should be '88'. This can be observed in the figure above.

</details>

<details>
  <summary>VHDL Testbench Code Simulation in ModelSim</summary>
<br>

```VHDL
library IEEE;
use IEEE.Std_logic_1164.all;
use IEEE.Numeric_Std.all;

entity part1_tb is
end;

architecture bench of part1_tb is

  component part1 
  
     PORT ( SW         : IN  STD_LOGIC_VECTOR(7 DOWNTO 0);
            LEDR       : OUT STD_LOGIC_VECTOR(7 DOWNTO 0);
            HEX1, HEX0 : OUT STD_LOGIC_VECTOR(0 TO 6));
				
  end component;

  signal SW: STD_LOGIC_VECTOR(7 DOWNTO 0);
  signal LEDR: STD_LOGIC_VECTOR(7 DOWNTO 0);
  signal HEX1, HEX0: STD_LOGIC_VECTOR(0 TO 6);

begin

  uut: part1 port map ( SW   => SW,
                        LEDR => LEDR,
                        HEX1 => HEX1,
                        HEX0 => HEX0 );

  stimulus: process
  begin
   
    -- Put initialisation code here
	 
	 SW<= "00000000";
	 wait for 100ns;
	 
	 SW<= "00000001";
	 wait for 100ns;
	 
	 SW<= "00000010";
	 wait for 100ns;
	 
	 SW<= "00000011";
	 wait for 100ns;
	 
	 SW<= "00000100";
	 wait for 100ns;
	 
	 SW<= "00000101";
	 wait for 100ns;
	 
	 SW<= "00000110";
	 wait for 100ns;
	 
	 SW<= "00000111";
	 wait for 100ns;
	 
	 SW<= "00001000";
	 wait for 100ns;
	 
	 SW<= "00001001";
	 wait for 100ns;

    -- Put test bench stimulus code here

    wait;
  end process;


end;

```

<p align="center">
  <img src="Photos/33.png" title="ModelSim Result" />
</p>

In the results, we can clearly observe that for each binary input, we get the correct corresponding output on the 7-segment display. For example, when we enter the binary value '1000', the output is '0000000', which means the 7-segment display is correctly showing the number '8'. Similarly, other inputs such as '0001' will display '1', and so on. This demonstrates that the system reliably converts the binary inputs into their respective numerical values on the 7-segment display, ensuring accurate visual feedback for each input combination.

</details>
</details>

<details>
  <summary>7-Segment Display Working From (0 to F)</summary>
<br>

<details>
  <summary>VHDL Code Implementation on the FPGA Board</summary>
<br>

``` VHDL
-- Easy Code (Not the main one)

LIBRARY ieee;
USE ieee.std_logic_1164.all;

ENTITY part1 IS
   PORT ( SW : IN  STD_LOGIC_VECTOR(3 DOWNTO 0);       -- My input (SW) have 4 bit as input
          HEX0,HEX1 : OUT STD_LOGIC_VECTOR(0 TO 6));   -- My Output (HEX0 and HEX1) having 7 bit as output
END part1;

ARCHITECTURE Structure OF part1 IS
BEGIN
	
	HEX0 <=  "0000001" when SW <= "0000" else                   -- Write every case for HEX0 depending on the input (SW)
				"1001111" when SW <= "0001" else
				"0010010" when SW <= "0010" else
				"0000110" when SW <= "0011" else
				"1001100" when SW <= "0100" else
				"0100100" when SW <= "0101" else
				"0100000" when SW <= "0110" else
				"0001111" when SW <= "0111" else
				"0000000" when SW <= "1000" else
				"0000100" when SW <= "1001" else
				"0001000" when SW <= "1010" else
				"0000000" when SW <= "1011" else
				"0110001" when SW <= "1100" else
				"0000001" when SW <= "1101" else
				"0110000" when SW <= "1110" else
				"0111000" when SW <= "1111" else
				"0000000";
				
	HEX1 <=  "0000001" when SW <= "0000" else                   -- Write every case for HEX1 depending on the input (SW)
				"1001111" when SW <= "0001" else
				"0010010" when SW <= "0010" else
				"0000110" when SW <= "0011" else
				"1001100" when SW <= "0100" else
				"0100100" when SW <= "0101" else
				"0100000" when SW <= "0110" else
				"0001111" when SW <= "0111" else
				"0000000" when SW <= "1000" else
				"0000100" when SW <= "1001" else
				"0001000" when SW <= "1010" else
				"0000000" when SW <= "1011" else
				"0110001" when SW <= "1100" else
				"0000001" when SW <= "1101" else
				"0110000" when SW <= "1110" else
				"0111000" when SW <= "1111" else
				"0000000";
	
	
END Structure;
```

``` VHDL
-- (definding my Entity)

LIBRARY ieee;
USE ieee.std_logic_1164.all;

ENTITY bcd7seg IS                             -- Here im definding my Entity (bcd7seg) Which i will use it on the main code later

   PORT ( B : IN  STD_LOGIC_VECTOR(3 DOWNTO 0);    -- It have input B which have 4 bits
          H : OUT STD_LOGIC_VECTOR(0 TO 6));       -- It have output H which have 7 bit (So i can use it as the 7-Segment Display)
			 
END bcd7seg;

ARCHITECTURE Structure OF bcd7seg IS           -- Here im just assign my output depending on my output
BEGIN
	
	H <=  "0000001" when B <= "0000" else
			"1001111" when B <= "0001" else
			"0010010" when B <= "0010" else
			"0000110" when B <= "0011" else
			"1001100" when B <= "0100" else
			"0100100" when B <= "0101" else
			"0100000" when B <= "0110" else
			"0001111" when B <= "0111" else
			"0000000" when B <= "1000" else
			"0000100" when B <= "1001" else
			"0001000" when B <= "1010" else
			"0000000" when B <= "1011" else
			"0110001" when B <= "1100" else
			"0000001" when B <= "1101" else
			"0110000" when B <= "1110" else
			"0111000" when B <= "1111" else
			"0000000";
	
	
END Structure;    -- Its just like a function that calculate something for you
                  -- insted of write it in you main code so you can just call it
                            

```

``` VHDL
-- (My main)

LIBRARY ieee;                  -- This is the top level design (because im calling other entity)
USE ieee.std_logic_1164.all;

ENTITY part1 IS               -- Defind My Main entity

   PORT ( SW         : IN  STD_LOGIC_VECTOR(7 DOWNTO 0);        -- SW my input (8 bit)
          LEDR       : OUT STD_LOGIC_VECTOR(7 DOWNTO 0);        -- LEDR my output (to show my inputs)
          HEX1, HEX0 : OUT STD_LOGIC_VECTOR(0 TO 6));           -- HEX0 and HEX1 is my two 7-segment dispaly that i will use
			  
END part1;

ARCHITECTURE Structure OF part1 IS     -- Here if im using an outside entity i need to declare my inputs and outputs
				       -- under (COMPONENT) you can think about it as C++ function signature

   COMPONENT bcd7seg
	
      PORT ( B : IN  STD_LOGIC_VECTOR(3 DOWNTO 0);    -- Same as above
             H : OUT STD_LOGIC_VECTOR(0 TO 6));
				 
   END COMPONENT;
	
	
BEGIN

   LEDR <= SW;                             -- Just assign the LEDR to match the input (SW)
   
   digit0: bcd7seg PORT MAP (SW(3 DOWNTO 0), HEX0); -- digit0 it just a varible name, here we calling the entity (bcd7seg) and 
   digit1: bcd7seg PORT MAP (SW(7 DOWNTO 4), HEX1); -- map it, the maping is like the first thing which is the SW it will go to my

-- entity as B because in the entity my B is the input so we pass the first four bit to entity
-- Then my Entity take the input and do the steps then save the
-- output on H then the H is maped with the HEX0 the why we need to becareful when we write this
-- line becaues we need to follow the same order as we write my entity, so if i satrt with input, here also start with input etc.
	
END Structure;
```
<p align="center">
  <img src="Photos/11.jpg" style="width: 49%; height: 300px;" title="0000 1010 = 0A" /> <img src="Photos/13.jpg" style="width: 49%; height: 300px;" title="0000 1100 = 0C"/>  
  <img src="Photos/30.jpg" style="width: 49%; height: 300px;" title="1110 0000 = E0" /> <img src="Photos/19.jpg" style="width: 49%; height: 300px;" title="1111 0000 = F0"/>
</p>

We can observe that when we set our 8-bit binary value from 0 to F, the corresponding numbers are displayed on the 7-segment display. The four rightmost switches control the first 7-segment display, while the remaining four switches control the second 7-segment display. For instance, if we input the binary value '1111 0000', the output displayed on the 7-segment displays should be 'F0'. This can be observed in the figure above.

</details>

<details>
  <summary>VHDL Testbench Code Simulation in ModelSim</summary>
<br>

```VHDL
library IEEE;
use IEEE.Std_logic_1164.all;
use IEEE.Numeric_Std.all;

entity part1_tb is
end;

architecture bench of part1_tb is

  component part1 
  
     PORT ( SW         : IN  STD_LOGIC_VECTOR(7 DOWNTO 0);
            LEDR       : OUT STD_LOGIC_VECTOR(7 DOWNTO 0);
            HEX1, HEX0 : OUT STD_LOGIC_VECTOR(0 TO 6));
				
  end component;

  signal SW: STD_LOGIC_VECTOR(7 DOWNTO 0);
  signal LEDR: STD_LOGIC_VECTOR(7 DOWNTO 0);
  signal HEX1, HEX0: STD_LOGIC_VECTOR(0 TO 6);

begin

  uut: part1 port map ( SW   => SW,
                        LEDR => LEDR,
                        HEX1 => HEX1,
                        HEX0 => HEX0 );

  stimulus: process
  begin
   
    -- Put initialisation code here
	 
	 SW<= "00000000";
	 wait for 100ns;
	 
	 SW<= "00000001";
	 wait for 100ns;
	 
	 SW<= "00000010";
	 wait for 100ns;
	 
	 SW<= "00000011";
	 wait for 100ns;
	 
	 SW<= "00000100";
	 wait for 100ns;
	 
	 SW<= "00000101";
	 wait for 100ns;
	 
	 SW<= "00000110";
	 wait for 100ns;
	 
	 SW<= "00000111";
	 wait for 100ns;
	 
	 SW<= "00001000";
	 wait for 100ns;
	 
	 SW<= "00001001";
	 wait for 100ns;

	SW<= "00001010";
	 wait for 100ns;

	SW<= "00001011";
	 wait for 100ns;

	SW<= "00001100";
	 wait for 100ns;

	SW<= "00001101";
	 wait for 100ns;

	SW<= "00001110";
	 wait for 100ns;

	SW<= "00001111";
	 wait for 100ns;

    -- Put test bench stimulus code here

    wait;
  end process;


end;

```

<p align="center">
  <img src="Photos/33.png" title="ModelSim Result" />
</p>

In the results, we can clearly observe that for each binary input, we get the correct corresponding output on the 7-segment display. For example, when we enter the binary value '1110', the output is '0110000', which means the 7-segment display is correctly showing the number 'E'. Similarly, other inputs such as '1111' will display 'F', and so on. This demonstrates that the system reliably converts the binary inputs into their respective numerical values on the 7-segment display, ensuring accurate visual feedback for each input combination.

</details>
</details>


















