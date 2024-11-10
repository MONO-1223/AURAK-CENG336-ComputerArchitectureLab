# <p align="center">Timers and Real-time Clock</p>

The objective of this lab was to design, simulate, and implement digital circuits on the DE2-115 Cyclone IV-E EP4CE115F29C7 Altera FPGA, specifically a modulo-k counter and a real-time clock. Additionally, we aim to study the use of clocks in timed circuits. This lab demonstrates foundational skills in digital circuit design, parameterized counting, and real-time display on FPGA hardware. 

---

The __modulo__ operation, commonly denoted as __mod__ or `%`, calculates the remainder of the division of one number by another. For example, in the expression 7 mod 3, the result is 1, because when 7 is divided by 3, it leaves a remainder of 1. Similarly, 10 mod 5 results in 0 since 10 is perfectly divisible by 5. 

A __modulo counter__ is a specialized digital counter that counts in a cyclic manner, resetting its count to zero after reaching a predefined maximum value, known as the __modulus__. This behavior is fundamental in various digital applications, where it is often necessary to cycle through a limited set of values. For instance, a modulo-10 counter counts from 0 to 9 before rolling over back to 0. The primary utility of such counters is to manage repetitive counting sequences, which can be crucial in applications like timers, clocks, and digital event counters. Modulo counters enable efficient resource usage, as they only require the bits necessary to represent the desired counting range. This compact design is particularly beneficial in digital circuits where space and power efficiency are critical. 


## Part 1: 8-bit Modulo-k Counter

The task involves creating a modulo-k counter by modifying the design of an 8-bit counter to incorporate an additional parameter (the value k, which defines the range of counting for the counter), allowing the counter to count from 0 to k−1. Specifically, the counter should be designed to roll over to 0 after reaching the maximum value of k−1. In this design, the pushbutton KEY0 will serve as an asynchronous reset, while KEY1 will function as a manual clock input. The current count value will be displayed on the red LEDR lights, and an additional output, named 'rollover,' will be indicated on one of the LEDR lights when the counter reaches k − 1. 

<details>
  <summary>VHDL Code Implementation on the FPGA Board</summary>
<br>

```VHDL
-- This code uses Register Transfer Level (RTL) modeling with behavioral modeling.
-- RTL is a design abstraction that represents data flow between registers on each clock cycle. This style is typical for describing synchronous circuits like counters.
-- The PROCESS block in the modulo_counter component describes behavior using sequential statements (IF and ELSIF) instead of individual gate-level components. The code defines how the counter should respond to the clock and reset signals and how it resets or increments. Behavioral modeling focuses on the desired operation (like counting) rather than specific hardware implementation details.

LIBRARY ieee;
USE ieee.std_logic_1164.all;

-- Define the entity for the main module, "part1," which serves as the top-level design.
ENTITY part1 IS
   PORT ( KEY  : IN  STD_LOGIC_VECTOR( 1 DOWNTO 0 ); -- KEY is a 2-bit input vector for clock and reset signals
          LEDR : OUT STD_LOGIC_VECTOR( 17 DOWNTO 0 ) ); -- LEDR is an 18-bit output vector to show the counter value and rollover signal
END ENTITY;

-- Begin architecture definition for "part1" with an RTL (Register Transfer Level) style.
ARCHITECTURE rtl OF part1 IS
   -- Declare a component named "modulo_counter" to be instantiated within "part1".
   -- This component will have parameters for bit width (n) and the modulo value (k).
   COMPONENT modulo_counter IS       -- Define the ports for "modulo_counter":
      GENERIC ( n : NATURAL := 4; k : INTEGER := 15 ); -- natural is a predefined type in VHDL representing non-negative integer values, including zero
     -- generic is used to define parameters for VHDL components or entities, allowing configurable values that can be set externally at the time of instantiation.
     -- This provides flexibility by letting you parameterize your design without modifying the core code. In other words, Generics allow us to customize the behavior or dimensions of a module, such as setting array sizes, frequencies, or thresholds, based on specific requirements.
      PORT ( clock    : IN  STD_LOGIC;
             reset_n  : IN  STD_LOGIC;
             Q        : OUT STD_LOGIC_VECTOR(n-1 DOWNTO 0);
             rollover : OUT STD_LOGIC );
      -- clock and reset_n are inputs, Q outputs the counter value, 
      -- and rollover is set high when the counter reaches its maximum value (k-1).
   END COMPONENT;
BEGIN
   -- Instantiate "modulo_counter" component as "my_counter".
   my_counter: modulo_counter
      -- Map the GENERIC parameters: set n to 8 (8-bit counter) and k to 20 (count from 0 to 19).
      GENERIC MAP ( n => 8, k => 20 )
      -- Map the ports of the component to the top-level entity ports.
      -- Connect KEY(1) to the clock input and KEY(0) to reset_n.
      -- Connect the 8-bit counter output Q to LEDR bits [7:0].
      -- The rollover signal connects to LEDR(17) to indicate the counter has reset.
      PORT MAP ( clock => KEY(1), 
		           reset_n => KEY(0), 
					  Q => LEDR(7 DOWNTO 0), 
                 rollover => LEDR(17) );
   LEDR (16 DOWNTO 8) <= "000000000"; -- Set the unused bits of LEDR (16 to 8) to '0' to prevent any floating values.
END rtl;

-----------------------------------------------------------------------------------------------------------------------
LIBRARY ieee;
USE ieee.std_logic_1164.all;
-- Use the std_logic_unsigned package to enable unsigned arithmetic operations on STD_LOGIC_VECTOR.
USE ieee.std_logic_unsigned.all;

-- Define the entity "modulo_counter," which performs counting with parameterized width and modulus.
ENTITY modulo_counter is
   -- Declare generic parameters for flexibility:
   -- "n" represents the bit width of the counter, defaulted to 4 bits.
   -- "k" is the maximum count value, defaulted to 15.
   GENERIC (n : NATURAL := 4; k : INTEGER := 15);
   -- Define ports for the counter:
   -- "clock" for counting, "reset_n" as an asynchronous active-low reset,
   -- "Q" outputs the current counter value, and "rollover" signals the reset condition.
   PORT ( clock    : IN  STD_LOGIC;
          reset_n  : IN  STD_LOGIC;
          Q        : OUT STD_LOGIC_VECTOR(n-1 DOWNTO 0);
          rollover : OUT STD_LOGIC );
END ENTITY;

-- Begin architecture "core" for "modulo_counter."
ARCHITECTURE core OF modulo_counter IS
   -- Declare "counter" as an internal signal to hold the current count value,
   -- with a bit width of "n" to match the counter size.
   SIGNAL counter : STD_LOGIC_VECTOR(n-1 DOWNTO 0);
BEGIN
   -- Define a process that triggers on changes to "clock" or "reset_n."
   PROCESS(clock, reset_n)
   BEGIN
      -- Check if "reset_n" is active low ('0'), indicating a reset is requested.
      IF (reset_n = '0') THEN
         -- If reset, set "counter" to all zeros to start counting from 0.
         counter <= (OTHERS => '0');
      -- Otherwise, check for a rising edge on "clock" to perform counting.
      ELSIF ((clock'event) AND (clock = '1')) THEN
         -- If the counter has reached "k-1," reset it to zero to implement modulo-k counting.
         IF (counter = k-1) THEN
            counter <= (OTHERS => '0');
         -- Otherwise, increment the counter by 1.
         ELSE
            counter <= counter + 1;
         END IF;
      END IF;
   END PROCESS;
   
   -- Output the current value of "counter" to "Q."
   Q <= counter;   
   -- Set "rollover" to '1' when "counter" equals "k-1" (indicating rollover).
   -- Set "rollover" to '0' for all other values.
   rollover <= '1' WHEN (counter = k-1) ELSE '0';
END core;
```

<p align="center">
  <img src="Photos/part1.gif" style="width: 1000px" title="Testing all counting cases." />
</p>

Tested above are different cases of the general n-bit counter. KEY 0 acts as a reset button whereas KEY 1 acts as a manual clock. Every time KEY 1 is clicked the counter increments by 1 as displayed on the red LEDs which act as a binary counter indicator. The counting starts from 0 all the way up to 19. Once reached, we can see the rollover sign on LEDR17 activated indicating that the counter has reached its maximum count value and then returning, or "rolling over," to zero on the next count. This behavior is typical in counters designed to cycle through a specific range of values in a repetitive pattern.


</details>


<details>
  <summary>Waveform Simulation</summary>
	
<br>

<p align="center">
  <img src="Photos/part1wave.png" title="Testing all counting cases." />
</p>

The generated waveform is in agreement with the FPGA implementation as well as the expected output of the code. We can observe that KEY 0 indeed is an active low button because as long as we kept it high during this wave, the counter worked normally; incrementing by 1 each clock. The wave of KEY 1 simulated a button press where you have to press and release and thus alternate between high and low. On the rising edge of the wave we can see the value displayed on the LEDRs incremented by 1 starting from 00000000 = 0 to 00010011 = 19 then back to 0 again.

<br>
	
</details>





## Part 2: Digital Stopwatch on 7-Segment Displays

In Part II of this lab exercise, the objective is to design and implement a digital real-time clock using a DE-series FPGA board, providing a dynamic display of time down to hundredths of a second. The time display is structured across `seven-segment displays`, where minutes are shown on `HEX5 and HEX4`, seconds on `HEX3 and HEX2`, and hundredths of a second on `HEX1 and HEX0`. The design leverages modular counters that count within specific ranges and reset accordingly, allowing the circuit to track time efficiently and accurately. Each counter stage representing hundredths, seconds, and minutes advances sequentially, with rollovers triggering the next stage’s increment. This design structure simplifies the clock's timing functionality and exemplifies the modular nature of digital timekeeping within FPGA environments.

The clock includes interactive features for added flexibility: the user can preset the minutes using switch inputs `(SW7–0)` in combination with the `KEY1` button, which allows starting from any defined minute count. Additionally, the clock is equipped with a pause feature, activated by pressing `KEY0`, that temporarily halts the time progression and resumes it upon release. This feature makes it possible to stop and restart the clock without losing the current time display. Through this hands-on implementation, we gain insight into the synchronization of timed circuits on FPGAs, as well as practical experience in utilizing counters and rollovers to build a scalable, real-time application.

<details>
<summary>VHDL Code Implementation on the FPGA Board</summary>
<br>

``` VHDL
LIBRARY ieee;
USE ieee.std_logic_1164.all;

ENTITY part2 is
   PORT ( CLOCK_50 : IN STD_LOGIC;
          SW       : IN STD_LOGIC_VECTOR(7 DOWNTO 0);
          KEY      : IN STD_LOGIC_VECTOR(1 DOWNTO 0);
          HEX5, HEX4, HEX3, HEX2, HEX1, HEX0 : OUT STD_LOGIC_VECTOR(0 to 6) );
END ENTITY;

ARCHITECTURE Behavior OF part2 IS
   COMPONENT modulo_counter_er IS
      GENERIC ( n : NATURAL  := 4; k : INTEGER := 15 );
		-- n is a generic parameter of type NATURAL, which is a non-negative integer. It specifies the bit-width of the counter.
		--  For instance, if n = 4, the counter has a 4-bit output, allowing it to count from 0 to 15 in binary.
		
		-- k is a generic parameter of type INTEGER. It defines the maximum count value or limit of the counter before it rolls over to zero.
		-- By setting k, the counter can be customized to roll over at different counts.
		-- For example, if k = 10, the counter will reset once it reaches 9,
      PORT ( clock, reset_n  : IN  STD_LOGIC;
             enable          : IN  STD_LOGIC;
             Q               : OUT STD_LOGIC_VECTOR(n-1 DOWNTO 0);
             rollover        : OUT STD_LOGIC );
   END COMPONENT;

   COMPONENT modulo_counter_sler IS
      GENERIC ( n : NATURAL     := 4; k : INTEGER := 15 );
      PORT ( clock, reset, load : IN  STD_LOGIC;
             enable             : IN  STD_LOGIC;
             data               : IN  STD_LOGIC_VECTOR(n-1 DOWNTO 0);
             Q                  : OUT STD_LOGIC_VECTOR(n-1 DOWNTO 0);
             rollover           : OUT STD_LOGIC );
      END COMPONENT;
   
   COMPONENT bcd7seg IS       
      PORT ( bcd      : IN  STD_LOGIC_VECTOR(3 DOWNTO 0);
             display  : OUT STD_LOGIC_VECTOR(0 TO 6) );
      END COMPONENT;

   SIGNAL one_Hundredth : STD_LOGIC;
   SIGNAL H1, H0 : STD_LOGIC_VECTOR(3 DOWNTO 0);  -- HH
   SIGNAL S1, S0 : STD_LOGIC_VECTOR(3 DOWNTO 0);  -- SS
   SIGNAL M1, M0 : STD_LOGIC_VECTOR(3 DOWNTO 0);  -- MM
   SIGNAL roll_H0, roll_H1, roll_S0, roll_S1, roll_M0, roll_M1 : STD_LOGIC;
   SIGNAL en_H0, en_H1, en_S0, en_S1, en_M0, en_M1 : STD_LOGIC;
	
BEGIN
					 
	slow_clock: modulo_counter_er
      GENERIC MAP ( n => 27, k => 500000) -- Set 'n' to 27 bits, defining a large enough range for high-frequency counting; 
                                          -- 'k' is set to 500,000, so the counter will reset after reaching this count.
      PORT MAP( clock => CLOCK_50,        -- Connects the system clock signal (CLOCK_50) to the clock input of the counter.
                reset_n => KEY(1),        -- Connects KEY(1) to the reset_n input for asynchronous reset, used to reset the counter.
                enable => KEY(0),         -- Connects KEY(0) to the enable input, which controls when the counter is active.
                rollover => one_Hundredth ); -- Outputs the rollover signal to 'one_Hundredth', toggling when the counter reaches k-1.


   en_H0 <= one_Hundredth; -- Enables the next counter stage (H0) once every hundredth of a second, based on the 'one_Hundredth' signal from slow_clock.
   U_H0: modulo_counter_er -- Instantiates a modulo-10 counter component named 'U_H0', used to count hundredths of a second.
      GENERIC MAP ( n => 4, k => 10 ) -- Sets 'n' to 4 bits, allowing it to count from 0 to 9 (mod-10), with 'k' set to 10, so it rolls over after reaching 9.
      PORT MAP (CLOCK_50,             -- Connects the main system clock (CLOCK_50) to the counter.
                KEY(1),               -- Connects KEY(1) to the reset_n input, allowing an asynchronous reset for the counter.
                en_H0,                -- Connects 'en_H0' as the enable signal, which allows counting to occur only when en_H0 is high.
                H0,                   -- Outputs the current count value to 'H0', representing the lower digit of hundredths of a second.
                roll_H0);             -- Outputs the rollover signal to 'roll_H0', which toggles high when the counter reaches its max value (k-1) and resets.



   en_H1 <= one_Hundredth AND roll_H0;
   U_H1: modulo_counter_er
      GENERIC MAP ( n => 4, k => 10 )
      PORT MAP (CLOCK_50, KEY(1), en_H1, H1, roll_H1);   

   en_S0 <= en_H1 AND roll_H1;
   U_S0: modulo_counter_er
      GENERIC MAP ( n => 4, k => 10 )
      PORT MAP (CLOCK_50, KEY(1), en_S0, S0, roll_S0);   

   en_S1 <= en_S0 AND roll_S0;
   U_S1: modulo_counter_er
      GENERIC MAP ( n => 4, k => 6 )
      PORT MAP (CLOCK_50, KEY(1), en_S1, S1, roll_S1);   

   en_M0 <= (en_S1 AND roll_S1) OR (NOT KEY(1));
   U_M0: modulo_counter_sler
      GENERIC MAP ( 4, 10 )
      PORT MAP (CLOCK_50, '0', NOT KEY(1), en_M0, SW(3 DOWNTO 0), M0, roll_M0);

   en_M1 <= (en_M0 AND roll_M0) OR (NOT KEY(1));
   U_M1: modulo_counter_sler
      GENERIC MAP ( 4, 6 )
      PORT MAP (CLOCK_50, '0', NOT KEY(1), en_M1, SW(7 DOWNTO 4), M1, roll_M1);
		
   -- drive the displays
   digit5: bcd7seg PORT MAP (M1, HEX5);
   digit4: bcd7seg PORT MAP (M0, HEX4);
   digit3: bcd7seg PORT MAP (S1, HEX3);
   digit2: bcd7seg PORT MAP (S0, HEX2);
   digit1: bcd7seg PORT MAP (H1, HEX1);
   digit0: bcd7seg PORT MAP (H0, HEX0);
      
END Behavior;
-----------------------------------------------------------------------------------------------------------------------

LIBRARY ieee;
USE ieee.std_logic_1164.all;
USE ieee.std_logic_arith.all;
USE ieee.std_logic_signed.all;

ENTITY modulo_counter_er IS
   GENERIC ( n : NATURAL := 4; k : INTEGER := 15 );
   PORT ( clock, reset_n : IN  STD_LOGIC;
          enable         : IN  STD_LOGIC;
          Q              : OUT STD_LOGIC_VECTOR(n-1 DOWNTO 0);
          rollover       : OUT STD_LOGIC );
END ENTITY;

ARCHITECTURE Behavior OF modulo_counter_er IS
   SIGNAL counter : STD_LOGIC_VECTOR(n-1 DOWNTO 0);
BEGIN
   PROCESS(clock, reset_n)
   BEGIN
      IF (reset_n = '0') THEN
         counter <= (OTHERS => '0');
      ELSIF ((clock'event) AND (clock = '1')) THEN
         IF (enable = '1') THEN
            IF (counter = CONV_STD_LOGIC_VECTOR(k-1, n)) THEN
               counter <= (OTHERS => '0');
            ELSE
               counter <= counter + 1;
            END IF;
         END IF;
      END IF;
   END PROCESS;
   Q <= counter;
   rollover <= '1' WHEN (counter = CONV_STD_LOGIC_VECTOR(k-1, n)) ELSE '0';   
END Behavior;

-----------------------------------------------------------------------------------------------------------------------

LIBRARY ieee;
USE ieee.std_logic_1164.all;
USE ieee.std_logic_arith.all;
USE ieee.std_logic_signed.all;

ENTITY modulo_counter_sler IS
   GENERIC ( n : NATURAL := 4; k : INTEGER := 15 );
   PORT ( clock, reset, load : IN  STD_LOGIC;
          enable             : IN  STD_LOGIC;
          data               : IN  STD_LOGIC_VECTOR(n-1 DOWNTO 0);
          Q                  : OUT STD_LOGIC_VECTOR(n-1 DOWNTO 0);
          rollover           : OUT STD_LOGIC );
END ENTITY;

ARCHITECTURE Behavior OF modulo_counter_sler IS
   SIGNAL counter : STD_LOGIC_VECTOR(n-1 DOWNTO 0);
BEGIN
   PROCESS(clock, reset, load, enable)
   BEGIN
      IF ((clock'event) AND (clock = '1')) THEN
         IF (enable = '1') THEN
            IF (reset = '1') THEN
               counter <= (OTHERS => '0');
            ELSIF (load = '1') THEN
               counter <= data;
            ELSIF (counter = CONV_STD_LOGIC_VECTOR(k-1, n)) THEN
               counter <= (OTHERS => '0');
            ELSE
               counter <= counter + 1;
            END IF;
         END IF;
      END IF;
   END PROCESS;
   Q <= counter;
   rollover <= '1' WHEN (counter = CONV_STD_LOGIC_VECTOR(k-1, n)) ELSE '0';   
END Behavior;

-----------------------------------------------------------------------------------------------------------------------

LIBRARY ieee;
USE ieee.std_logic_1164.all;

ENTITY bcd7seg IS
   PORT ( bcd     : IN  STD_LOGIC_VECTOR(3 DOWNTO 0);
          display : OUT STD_LOGIC_VECTOR(0 TO 6) );
END ENTITY;

ARCHITECTURE Behavior OF bcd7seg IS
   --       0  
   --      ---  
   --     |   |
   --    5|   |1
   --     | 6 |
   --      ---  
   --     |   |
   --    4|   |2
   --     |   |
   --      ---  
   --       3  
    
BEGIN
   display <= "0000001" WHEN (bcd = "0000") ELSE
              "1001111" WHEN (bcd = "0001") ELSE
              "0010010" WHEN (bcd = "0010") ELSE
              "0000110" WHEN (bcd = "0011") ELSE
              "1001100" WHEN (bcd = "0100") ELSE
              "0100100" WHEN (bcd = "0101") ELSE
              "1100000" WHEN (bcd = "0110") ELSE
              "0001111" WHEN (bcd = "0111") ELSE
              "0000000" WHEN (bcd = "1000") ELSE
              "0001100" WHEN (bcd = "1001") ELSE
              "1111111";
END Behavior;         
```

<p align="center">
  <img src="Photos/part21.gif" style="width: 333px; height: 250px; object-fit: cover;" title="Allowing the timer to run freely up to 1 minute with manual timer pausing" />
  <img src="Photos/part22.gif" style="width: 333px; height: 250px; margin: 0 10px; object-fit: cover;" title="Setting a starting minute for the timer on HEX4 using SW[3 DOWNTO 0]" />
  <img src="Photos/part23.gif" style="width: 333px; height: 250px; object-fit: cover;" title="Setting a starting minute for the timer on both HEX5 and HEX4 using SW[7 DOWNTO 4] and SW[3 DOWNTO 0] respectively" />
</p>


The FPGA-based design implements a real-time, settable clock that displays time in minutes, seconds, and hundredths of a second (MM:SS:HH format). To set an initial time, users can adjust switches SW(7:0) to represent a two-digit minute value, which the clock will use as a starting point. Pressing KEY(1) loads this initial time on the HEX displays; which means that when you press it again it will be like resetting the clock back to the initial time you set using the switches. The clock can be resumed or paused by holding the KEY(0), allowing users to control its timing without resetting the display. This setup enables real-time timekeeping with user-defined initial values and intuitive start/stop functionality.

In this setup, the rightmost four switches control the right HEX display of the minutes, while the leftmost switches control the left HEX display of the minutes. Instead of the typical binary pattern of 128, 64, 32, 16, 8, 4, 2, 1, these switches follow a different arrangement of 8421 for each four switches controlling the display, corresponding to each digit separately. The maximum value that can be set for the minutes is 99, ensuring only two-digit numbers are displayed. Additionally, if the value to be displayed requires a letter (such as in hexadecimal notation), the display will show absolutely nothing, leaving the segment blank instead.


</details>

<details>
  <summary>Waveform Simulation</summary>
	<br>

<p align="center">
  <img src="Photos/part2wave.png" title="Testing different cases of button settings" />
</p>

The waveform results accurately reflect the behavior observed on the FPGA. The value changes occur on the rising edge of the clock. When KEY0 is high, the watch pauses and stops counting, while it resumes normal operation when KEY0 is low. KEY1 remains low throughout the entire simulation since the clock is automatically driven by the board; there is no need to simulate manual button presses by periodically making the wave high and low (like in the Clock, for example). The primary role of KEY1 is to load the initial value, set by the switches, onto the HEX display, and to reset the watch to this value when pressed again. Regarding the switches, the right minute HEX display initially shows values from 8 to 9, then increments to 10 (which would display 'A', but it is invalid and won't show on the board), followed by 11 (which would display 'B', another invalid value), and continuing with 12 ('C' - invalid), 13 ('D' - invalid), 14 ('E' - invalid), and 15 ('F' - invalid). The left minute HEX display operates similarly, starting with a value of 1, then we start operating both digits of the minutes' HEXes with values "1" "1", "1" "2", "1" "3", and "1" "4". As for the output on the HEX displays, initially, all segments show 0, as the clock is low. When the clock goes high and KEY1 is low (not resetting) while KEY0 is low (not pausing the watch), the value in the switches (00001011 = 11 = B) causes the right HEX display to go inactive. This is because, as we mentioned before, letters are not allowed to display on the HEX display in our code, so it shows all segments as low (1111111). The value remains held because the clock is low and KEY0 is high, keeping the watch paused. At 150 ns, when the clock goes high again and the value in the switches changes to 00010010 (representing "1" "2"), the value 12 is displayed on both the right and left minute HEX displays. The segments for "12" are drawn as 0010010 for the "2" and 1001111 for the "1" on the HEX display segments, resulting in the correct time being shown.

Noteworthy is that during the set up for this waveform simulation, we did not use our typical way of setting up the waves by using Edit - Value - Count Value - Count Every, we can simply highlight a region of the wave by pressing and holding the mouse cursor over it then selecting the "Force High (1)" or "Force Low (0)" buttons available in the toolbar to manually set the value of the wave at a specific time.
<br>


</details>

## Conclusion

In this lab, we successfully designed and implemented an 8-bit modulo-k counter and a digital stopwatch using VHDL on an FPGA board. The modulo-k counter demonstrated effective counting capabilities, resetting after reaching the specified maximum value, while the real-time clock showcased the ability to accurately display time using 7-segment displays. Through these implementations, we gained practical experience in digital circuit design, parameterized counting, and utilizing clocks in timed circuits, reinforcing our understanding of fundamental concepts in digital electronics and their applications in real-world scenarios. 

## Resources

|1| Andrew MASTERS (Director). (2016, March 1). 1 bit and 2 bit counter [Video recording]. <br> https://www.youtube.com/watch?v=lj-QGx-Wyw4  
|2| Ashenden, P. J. (2008). The designer’s guide to VHDL (3rd ed). Morgan Kaufmann Publishers.  
|3| Design Mod—N synchronous Counter. (2021, May 1). GeeksforGeeks. <br> https://www.geeksforgeeks.org/design-mod-n-synchronous-counter/  
|4| How To Electronics (Director). (2019, July 14). Stopwatch Using 4 Digit 7 Segment Display & Arduino [Video recording]. <br> https://www.youtube.com/watch?v=XhlMOEGGkOc  
|5| Logic Circuit official web site. (n.d.). Retrieved November 9, 2024, from <br> https://www.logiccircuit.org/index.html  
|6| MArobotic (Director). (2022, February 1). How to make Stopwatch with 7Segment Display Module MAX7219 | how to Arduino control 6digit Stopwatch [Video recording]. 
 <br> https://www.youtube.com/watch?v=dLodeF9Ds2U  
|7| Neso Academy (Director). (2015a, March 12). Introduction to Counters | Important [Video recording]. <br> https://www.youtube.com/watch?v=iaIu5SYmWVM  
|8| Neso Academy (Director). (2015b, March 30). Modulus of the Counter & Counting up to Particular Value [Video recording]. <br> https://www.youtube.com/watch?v=l20xHDJPHBM  
|9| Neso Academy (Director). (2015c, April 4). 3-Bit & 4-bit Up/Down Synchronous Counter [Video recording]. <br> https://www.youtube.com/watch?app=desktop&v=svFUEJkoeVY  
|10| Rania Hussein (Director). (2016, April 4). Creating a waveform simulation in Quartus Prime Lite Edition [Video recording]. <br> https://www.youtube.com/watch?v=e_ksjHd6sY0  
|11| Siddiqui, O. (n.d.-a). Circuit design Stopwatch using 7-Segment Display. Tinkercad. Retrieved November 10, 2024, from <br> https://www.tinkercad.com/things/9UV25tTGCKB  
|12| Siddiqui, O. (n.d.-b). Circuit design Stopwatch Using LCD. Tinkercad. Retrieved November 10, 2024, from <br> https://www.tinkercad.com/things/kvsYLnUJuAN  
|13| Simple Digital Stopwatch Circuit Diagram using 555 Timer IC & CD 4033. (n.d.). Retrieved November 10, 2024, from <br> https://circuitdigest.com/electronic-circuits/digital-stopwatch-circuit-diagram  
|14| Storr, W. (2021, March 12). MOD Counters are Truncated Modulus Counters. Basic Electronics Tutorials. <br> https://www.electronics-tutorials.ws/counter/mod-counters.html  
|15| What is MOD Counters: Design Mod – N Synchronous Counter—Semiconductor for You. (n.d.). Retrieved November 10, 2024, from <br> https://www.semiconductorforu.com/what-is-mod-counters-design-mod-n-synchronous-counter/  


<br>

```mermaid
gantt
    title Work Division Gantt Chart
    tickInterval 1day
    todayMarker off
    axisFormat %a-%Y-%m-%d
    section Preparation         
        Nour Mostafa : active, 2024-10-22 00:00, 01h
        Mohamed Abouissa : 2024-10-22 00:00, 01h
    section Quartus         
        Nour Mostafa : active, 2024-10-23 00:00, 1d
    section ModelSim       
        Nour Mostafa : active,2024-10-23 00:00, 1d
    section Results       
        Nour Mostafa : active,2024-10-23 00:00, 1d
    section Report
        Mohamed Abouissa : 2024-10-25 00:00, 4d
```

We extend our sincere appreciation to Eng. Umar Adeel for his insightful feedback which has significantly contributed to the successful completion of this experiment.

This publication adheres to all regulatory laws and guidelines established by the American University of Ras Al Khaimah (AURAK) regarding the dissemination of academic materials.



