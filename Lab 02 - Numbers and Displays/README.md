# <p align="center">Numbers and Displays</p>

The objective of this lab session was to design and implement two fundamental combinational circuits using VHDL on an Intel FPGA DE-series board. The first goal was to develop a circuit that converts binary inputs from switches into decimal digits displayed on two 7-segment displays. The second objective was to create a ripple-carry adder by instantiating multiple full adder modules, enabling the addition of two four-bit binary numbers. Through this lab, the aim was to apply VHDL for deriving Boolean expressions, designing arithmetic circuits, and testing functionality on FPGA hardware.  

---
In digital design, understanding the fundamental components and structures of hardware description languages is essential for creating efficient and reliable systems. This lab explores the key concepts of VHDL, including components, signals, and the hierarchical organization of designs, which together form the foundation for building complex digital circuits. First, it is important to know that VHDL allows you to reference components before they are actually defined. This is because VHDL is a hardware description language, and it compiles based on the logical structure of the design rather than the specific order of lines in the file.

A `component` in VHDL refers to a module or a reusable building block that can be instantiated multiple times within a design. It serves as a sub-circuit or function that performs a specific task. Components are typically defined separately and then instantiated inside other modules or architectures. A `signal` in VHDL acts like a wire or connection within a circuit, holding values that can be transferred between components or used internally. These values, similar to variables in programming, represent the data at different points in time during the circuit's operation. Signals enable communication between different parts of the circuit, mimicking how physical wires connect hardware components.

The `entity` section in VHDL defines the module's inputs, outputs, and ports. The inputs represent the signals or data coming into the module, while the outputs represent the data that the module sends out. Ports define the names and types of these inputs and outputs. The entity effectively establishes the interface through which the module interacts with the rest of the design, specifying what external data is available for processing and what the module produces. The `architecture` section describes the internal logic and behavior that connects the inputs to the outputs. It defines how the inputs are processed, the logic that implements the desired functionality, and how the outputs are generated. This section can include declarations of components (other entities) to build more complex designs, as well as internal signals to facilitate the flow of data within the circuit. The architecture ultimately outlines how the circuit behaves and how its components work together to achieve the desired outcome.

The *top-level design* serves as the primary module that orchestrates the overall functionality and interconnections of various submodules or components. It typically encapsulates the entire system's architecture, defining how different parts communicate and interact to achieve a specified goal. The top-level design is responsible for instantiating lower-level modules, such as functional blocks, state machines, or data paths, and it manages their connections, inputs, and outputs In contrast, other types of design refer to the individual components or modules that perform specific tasks within the system. These can include simple combinational logic circuits, sequential circuits like flip-flops and counters, or more complex entities like arithmetic logic units (ALUs) and memory elements. Each of these modules is designed to fulfill a particular function, and they are often reused across different top-level designs. While the top-level design provides a high-level view of the entire system's architecture, the lower-level designs focus on the implementation details of specific functionalities. This hierarchical approach enables designers to manage complexity more effectively, allowing for easier debugging, testing, and modifications as requirements evolve. Overall, the top-level design is crucial for integrating and managing the entire system, while the other types of design focus on the granular details of individual components.

## Part 1: Driving a 7-Segment Display as a Binary to Decimal Converter

The task at hand involves controlling two 7-segment displays (HEX0 and HEX1) to show numbers, with 8 switch input bits in total. The first 4 bits control HEX0, the most right display, while the next 4 bits control HEX1. The goal is to display numbers from 0 to 9 on these two displays. Each number must be represented by its corresponding 4-bit binary input. For example, to display the number 0 on a 7-segment display, the input must be "0000," and the display's segment code should be "00000010," which turns on the appropriate segments to form the digit 0. Similarly, the numbers 1 through 9 are represented by their respective 4-bit inputs and corresponding segment patterns on the 7-segment display. For inputs in the range of "1010" to "1111," which correspond to the hexadecimal values A to F, we treat these inputs as "don't care" conditions in this part of the task, as the displays are only meant to show numbers 0 to 9. This simplifies the design by ignoring these higher inputs for this specific portion, ensuring that only valid decimal numbers are displayed on HEX0 and HEX1.

Once this is set up for displaying numbers on HEX0 and HEX1, the second part of the task extends the functionality to display hexadecimal values, from 0 to F, using the first two 7-segment displays available on the board (HEX0 and HEX1). For this part, the 4-bit inputs will represent numbers from "0000" (0) to "1111" (F), with each display showing its respective character. For example, the input "1010" would light up the segments to display the letter "A" on one of the displays. By mapping each 4-bit input to the correct segment code for both decimal and hexadecimal values. Recall that we read the binary input bits on the switches from right to left but the output bits on the HEX are read from left to right.

<details>
  <summary>7-Segment Display Working From (0 to 9)</summary>
<br>

<details>
  <summary>VHDL Code Implementation on the FPGA Board</summary>
<br>

``` VHDL
-- The code studied/implemented in the lab
-- Modular design

LIBRARY ieee;                  -- This is the top level design (because im calling other entity)
USE ieee.std_logic_1164.all;

ENTITY part1 IS               -- Defind My Main entity

   PORT ( SW         : IN  STD_LOGIC_VECTOR(7 DOWNTO 0);        -- SW my input (8 bit)
          LEDR       : OUT STD_LOGIC_VECTOR(7 DOWNTO 0);        -- LEDR my output (to show my inputs)
          HEX1, HEX0 : OUT STD_LOGIC_VECTOR(0 TO 6));           -- HEX0 and HEX1 is my two 7-segment dispaly that i will use
			  
END part1;

ARCHITECTURE Structure OF part1 IS     -- Here if I'm using an outside entity I need to declare my inputs and outputs
				       -- under (COMPONENT). You can think about it as C++ function signature

   COMPONENT bcd7seg
	
      PORT ( B : IN  STD_LOGIC_VECTOR(3 DOWNTO 0);  -- This is the 4 bit input to be taken from the switches
             H : OUT STD_LOGIC_VECTOR(0 TO 6)); -- This is the 7 bit output displayed on the 7SD
				 
   END COMPONENT;
	
	
BEGIN

   LEDR <= SW;                             -- Just assign the LEDR to match the input (SW)
   
   digit0: bcd7seg PORT MAP (SW(3 DOWNTO 0), HEX0); -- digit0 is a varible name, here we calling the entity bcd7seg (whose architecture is defined below). We are passing the leftmost 4 bits of the switch as B (the input) and HEX0 as H (the output) which are the arguments required by the bcd7seg to operate.
   digit1: bcd7seg PORT MAP (SW(7 DOWNTO 4), HEX1);
	
END Structure;

-- Entity definition for the BCD to 7-segment display converter
LIBRARY ieee;                  
USE ieee.std_logic_1164.all;
ENTITY bcd7seg IS                             -- definding Entity (bcd7seg) which was used above

   PORT ( B : IN  STD_LOGIC_VECTOR(3 DOWNTO 0);    -- input B (binary) which have 4 bits
          H : OUT STD_LOGIC_VECTOR(0 TO 6));       -- output H (hex) which have 7 bit (So i can use it as the 7-Segment Display)
			 
END bcd7seg;

ARCHITECTURE Structure OF bcd7seg IS           -- Here im just assign my output depending on my output
BEGIN
	-- -- Output logic for the 7-segment display based on the BCD input
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
                  -- insted of writing it in you main code, you can just call it from here                    
```


``` VHDL
-- Separate code that achieves the same result using a different approach
-- Doing everything in one function without having to call any external functions

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

<p align="center">
  <img src="Photos/0.jpg" style="width: 49%; height: 300px;" title="0000 0000 = 00"/> <img src="Photos/1.jpg" style="width: 49%; height: 300px;" title="0000 0001 = 01" /> 
  <img src="Photos/2.jpg" style="width: 49%; height: 300px;" title="0000 0010 = 02"/>  <img src="Photos/3.jpg" style="width: 49%; height: 300px;" title="0000 0011 = 03" />
  <img src="Photos/4.jpg" style="width: 49%; height: 300px;" title="0000 0100 = 04"/> <img src="Photos/5.jpg" style="width: 49%; height: 300px;" title="0000 0101 = 05" />
<img src="Photos/6.jpg" style="width: 49%; height: 300px;" title="0000 0110 = 06"/>  <img src="Photos/7.jpg" style="width: 49%; height: 300px;" title="0000 0111 = 07" />
<img src="Photos/8.jpg" style="width: 49%; height: 300px;" title="0000 1000 = 08"/> <img src="Photos/10.jpg" style="width: 49%; height: 300px;" title="0000 1001 = 09" />
</p>
 We can observe that when we set our 8-bit binary value from 0 to 9, the corresponding numbers are displayed on the 7-segment display. The four rightmost switches control the first 7-segment display, while the remaining four switches control the second 7-segment display. In the above cases, we focus on driving HEX0 only.


<p align="center">
 <img src="Photos/0.jpg" style="width: 49%; height: 300px;" title="0000 0000 = 00" /> <img src="Photos/17.jpg" style="width: 49%; height: 300px;" title="0001 0000 = 10"/> 
 <img src="Photos/18.jpg" style="width: 49%; height: 300px;" title="0010 0000 = 20" /> <img src="Photos/19.jpg" style="width: 49%; height: 300px;" title="0011 0000 = 30"/>
  <img src="Photos/20.jpg" style="width: 49%; height: 300px;" title="0100 0000 = 40" /> <img src="Photos/21.jpg" style="width: 49%; height: 300px;" title="0101 0000 = 50"/>
  <img src="Photos/22.jpg" style="width: 49%; height: 300px;" title="0110 0000 = 60" /> <img src="Photos/23.jpg" style="width: 49%; height: 300px;" title="0111 0000 = 70"/>
  <img src="Photos/24.jpg" style="width: 49%; height: 300px;" title="1000 0000 = 80" /> <img src="Photos/25.jpg" style="width: 49%; height: 300px;" title="1001 0000 = 90" />
</p>
 In these test cases, we focus on driving HEX1 for values from 0 to 9.
 
 <p align="center">
  <img src="Photos/9.jpg" style="width: 49%; height: 300px;" title="0000 1111 = 08" />  <img src="Photos/32.jpg" style="width: 49%; height: 300px;" title="1000 1000 = 88" />
</p>

  Finally, here we show one "don't care" case where all of the first 4 bits are activated, which would be "F" but it displays "8" instead because it is not defined in our scope. In the second case, we display different combinations for HEX0 and HEX1 driven both together. For instance, if we input the binary value '1000 1000', the output displayed on the 7-segment displays should be '88'. This can be observed in the figure above.

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

In the results, we can clearly observe that for each binary input, we get the correct corresponding output on the 7-segment display. We also see the switch inputs mirrored on the above LEDRs as an indicator of the switch's activation. For example, when we enter the binary value '1000', the output is '0000000', which means the 7-segment display is correctly showing the number '8'. Similarly, other inputs such as '0001' will display '1', and so on. Note that the outputs on the HEX0, and HEX1 have 1 bit less than the input and that's because the 7-segment display has a max of 7 bits one for each index. This demonstrates that the system reliably converts the binary inputs into their respective numerical values on the 7-segment display, ensuring accurate visual feedback for each input combination.

In ModelSim, when observing variables that are composites of multiple waveforms, the simulator often displays a bit string of numbers instead of a traditional waveform. This occurs because the variable being observed is essentially an aggregation of several individual 1-bit waveforms, each representing a component of the composite variable. In the simulation interface, these composite variables appear as a dropdown list containing multiple sub-variables, each with its own distinct waveform. It's crucial to understand that seeing "0000000" in HEX1 at the beginning doesn't imply that the result is only relevant for the first input. Rather, this value remains constant throughout the simulation, and thus, it is not repeatedly copied for each input cycle. If the value were to change, you would observe updates for each new input. However, since it remains unchanged, it is displayed once at the start and extends continuously until the end of the waveform.

</details>
</details>

<details>
  <summary>7-Segment Display Working From (0 to F)</summary>
<br>

<details>
  <summary>VHDL Code Implementation on the FPGA Board</summary>
<br>

``` VHDL
-- The main approach studied in the lab (modular design)

LIBRARY ieee;                  
USE ieee.std_logic_1164.all;

ENTITY part1 IS  
   PORT ( SW         : IN  STD_LOGIC_VECTOR(7 DOWNTO 0);        
          LEDR       : OUT STD_LOGIC_VECTOR(7 DOWNTO 0);       
          HEX1, HEX0 : OUT STD_LOGIC_VECTOR(0 TO 6));           
			  
END part1;

ARCHITECTURE Structure OF part1 IS     
				       

   COMPONENT bcd7seg
	
      PORT ( B : IN  STD_LOGIC_VECTOR(3 DOWNTO 0);    
             H : OUT STD_LOGIC_VECTOR(0 TO 6));
				 
   END COMPONENT;
	
	
BEGIN

   LEDR <= SW;                             
   
   digit0: bcd7seg PORT MAP (SW(3 DOWNTO 0), HEX0); 
   digit1: bcd7seg PORT MAP (SW(7 DOWNTO 4), HEX1); 

END Structure;

-- Defining my Entity
LIBRARY ieee;  -- not repeated, this must be here because this entity cannot see the included libraries for the entity above                
USE ieee.std_logic_1164.all;
ENTITY bcd7seg IS                             

   PORT ( B : IN  STD_LOGIC_VECTOR(3 DOWNTO 0);    
          H : OUT STD_LOGIC_VECTOR(0 TO 6));       
			 
END bcd7seg;

ARCHITECTURE Structure OF bcd7seg IS           
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
	
	
END Structure;    
```


``` VHDL
-- Second approach

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

<p align="center">
  <img src="Photos/11.jpg" style="width: 49%; height: 300px;" title="0000 1010 = 0A" /> <img src="Photos/12.jpg" style="width: 49%; height: 300px;" title="0000 1011 = 0B"/>  
  <img src="Photos/13.jpg" style="width: 49%; height: 300px;" title="0000 1100 = 0C" /> <img src="Photos/14.jpg" style="width: 49%; height: 300px;" title="0000 1101 = 0D"/>
  <img src="Photos/15.jpg" style="width: 49%; height: 300px;" title="0000 1110 = 0E" /> <img src="Photos/16.jpg" style="width: 49%; height: 300px;" title="0000 1111 = 0F"/>  
</p>

  We can observe that when we set our 8-bit binary value from 0 to F, the corresponding numbers are displayed on the 7-segment display. In the above cases, we are focusing on driving HEX0 only.

  <p align="center">
  <img src="Photos/26.jpg" style="width: 49%; height: 300px;" title="1010 0000 = A0" /> <img src="Photos/27.jpg" style="width: 49%; height: 300px;" title="1011 0000 = B0"/>  
  <img src="Photos/28.jpg" style="width: 49%; height: 300px;" title="1100 0000 = C0" /> <img src="Photos/29.jpg" style="width: 49%; height: 300px;" title="1101 0000 = D0"/>
  <img src="Photos/30.jpg" style="width: 49%; height: 300px;" title="1110 0000 = E0" /> <img src="Photos/31.jpg" style="width: 49%; height: 300px;" title="1111 0000 = F0"/>
</p>

 The four rightmost switches control the first 7-segment display, while the remaining four switches control the second 7-segment display. In the above cases, we are focusing on driving HEX1 only. For instance, if we input the binary value '1111 0000', the output displayed on the 7-segment displays should be 'F0'. This can be observed above.

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

## Part 2: 4-bit Ripple-Carry Adder
In this part, we write a VHDL entity for the full adder subcircuit and a top-level VHDL entity that instantiates four instances of this full adder. We use switches SW7−4 and SW3−0 to represent the inputs A and B, while SW8 serves as the carry-in (Cin) of the adder. The outputs of the adder, consisting of the sum (S) and the carry-out (Cout), are connected to the red lights LEDR. A full adder takes three inputs: a, b, and Cin, and produces two outputs: the sum (S) and the carry-out (Cout). The sum is calculated by adding the three inputs: a, b, and Cin. This setup forms a ripple-carry adder, where the carry-out from each full adder is passed to the next full adder in sequence, creating the final multi-bit result.

A *full adder* is a fundamental digital circuit used in arithmetic operations, specifically in binary addition. It is designed to calculate the sum of three input bits: two significant bits, typically referred to as A and B, and a carry-in bit (Cin) from a previous addition stage. This makes it more advanced than a half adder, which only adds two bits without considering a carry input. The full adder outputs two values: the sum (S) and a carry-out (Cout). The sum represents the least significant bit (LSB) of the addition result, while the carry-out is the most significant bit (MSB), which is passed to the next stage in a chain of adders if performing multi-bit additions (such as in a ripple-carry adder). The logic behind a full adder is based on the binary arithmetic rule that if the sum of the three input bits exceeds 1, a carry must be generated. The full adder is essential in digital systems such as CPUs and ALUs (Arithmetic Logic Units), where it is used to build larger adders that can handle multi-bit numbers. For example, four full adders can be connected in a sequence to create a ripple-carry adder, which is capable of adding two four-bit binary numbers, with carries propagating through the circuit. This modular approach makes the full adder a cornerstone of digital arithmetic processing.

A *ripple-carry adder* is a digital circuit used to perform the addition of multi-bit binary numbers by cascading several full adders in sequence. Each full adder in the circuit is responsible for adding a pair of corresponding bits from two binary numbers, along with any carry from the previous stage of addition. The term "ripple-carry" refers to the way in which the carry-out from each full adder is passed as the carry-in to the next full adder in the sequence, like a ripple moving through the circuit. In a typical 4-bit ripple-carry adder, four full adders are connected. Each full adder adds a single pair of bits from two 4-bit binary numbers, along with a carry bit from the previous adder. The process begins with the least significant bit (LSB) and moves towards the most significant bit (MSB). The final result consists of a 4-bit sum and a final carry-out. The carry from one adder must propagate to the next, which creates a delay as each adder must wait for the carry from the previous stage before computing its output. This delay is known as the "propagation delay," and it is a limiting factor for the speed of the ripple-carry adder when handling large numbers. Despite its simplicity and modularity, the ripple-carry adder is not the most efficient for high-speed operations due to the time it takes for the carry to propagate through all the stages. However, it remains widely used in digital systems where simplicity and ease of design are prioritized, making it a fundamental building block for more complex arithmetic units in CPUs and other digital processing hardware.

<details>
<summary>VHDL Code Implementation on the FPGA Board</summary>
<br>
	
```VHDL
-- These are not separate codes solving the same problem. They are part of one solution:
-- The first part of the code defines the ripple-carry adder, which is made up of four full adders.
-- The second part of the code defines how each full adder behaves.

-- This code represents a hierarchical design in VHDL, with a top-level entity and architecture (part3) and lower-level components (fa, the full adder). 
-- The entity part3 serves as the top-level design. 
-- The full adder (fa) is a lower-level module (or component). 

-- First part of the code
-- declared the full adder as a component within the ripple-carry adder's architecture
LIBRARY ieee;
USE ieee.std_logic_1164.all;

ENTITY part3 IS -- entity declares the inputs and outputs of the module
   PORT ( SW   : IN  STD_LOGIC_VECTOR(8 DOWNTO 0); -- 9 switches used for input
          LEDR : OUT STD_LOGIC_VECTOR(9 DOWNTO 0)); -- 10 LEDRs used for output
END part3;

ARCHITECTURE Structure OF part3 IS -- architecture defines the internal workings of the module (what it actually does)
   COMPONENT fa -- fa is short for full adder, fa is used four times in the next section to create a ripple-carry adder (adding 4-bit numbers)
   -- Like saying I'm going to use a function called "fa" here. I'll define what it is later
		PORT ( a, b, ci : IN  STD_LOGIC; -- defining a, b, ci as inputs (ci is carry in)
                       s, co    : OUT STD_LOGIC); -- defining s, co as outputs (s is sum and co is carry out)
   END COMPONENT;
	
   SIGNAL A, B, S : STD_LOGIC_VECTOR(3 DOWNTO 0); -- a, b, s as variables each having 4 bits
   SIGNAL C       : STD_LOGIC_VECTOR(4 DOWNTO 0); -- reason C has 5 bits is because it holds the carry values, including the initial carry-in (SW(8)) and the final carry-out. 
	-- The first bit is the carry-in, and each subsequent bit is the carry-out from each adder, so 4 bits for carries from the adders + 1 extra bit for the initial carry-in makes it 5.
	
BEGIN
   A <= SW(7 DOWNTO 4); -- Input A is assigned to the upper 4 bits of the switches
   B <= SW(3 DOWNTO 0); -- Input B is assigned to the lower 4 bits of the switches 
   C(0) <= SW(8); -- the leftmost switch is used as the initial carry-in
	
	-- This creates 4 full adders that add the corresponding bits of A and B with the carry, passing the carry from one stage to the next
   bit0: fa PORT MAP (A(0), B(0), C(0), S(0), C(1)); -- NOTE that this line is like calling the function and passing those 5 arguments to it. Adds A(0), B(0), and C(0), producing S(0) and C(1). The C(1) will be used by the next line
   bit1: fa PORT MAP (A(1), B(1), C(1), S(1), C(2)); -- Adds A(1), B(1), and C(1), and so on
   bit2: fa PORT MAP (A(2), B(2), C(2), S(2), C(3));
   bit3: fa PORT MAP (A(3), B(3), C(3), S(3), C(4));
   
   -- Display the inputs
   LEDR(4 DOWNTO 0) <= (C(4) & S); -- This combines the last carry-out C(4) and the 4-bit sum S and displays them on the LEDs
   LEDR(9 DOWNTO 5) <= "00000"; -- Not being used
END Structure;

-- Second part of the code (This part defines the full adder that is used in the first block)
-- define how the full adder (fa) works (i.e., its entity and architecture). This definition provides the necessary details for the VHDL compiler to understand how the full adder operates.
-- Keep in mind that in VHDL, the order of the code doesn’t matter as long as the entity and architecture of a component (like this full adder) are declared somewhere in the file 
-- or are accessible from other files in the project. 

LIBRARY ieee;                  
USE ieee.std_logic_1164.all;
ENTITY fa IS -- This declares the inputs (a, b, ci) and outputs (s, co) for a single full adder
   PORT ( a, b, ci : IN  STD_LOGIC;
          s, co    : OUT STD_LOGIC);
END fa;

ARCHITECTURE Structure OF fa IS
   SIGNAL a_xor_b : STD_LOGIC;
BEGIN
   a_xor_b <= a XOR b; -- declares a signal named a_xor_b that will hold a single bit of type STD_LOGIC. This signal is used to store the result of the XOR operation between the two inputs, a and b.
   s <= a_xor_b XOR ci; -- The sum (s) is calculated as the XOR of a, b, and ci
   co <= (NOT(a_xor_b) AND b) OR (a_xor_b AND ci);
END Structure;
```

<p align="center">
 <img src="Photos/40.jpg" style="width: 49%; height: 300px;" title="A=0000 B=0101 Cin=0 (0 + 5 + 0 = 5)"/> <img src="Photos/41.jpg" style="width: 49%; height: 300px;" title="A=0010 B=0010 Cin=0 (2 + 2 + 0 = 4)" /> 
 <img src="Photos/43.jpg" style="width: 49%; height: 300px;" title="A=1000 B=0011 Cin=0 (8 + 3 + 0 = 11)"/>  <img src="Photos/44.jpg" style="width: 49%; height: 300px;" title="A=1010 B=1111 Cin=0 (10 + 15 + 0 = 25)" />
</p>

As shown in the results above, the setup is behaving as expected. For example, when `A = 0000 = 0`, `B = 0101 = 5`, and `Cin = 0` their sum was 5 as displayed on the rightmost bits of the LEDRs with 0101. Note that we haven't documented any cases with `Cin = 1`.

</details>


<details>
<summary>VHDL Testbench Code Simulation in ModelSim</summary>
<br>
	
```VHDL
-- A testbench is used to verify the functionality of a design by simulating it with various input values
library IEEE;
use IEEE.Std_logic_1164.all;
use IEEE.Numeric_Std.all;

entity part3_tb is -- defines the testbench entity part3_tb, which has no ports since it is used only for simulation
end;

architecture bench of part3_tb is

  component part3 -- part3 component is declared so that it can be instantiated within the testbench. It specifies the input (SW) and output (LEDR) ports.
     PORT ( SW   : IN  STD_LOGIC_VECTOR(8 DOWNTO 0);
            LEDR : OUT STD_LOGIC_VECTOR(9 DOWNTO 0));
  end component;

  -- Two signals are declared: SW for the input switches and LEDR for the output LEDs. These signals will connect the testbench to the part3 component.
  signal SW: STD_LOGIC_VECTOR(8 DOWNTO 0);
  signal LEDR: STD_LOGIC_VECTOR(9 DOWNTO 0);

begin

  uut: part3 port map ( SW   => SW, -- The part3 component is instantiated as uut (unit under test), mapping the testbench signals to the component ports.
                        LEDR => LEDR );

  stimulus: process
  begin
  
    -- This process generates test stimuli for the SW input signal. It assigns different binary values to SW at 100 ns intervals, 
	 -- allowing us to test how the part3 entity responds to each input. 

		SW<= "000000101"; -- A=0000 B=0101 Cin=0 (0 + 5 + 0)
		wait for 100ns;
		
		SW<= "000100010"; -- A=0010 B=0010 Cin=0 (2 + 2 + 0)
		wait for 100ns;
		
		SW<= "001000100"; -- A=0100 B=0100 Cin=0 (4 + 4 + 0)
		wait for 100ns;
		
		SW<= "010000011"; -- A=1000 B=0011 Cin=0 (8 + 3 + 0)
		wait for 100ns;
		
		SW<= "010101111"; -- A=1010 B=1111 Cin=0 (10 + 15 + 0)
		wait for 100ns;

    wait;
  end process;

end;
```

<p align="center">
  <img src="Photos/45.png" title="ModelSim Result" />
</p>

In the testbench, we simulated the same test cases we tried on the FPGA board to examine whether the outcomes would match and they did. For instance, in the second input to SW, we test 2 + 2 + 0 as 000100010 and the result on the LEDR was 000000100 which is the binary for 4!

</details>


## Conclusion
In conclusion, this lab session successfully demonstrated the design and implementation of fundamental combinational circuits using VHDL on an Intel FPGA DE-series board. By creating a binary-to-decimal converter for 7-segment displays and developing a ripple-carry adder through the instantiation of full adder modules, we effectively applied key VHDL concepts such as component instantiation, signal management, and hierarchical design. The project underscored the importance of understanding digital design principles and provided hands-on experience with translating logical operations into hardware descriptions. Overall, the lab enhanced our comprehension of VHDL and its application in constructing reliable digital systems, laying a solid foundation for future explorations in digital circuit design and implementation.

## Resources
|1| Ashenden, P. J. (2008). The designer’s guide to VHDL (3rd ed). Morgan Kaufmann Publishers.  
|2| Terasic. (2017). DE2-115 User Manual. <br> https://www.terasic.com.tw/attachment/archive/502/DE2_115_User_manual.pdf <br>
|3| VHDL Testbench Creation Using Perl. (n.d.). Retrieved September 21, 2024, from <br> https://www.doulos.com/knowhow/perl/vhdl-testbench-creation-using-perl/   
|4| ALL ABOUT ELECTRONICS. (2022, January 25). Ripple Carry Adder Explained | Working and Limitation of Ripple Carry Adder. <br> https://www.youtube.com/watch?v=b70ZQwci5sY  
|5| Full Adder in Digital Logic. (2017, March 21). GeeksforGeeks. <br>  https://www.geeksforgeeks.org/full-adder-in-digital-logic/

<br>

```mermaid
gantt
    title Work Division Gantt Chart
    tickInterval 1day
    todayMarker off
    axisFormat %a-%Y-%m-%d
    section Preparation         
        Nour Mostafa : active, 2024-09-17 00:00, 01h
        Mohamed Abouissa : 2024-09-17 00:00, 01h
    section Quartus         
        Nour Mostafa : active, 2024-09-18 00:00, 1d
        Mohamed Abouissa : 2024-09-18 00:00, 1d
    section ModelSim       
        Nour Mostafa : active,2024-09-18 00:00, 1d
        Mohamed Abouissa : 2024-09-18 00:00, 1d
    section Results       
        Nour Mostafa : active,2024-09-18 00:00, 1d
        Mohamed Abouissa : 2024-09-18 00:00, 1d
    section Report
        Nour Mostafa : active,2024-09-24 00:00, 1d
        Mohamed Abouissa : 2024-09-22 00:00, 1d
```

We extend our sincere appreciation to Eng. Umar Adeel for his insightful feedback which has significantly contributed to the successful completion of this experiment.

This publication adheres to all regulatory laws and guidelines established by the American University of Ras Al Khaimah (AURAK) regarding the dissemination of academic materials.
