# <p align="center">Latches, Flip-flops, and Registers</p>

abstract

---

introduction

## Part 1: Design and Implementation of an RS Latch

In the first part, we will create an RS latch, which is a basic memory element used in digital circuits to store a single bit of information. The RS latch operates with two main inputs: Set (S) and Reset (R), and produces two outputs: `Q` and its complement `Q'`. The function of the latch is straightforward: when the Set (S) input is activated, the output `Q` becomes 1, and `Q'` becomes 0, effectively setting the latch. Conversely, when the Reset (R) input is activated, the output `Q` becomes 0, and `Q'` becomes 1, resetting the latch. If both inputs are inactive (set to 0), the latch holds its current state, remembering the last set or reset condition. Refer to [Figure 1](Photos/SRlatch.png) for a visual representation of the RS latch.

In this part, we will also explore how the RS latch behaves when both inputs are activated simultaneously, leading to an invalid state where the outputs `Q` and `Q'` are inconsistent. We will implement the RS latch using two cross-coupled NOR gates, which provide the necessary feedback loop to maintain the stored bit of data when both inputs are inactive. This simple latch design serves as a foundation for understanding more advanced memory elements in digital systems.

Refer to [Figure 2](Photos/Part1Q.png) for a visual representation of the gated RS latch. In this configuration, the latch receives `R`, `S`, and `clk` as inputs, producing `Qa` as the output and `Qb` as its complement. Additionally, the `signals` `R_g` and `S_g` are derived by ANDing the `R` and `clk` signals together and the `S` and `clk` signals, respectively. This gating mechanism helps control the operation of the RS latch based on the clock signal, ensuring that the latch responds appropriately to the inputs while maintaining its state as needed.

<details>
  <summary>VHDL Code Implementation on the FPGA Board (RS Latch)</summary>
<br>

``` VHDL
LIBRARY ieee;
USE ieee.std_logic_1164.all;

ENTITY part1 IS

   PORT ( Clk, R, S : IN  STD_LOGIC;          -- Assigning my inputs R,S and Clk
          Q         : OUT STD_LOGIC);         -- Assigning my output Q
     
END part1;

ARCHITECTURE Structural OF part1 IS

   SIGNAL R_g, S_g, Qa, Qb : STD_LOGIC ;          -- Creat my signals R_g, S_g, Qa and Qb
   ATTRIBUTE KEEP: BOOLEAN;                       -- The KEEP attribute is defined as a boolean value, which can be used to instruct the synthesis tool to preserve certain signals during optimization.
   ATTRIBUTE KEEP OF R_g, S_g, Qa, Qb : SIGNAL IS true;           -- By applying the KEEP attribute to R_g, S_g, Qa, and Qb, we ensure that these signals are not optimized away or removed, which is particularly important in maintaining the functionality of the RS latch during synthesis.

BEGIN

   R_g <= R AND Clk;               -- R_g is signal that have the valuse of R AND-ing Clk
   S_g <= S AND Clk;               -- S_g is signal that have the valuse of S AND-ing Clk
   Qa <= NOT (R_g OR Qb);          -- Same as shoing in the Question 
   Qb <= NOT (S_g OR Qa);          -- Same as shoing in the Question 

   Q <= Qa;                        -- My primery output Q is the values of my Qa signla

END Structural;                   
```
</details>


<details>
  <summary>VHDL Testbench Code Simulation in ModelSim</summary>
<br>

``` VHDL
library IEEE;
use IEEE.Std_logic_1164.all;
use IEEE.Numeric_Std.all;

entity part1_tb is
end;

architecture bench of part1_tb is

  component part1
     PORT ( Clk, R, S : IN  STD_LOGIC;
            Q         : OUT STD_LOGIC);
  end component;

  signal Clk, R, S: STD_LOGIC;
  signal Q: STD_LOGIC;

begin

  uut: part1 port map ( Clk => Clk,
                        R   => R,
                        S   => S,
                        Q   => Q );
						
  stimulus: process
  begin
  
-- Put initialisation code here

  R <= '1';
	S <= '0';
	clk <= '1';
	wait for 10 ns;
	
	R <= '0';
	S <= '1';
	clk <= '1';
	wait for 10 ns;
	
	R <= '0';
	S <= '0';
	clk <= '0';
	wait for 10 ns;
	
	R <= '1';
	S <= '1';
	clk <= '1';
	wait for 10 ns;

-- Put test bench stimulus code here

    wait;
  end process;
end;
```

<p align="center">
  <img src="Photos/part1rtl.png" title="ModelSim Result" />
</p>

In the simulation of the SR latch with a clock, we observe that the latch responds to the clock signal and the inputs `S (Set)` and `R (Reset)` to determine the output. When the clock `(CLK)` is high, the latch reads the values of `S` and `R`. For example, in the first case, when the clock is high, `S` is 0, and `R` is 1, the output `Q` is set to 0, as the reset input is active. In the second case, with the clock still high, `S` is 1 and `R` is 0, resulting in the output `Q` being set to 1, since the set input is now active.

When the `clock` goes low, the latch enters a holding state, meaning it retains the value of the previous output regardless of the inputs `S` and `R`. For instance, if the previous output was 1, the latch holds this value when the clock is low. Lastly, when both `S` and `R` are set to 1 while the `clock` is high, this represents an invalid state for the SR latch. In this scenario, the output becomes undefined or may take on a random value, as the set and reset conditions are being applied simultaneously, which leads to a logical contradiction. This simulation highlights how the SR latch behaves depending on the `clock` signal, with valid outputs when the `clock` is high and an undefined state when both `S` and `R`` are high simultaneously.

</details>

<details>
  <summary>VHDL Simulation in Waveform Editor</summary>
<br>

</details>
