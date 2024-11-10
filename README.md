# Computer Architecture Lab


https://github.com/user-attachments/assets/cf1bb517-8816-4ad3-a6d3-56deb3ef6147

https://github.com/user-attachments/assets/ef2b7863-8adf-4ae9-b218-2f611a3c44be

https://github.com/user-attachments/assets/5c811a64-28a7-49d8-8dfd-63aa5b0cfdeb

The DE2 series has long been a leader in educational development boards, setting itself apart with a wide range of interfaces designed to support various application requirements. Building on its established leadership and success, Terasic introduces the latest addition, the DE2-115, featuring the Cyclone IV E device. This board is designed to meet the growing demand for versatile, low-cost solutions in areas such as mobile video, voice, and data access, while also addressing the need for high-quality images. The DE2-115 strikes an ideal balance between affordability, low power consumption, and an extensive range of logic, memory, and DSP capabilities. The Cyclone EP4CE115 device integrated into the DE2-115 boasts 114,480 logic elements (LEs), making it the largest device in the Cyclone IV E series. It also includes up to 3.9 Mbits of RAM and 266 multipliers, offering a powerful combination of cost-effectiveness, advanced functionality, and lower power consumption compared to previous Cyclone generations. The DE2-115 builds on the features of its predecessors, particularly the DE2-70, while incorporating additional interfaces to support key protocols like Gigabit Ethernet (GbE). It also includes a High-Speed Mezzanine Card (HSMC) connector, allowing for expanded functionality and connectivity through HSMC daughter cards and cables. For large-scale ASIC prototyping, multiple FPGA-based boards can be connected using HSMC cables through this connector, enabling more complex development setups.


In VHDL projects using Quartus, the name of the top-level entity must match the file name exactly. This requirement stems from how Quartus identifies and maps design components during compilation. The software relies on this consistency to locate the primary entity that serves as the starting point for synthesis and simulation. If the entity name and file name differ, Quartus will throw an error, unable to resolve which entity represents the main design. Keeping the entity and file names identical also helps avoid confusion, especially when multiple design files are involved, as it clarifies the hierarchy and makes it easier to organize and troubleshoot the project.

In VHDL, you can mix multiple modeling techniques within the same code. Combining structural, dataflow, and behavioral modeling approaches allows for a more versatile design, enabling each part of the system to be modeled in the most suitable way. For example, structural modeling can organize different components hierarchically, while behavioral modeling within a process can handle complex control logic. Mixing techniques can optimize performance, readability, and resource usage in your FPGA design.

In VHDL, the <= symbol is used as the signal assignment operator rather than a simple = because it represents a more complex behavior tailored for hardware design. Here’s why:
1. Signal Propagation: In VHDL, <= indicates that a value will be assigned to a signal after a certain time delay, representing the natural propagation delay in real hardware. Unlike in software programming where = means immediate assignment, <= reflects that signals in hardware don't change instantly—they propagate and update at specific intervals depending on how the circuit is clocked or scheduled.


2. Concurrency: VHDL is a concurrent language, meaning many assignments and operations happen simultaneously. The <= operator supports this by allowing updates to signals at the same time across the design. If VHDL used =, which typically implies sequential and immediate assignment in software languages like C++, it would conflict with this concurrent execution model.

3 Distinction Between Signals and Variables: In VHDL, there is a difference between signals (which model connections in hardware) and variables (used for temporary storage in processes). Variables use := for immediate, sequential assignments, while signals use <= to handle hardware-like updates. The distinction is crucial in designs with processes or complex circuits.

In terms of assignment operators, VHDL uses <= for concurrent assignment, which sets signals based on the logic described in the architecture and effectively models hardware behavior by updating signals when conditions are met. The symbol => serves a different role, specifying which bits of a vector or parts of a structure should receive values in component instantiations or signal mappings.  In other words, => is used in port mappings to specify connections between entity ports and signals within a component instance. This enables flexible mapping by associating specific signals with the entity’s inputs and outputs. Unlike procedural languages, VHDL doesn’t execute line-by-line instructions; it updates values across all concurrent processes at once. This simultaneous execution models real-time circuit behavior, where changes in inputs trigger immediate recalculations across interconnected components.

The reason VHDL operates concurrently is that it is designed to simulate and synthesize real digital systems, which require all parts to function at the same time rather than in a stepwise manner. This concurrency allows VHDL to describe everything from combinational logic (like multiplexers or adders) to complex sequential logic (like state machines and counters). VHDL’s concurrent execution is what makes it optimal for simulating hardware systems, enabling the description of logic that mirrors real-world parallel operation, crucial for the design and testing of reliable digital hardware.

VHDL, while used for hardware description, bears some analogies to software languages like C++, particularly in the way it handles variables, data types, and structures. For example, VHDL’s signal is akin to a variable in C++, enabling you to store and manipulate values. However, VHDL is fundamentally different in how it processes these values due to its parallel nature. Unlike C++, which operates sequentially, VHDL executes all concurrent statements simultaneously. This parallelism is key to VHDL's role in describing hardware circuits, where multiple signals and components act independently, just as they would in a physical circuit.

In VHDL, a signal is similar to a data type like int in other programming languages. Declaring a signal reserves a place in memory for storing values and allows the signal to carry data between parts of a design. You can define a signal with a single bit or use multiple bits to create a vector, depending on the requirements of your design. Once a signal is declared, it needs to be assigned a value, similar to variable assignments in other languages. 

The external inputs and outputs in VHDL are those specified within the entity section. They define the interface between the design and the outside world. These signals must be connected to the corresponding hardware or signals when instantiated in another design, making the entity section a standardized way to declare what inputs and outputs are available.

Specifying the architecture in VHDL is essential because it represents the implementation of the entity. Multiple architectures can exist for one entity, offering different ways to realize the entity's functionality. The architecture is where the specific modeling technique (behavioral, dataflow, or structural) is defined for that particular design. Also, note that you must always declare what entity the architecture is defining because you could have more than one entitiy in your design.

The process block is a primary construct in behavioral modeling, allowing sequential operations to occur in response to changes in specified signals. A process is triggered whenever any signal in its sensitivity list changes. This flexibility is essential in behavioral modeling as it enables detailed, clock-based or event-driven design execution within the process.

The entity is consistent across all VHDL modeling techniques. Whether using structural, dataflow, or behavioral modeling, the entity defines the same inputs and outputs for the design. It remains constant, representing the interface, while the architecture (containing the actual logic) can vary according to the chosen modeling style.



When designing with dataflow modeling, the number of lines in the code often reflects the number of outputs. Each output assignment has its own line to define how it’s derived based on input values. If there is only one output, then only one line is necessary, but if there are multiple outputs, multiple assignment statements will be present, one per output signal.

RTL and TTL are both historical families of digital logic design. They define types of circuits used to implement logical functions in hardware:

1. __RTL (Resistor-Transistor Logic):__ RTL was an early form of digital logic circuit design that used resistors and transistors to build logical gates. It was a precursor to TTL and is now mostly obsolete. RTL circuits were limited by slower switching speeds and higher power consumption compared to newer technologies.

2. __TTL (Transistor-Transistor Logic):__ TTL circuits, which use only transistors (and not resistors for logic functions), became the standard for digital logic design in the 1970s and 1980s. TTL logic is faster and more power-efficient than RTL. These circuits were widely used in microprocessors and other early digital systems.

In this lab, even though we’re using an FPGA with an Intel Altera DE2-115 board rather than physical TTL or RTL circuits, understanding these fundamentals is still relevant. FPGAs emulate these digital logic behaviors with lookup tables (LUTs), flip-flops, and configurable logic blocks. While TTL and RTL are physical implementations, FPGAs provide a reprogrammable way to simulate these types of logic designs and behaviors in digital circuits. This concept is important as it gives insight into the FPGA’s flexibility to model a wide range of digital logic families, including TTL.

There isn't a fully developed emulator that visually simulates the Altera DE2-115 FPGA board, showing the physical chip and all its peripherals as they would respond to your uploaded code. However, there are tools available that offer partial emulation or simulation of specific aspects of the FPGA, which can assist in testing our designs virtually. Though these tools don't provide a complete visual representation of the board, they do offer ways to simulate its behavior in an interactive manner.

One such tool is Quartus Prime, combined with ModelSim for waveform simulation. While Quartus Prime doesn't generate a graphical view of the physical FPGA board, it does enable simulation of your Verilog or VHDL code. With this, you can observe how your logic design would function on the FPGA. This simulation includes testing the behavior of inputs and outputs, such as switches, LEDs, and buttons, in response to different signals. The process involves designing the logic, running the simulation in Quartus/ModelSim, and viewing the results through waveform viewers. Though this doesn't provide a "physical board" view, it offers insight into how the internal components of the FPGA (such as registers and I/Os) react to various inputs.

The reason full FPGA emulators do not exist stems from the inherent flexibility of FPGAs. Unlike microcontrollers or standard processors, FPGAs are highly customizable, and each design fundamentally alters the chip's behavior. This makes it incredibly challenging to simulate the entire chip, with all its customizable gates and peripheral interactions, in a real-time visual format. As a result, the industry typically relies on logic simulators, like ModelSim, to analyze and test the behavior of the design rather than attempting to emulate the entire board visually.

Here’s a rundown of some of the recurring Quartus Prime file extensions:
1. __QDF - Quartus Design File:__ This file is often used for storing design logic in a form Quartus can read, typically intermediate.
2. __JDI - JTAG Debug Information:__ Stores debugging information used for JTAG connections.
3. __DONE - Done File:__ Used to indicate a completed compilation step or process.
4. __QWS - Quartus Workspace File:__ Holds settings related to the Quartus working environment, useful for managing workspaces.
5. __QPF - Quartus Project File:__ The main project file that connects to all other project-related files.
6. __QSF - Quartus Settings File:__ Contains the pin assignments and other project settings.
7. __BAK - Backup File:__ A copy of previous versions of certain files as a safeguard.
8. __RPT - Report File:__ Contains detailed reports from the compilation, analysis, or timing results.
9. __SUMMARY - Summary Report:__ Offers an overview of results from the compilation process.
10. __VHD - VHDL File:__ Contains VHDL code defining logic for synthesis.
11. __SMSG - Simulation Message:__ May store log messages for simulation, depending on setup.
12. __SOF - SRAM Object File:__ The bitstream file downloaded to the FPGA for programming.
13. __SLD - SignalTap Logic Analyzer File:__ Stores configuration for SignalTap debugging setups.
14. __PIN - Pin File:__ Contains information on the pin assignments for the project.
Deleting any of these files can impact different aspects of your workflow. For example, deleting the SOF file removes the bitstream, requiring recompilation for a new one. The QSF file holds pin assignments, so deleting it would require reassigning pins.

When you’re unsure about which variables to use in the stimulus of your testbench, a helpful approach is to check the top-level entity of your design. Look for input and output signals like SW, HEX, KEY, or any other variable names in that entity, as these typically represent the inputs and outputs managed by the testbench. The inputs are the signals that the stimulus will set values for, simulating real inputs during testing.

When connecting the USB cable to the board, make sure to insert it into the left port at the top, where it says "USB Blaster." This port is specifically for programming and debugging, and the name labeled on it confirms its purpose. Using the correct port is essential for your connection to function properly.

If you ever need to identify the model of the board and don’t have the manual nearby, simply look at the CPU area on the board itself. The model name is usually printed there, allowing you to confirm the board model easily.

In digital design, a key distinction exists between combinational and sequential circuits. Combinational circuits produce outputs purely based on their current inputs, without relying on past states or a clock signal. This means that any change in the inputs instantaneously affects the outputs, making combinational circuits suitable for logic operations where immediate results are needed. In contrast, sequential circuits depend on a clock to drive them. A clock signal enables sequential circuits to store and update states, allowing them to maintain a history of input changes over time. This timing aspect makes sequential circuits essential for designs involving memory or state transitions, such as counters and registers, where the circuit behavior depends on both current and previous inputs.
