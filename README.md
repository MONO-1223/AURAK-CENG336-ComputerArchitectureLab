# Computer Architecture Lab


https://github.com/user-attachments/assets/cf1bb517-8816-4ad3-a6d3-56deb3ef6147



https://github.com/user-attachments/assets/ef2b7863-8adf-4ae9-b218-2f611a3c44be



https://github.com/user-attachments/assets/5c811a64-28a7-49d8-8dfd-63aa5b0cfdeb


In VHDL projects using Quartus, the name of the top-level entity must match the file name exactly. This requirement stems from how Quartus identifies and maps design components during compilation. The software relies on this consistency to locate the primary entity that serves as the starting point for synthesis and simulation. If the entity name and file name differ, Quartus will throw an error, unable to resolve which entity represents the main design. Keeping the entity and file names identical also helps avoid confusion, especially when multiple design files are involved, as it clarifies the hierarchy and makes it easier to organize and troubleshoot the project.

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
