# VSD - Physical Design Using OpenSource Tools Workshop
This repository details the report of all the technical works performed during the VSD- Physical Design workshop through OpenSource tools.
PDK Used : SKyWater 130nm

Date : 22nd May 2024

## Contents
1. [Module-1 : Inception of OpenSOurce EDA Tools and Skywater 130 nm Designs](#module-1-inception-of-opensource-eda-tools-and-skywater-130-nm-designs)
   - [Synthesis of Design Through OpenLANE](#synthesis-of-design-through-openlane)
2. [Module-2 : FloorPlanning, PowePlanning, Placement and Introduction to Library Cell Characterization](#module-2--floorplanning,-poweplanning,-placement-and-introduction-to-library-cell-characterization)
   - [Floorplanning Through openLANE](#floorplanning-through-openlane)
   - [Congestion Aware Placement using RePlAce](#congestion-aware-placement-using-replace)
3. [Module-3 : Design Library Cell Using MAGIC Layout and NGSPICE Characterization](#module-3-design-library-cell-using-magic-layout-and-ngspice-characterization)
    - [Inception of Layout and 16 Mask CMOS Fabrication Process](#inception-of-layout-and-16-mask-cmos-fabrication-process)
    - [Layout and SPICE Parameter Extraction Through "Magic"](#layout-and-spice-parameter-extraction-through-"magic")
    - [Magic Tool Options and DRC Rules Check](#magic-tool-options-and-drc-rules)
4. [Module-4 : Pre-layout Timing Analysis and Clock Tree Synthesis](#module-4-pre-layout-timing-analysis-and-clock-tree-synthesis)
    - [Synthesis and Timing Analysis of New Custom Cell](#synthesis-and-timing-analysis-of-new-custom-cell)
    - [Timing Analysis Using OpenSTA](#timing-analysis-using-opensta)
    - [CTS Using TritonCTS Tool](#cts-using-tritoncts-tool)
5. [Module-5 : Final Steps for RTL2GDS -Routing and Post-Route Timing Analysis](module-5-final-steps-for-rtl2gds--routing-and-post-route-timing-analysis)
    - [PDN Through OpenLANE](pdn-through-openlane)
    - [Routing Through TritonRoute](routing-through-tritonroute)

## Introduction
In the current scenario where using the commercial tools or software may feel expensive and difficult to master, the opensopurce EDA tools are trying to fill the gaps
to reach the mass of semiconducter enthusisat. Opensource tools are great way to learn the semiconducter fabrication flow starting from logic synthesis to chip tape out. Though as of now the optimizations of the tools and model libraries are not upto the mark as compared to commercial ones on performance basis, it is still useful for beginners and may turn out as a great deal in the future too.

  In this report, I have documented all my learnings as part of completion of "VSD-Physical design through opensource tools" workshop as well as tried to add a small contribution towards the community which I hope will be helpful for new learners.

## Objective
The following workshop involved the complete RTL to GDSII design flow of a RISC-V processor architecture considering all the steps- RTL synthesis, static timing analysis, DFT, floorplan, placement, CTS, routing, physical verification as well as sign-off of the chip through the openLANE ASIC design flow with the Skywater 130nm PDK for all model libraries.

![download](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/33c9085b-b05e-484e-a381-adac5c2f3db5)

The openLANE ASIC design flow involves the following opensource tools for different processes-
1. YOSYS & ABC : Logic Synthesis
2. OpenSTA : Static Timing Analysis
3. FAULT : DFT
4. OpenROAD : Physical Design (FP, PP, Placement, Routing)
5. MAGIC & NETGEN : Cell Characterization & Physical Verification


# Module-1 : Inception of OpenSOurce EDA Tools and Skywater 130 nm Designs
The development and adoption of open-source EDA tools provide a cost-effective, accessible alternative to proprietary softwares. Projects such as OpenLANE, which integrates multiple open-source tools into a cohesive flow, brings greater functionality and ease of use to the design process. In the open-source hardware movement, the SkyWater 130 nm Process Design Kit (PDK) represents a significant milestone providing comprehensive documentation, design rules, and process parameters. 

 This module provides the foundation for the chip to be designed alongside introduced the OpenLANE ASIC design flow and Skywater 130 nm openPDK.

 The chip featured a Quad Flat No-Leads (QFN) die package with 48 pins occupying an area of 7mm x 7mm. The SoC comprised of a CPU with GPIO bank designed on RISC-V ISA. Moreover, it included various foundry IPs such as PLL, SRAM, ADC & DAC all interconnected to the IO pads via wire bonds to ensure the chip's functionality.

![download (1)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/9652e6d8-23f7-4b9e-b9ef-97ef0c04e439)

## Synthesis of Design Through OpenLANE

The primary objective of this module was to commence the openLANE flow, thereby initiating the logic or RTL synthesis of the design through YOSYS and ABC. Besides a supplementary task involving the calculation of the flop ratio for the synthesized design was introduced at a later stage as part of the assignments.

To invoke the tool the, it's essential to set up the Docker environment.
```
$ docker
```
This command launches the openLANE Docker container and mounts the current working directory to the /openLANE_flow directory inside the container. It then sets the working directory to /openLANE_flow.

Following the Docker setup, the flow.tcl file is executed using the Tcl command:
```
$ ./flow.tcl -interactive
```
The -interactive switch enables step-by-step execution of the flow, allowing users to intervene at various stages if necessary. By default, the flow executes all steps automatically, but with -interactive, users have the option to execute each step manually, providing greater control and flexibility throughout the design process.

![333826538-be2d9e99-fd10-4f57-87f0-ede688cbe389](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/e141fba4-b138-4427-9af8-636ac933b580)

Following the tool initialization, the next step involved installing additional packages using the command-
```
% package require openlane 0.9
```
Before proceeding with synthesis, it's essential to complete the design setup stage, which involves preparing all necessary directories for executing the specific design. In this case, the ```picorv32a``` design was selected for synthesis, located in the ```openlane/designs``` directory.

Use the following command to prepare the design:
```
% prep -design picorv32a
```
Once the setup is ready we run the synthesis through ```run_synthesis``` command which generates synthesized design output and stores inside the picorv32a design directory.

![333826581-611c0011-a309-4f2c-a89c-ac9f6885acfc](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/01afaf0e-ef81-41f0-a29c-b3e1924993cb)

![333826680-e1583f55-2802-41e4-9e6d-b642848216a6](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/a4ba1097-7511-444f-8d7e-8ce72eee8301)


After the synthesis, the files are stored inside the ```/picorv32a/runs/results``` and ```/picorv32a/runs/reports``` directory under the ```designs``` directory. All the timing reports, synthesized netlist and cell reports can be found inside these directories.  

![Screenshot from 2024-05-26 00-34-56](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/e1583f55-2802-41e4-9e6d-b642848216a6)

Coming to the assignment part, the flop ratio represents the ratio between total no of D-flipflops used to the total no of cells present in the synthesized design.

These data can be found inside the ```yosys.stat.rpt``` file inside ```picorv/runs/reports/``` which assembles all the data of logic block used in the design.

![Picture1](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/9ab2048f-2021-4549-bf77-30b482fec933)

The number of D-flipflops are represented by ```sky_fd_sc_hd_dfxtp_2``` and it was divided by the total number of cells in the design to get the flop-ratio.

Flop Ratio = 1613 / 14876 = 0.10842

This concludes the first module.


# Module-2 : FloorPlanning, PowePlanning, Placement and Introduction to Library Cell Characterization
After the synthesis stage comes the floorplanning stage where we arrange the pre-placed cells in the power rail and ground rail grid of the core followed by powerplanning, pin assignment and placement of standard cells. In this module we have executed the floorplanning stage in openLANE and viewed it layout results through the Magic tool. Also a minor task of representing the core dimeison in micron units was done as part of the module.

Afterwards, we have moved to powerplanning, pin assignment and placement steps and at the end briefed about the library cell characterization using the SPICE parameters, DRC rules and timing constraints.

## Floorplanning
Floorplanning typically invovles the ordering or arrangement of IPs which also could be referred as pre-placed cells in the power-ground mess of core of the chip.

![Picture3](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/4d7a05fe-58d1-4007-9315-2ba8d8deba96)

The core of chip is the portion where all the logic blocks are assembled or fabricated whereas the die suurounds the core as well as provides extra area for IO pin assignment. During floorplan stage in order to provide some space for routing and standard cell placement in further steps, we need to set some parameters for it which will be implemented by the tool during the execution.

The two essential parameters are 
1. Utilization Factor : The utilization factor refers to the area occupied by the synthesized netlist to the total area of the core i.e. Utilizatiion Factor = Area Occupie by Netlist / Total Area of Core
2. Aspect Ratio : The aspect ratio represents the shape and size of the cell i.e it is the ratio between height and width of the cell. Aspect ratio = Height of Cell/Width of Cell

A utilization factor of 1 which 100% represents that the total area of core is occupied leaving no space for extra logic. Therefore, in practical cases we consider 50-60% of core utilization to leave some space for extra buffers which helps in fixing timing violations as well as maintains signal integrity.

An aspect ratio of 1 refers to a square shape and rectangles for other values.


### Concept of Pre-placed cells
Macros (also called hard macros) are large, pre-designed blocks that perform specific functions within the chip. these are characterized by having a fixed layout and cannot be easily resized or reshaped. Macros can be either large memories or special IPs (DSP, ADC, DAC) which is implemented once and is repeated throughout the chip for further use with the same functionality. These are placed in user defined locations which is automated through place and route tool.

Pre-placed cells refer to smaller, often critical cells that are strategically placed during the floorplanning stage. These can include standard cells or specialized logic cells that need to be fixed in position early in the design process.Pre-placed cells, such as clock buffers or critical logic gates, might be positioned near the macros to optimize timing and minimize signal delay.

The process where pre-placed cells undergo steps called "Partition" involves breaking down a large logical block into smaller blocks, each performing the same function as the original block. This granular approach aids in floorplanning by enabling the reuse and placement of the same netlist across different areas of the chip, facilitating lower design effort and layout efficiency.

![download (5)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/3385fb6a-5778-4314-a10e-4c9c286e95fd)


### De-Cap Cells
Another type of cell placed during the floorplanning stage is the de-coupling capacitor cells (de-cap cells). As their name implies, these cells contain additional capacitors that decouple the actual voltage source from the cell. During switching, the capacitor in the de-cap cell acts as the immediate voltage source, stabilizing the supply voltage for the cell.

![Picture4](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/aa68be1d-3e3e-4ae5-b34b-c2821fe77f1e)

When a cell is located too far from the main power source, the switching signal can deteriorate before reaching the cell due to the resistive and inductive effects of the interconnects. This degradation introduces noise into the signal and affects the cell's switching performance. To counteract this issue, additional capacitors are placed in parallel with the cell. These capacitors charge to the same voltage as the power source during idle periods and act as a local power source during switching activities. This approach is particularly useful near cells with high switching activity.

The power issue of local cells or local communication is solved by de-cap cells but there still exists power issue in global communication that is between macros(with de-cap cells) which are connected together (Driver-Load Configuration) through wires and requires switching simultaneously. In order to solve this we use the powerplanning stage.

## Powerplanning
During simultaneous switching, two primary issues in global communication arise:
1. Ground Bounce - Problem due to discharging of multiple cells through a single ground line and hence creating a voltage bump/spike
2. Voltage Droop - Problem due to demand of charging multiple cells at the same time through a single vdd line creating a voltage drop/trough

![picture5](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/832f15a0-0331-4447-85ca-a7e9908259fd)

To mitigate these issues, a grid or mesh of power and ground lines is implemented across the core in the power planning stage. This network provides the necessary charge close to the cells, stabilizing the voltage .

![Picture6](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/87be7110-18cd-4a72-8fc8-1ddf2beb3749)

### Pin Assignment and Logic Cell Blockage 
Apart from the core area, to connect the input and output nodes of the netlist to external environment, pins are assigned at the boundary of the die. This requires handshkaing between frontend and backend VLSI design as the input/output pins should be placed near to the input/output nodes of the logic which rersults in random distribution of pins around the die.

The pins include both data pins and clock pins with the clock pins slightly bigger in size to help reduce the path resistance which affects the drive strength of the clock.

Moreover, in order to not to place any logic blocks and routing channels in the pin area, logical cell blockage is used which reserves the pin locations.

![download (12)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/c1345860-16ca-4ef4-8f49-8a62f6d8e986)

## Floorplanning through OpenLANE
After synthesizing the netlist using OpenLANE, the next step is floorplanning. This involves arranging the various blocks and cells of the synthesized netlist into a defined area on the chip, optimizing for performance, power, and area.

Before executing the floorplan command, it's crucial to understand the default configuration settings and switches available in OpenLANE floorplan flow. These can be found in the ```openlane/configuration/``` directory.

![334467819-09950797-7653-4aff-8005-50b83fe3dd6a](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/0d90c2b0-8a50-4c9e-8156-9673b9500945)

- The README.md file contains switches for all the flow stages. We will be focusing on floorplan here.

![334468114-e937fc22-9ae5-440b-b7f8-f4fd40e3f45e](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/9f241809-d449-4e8b-9180-26187e171ada)

- The "floorplan.tcl" file contains default values such as core utilization, number of horizontal and vertical metal lines, and other parameters.

![FloorplanDefualtTCL](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/89640b4c-963a-498c-974a-373c2c64cac1)

*Note : In openLANE flow floorplanning stage, the vmetal and hmetal is always one more than what is specified in the config file.

Precedence of Parameters : The floorplan follows a speicifc precedance order for parameters having least priority to system defualts-
- ```configuration/floorplan.tcl``` (least priority)
- ```designs/picorv32a/config.tcl```
- ```designs/picorv32a/sky130A_sky130_fd_sc_hd_config.tcl``` (highest priority)  

Running the Floorplan Command : Now use the ```run_floorplan``` command in the openlane terminal to execute the synthesized netlist.

![334469347-89f7b882-ea76-4e79-9ac5-e2a0f25cfc1f](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/4e03e4ad-9629-4373-a545-c906f54eb8ea)

Floorplan Output : After the run is completed, the results and reports are stored in ```openlane/designs/picorv32a/runs```.

Key Outputs : DEF file- Located in ```runs/results/floorplan``` directory, named ```picorv32a.floorplan.def```.  The def file basically translates the logical designs into physically manufacturable layouts. In further steps we will see how to view this floorplan layout through "Magic" tool using the def file.

![334469626-0050f92e-bb03-4918-ada1-f2a6d1acb8fe](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/e342a5a5-093e-4209-8444-b084f28ebcee)

Now coming to the assessment- 
- UNITS DISTANCE MICRON 1000 : 1 micron is equal to 1000 data base points.
- CORE DIMENSION : ( 0 0 ) ( 660685 671405 ) where the first coordinate represents the bottom left corner of die and second coordinate represents top right corner of the die.

Hence, the dimension of the core in micron units can be calculated from the above values as -
1. Height = ( 671405 - 0 )/1000 = 671.405 microns
2. Width = ( 660685 - 0 )/1000 = 660.685 microns 

So, the area of die = 671.405 x 660.685 = 443587.212425 sq. microns

Additional Configurations :
- config.tcl : Located in the ```picorv32a/runs/``` directory, this file contains core utilization, aspect ratio, core margin, IO metal layers, target density, etc.
  ![configTCLfile](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/ea4b6642-f376-46f0-a669-6f1d3f9f5b6d)
- Logs : Found in ```logs/floorplan/``` directory, these logs contain detailed information about the floorplan run and gets updates with every iteration.
  ![334475167-22cc2578-0768-446e-8296-cddf79311b64](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/06f866e9-6f23-449d-8743-9ca434f059fe)

We can also compare the switch precedance that we earlier discussed through this directory as it stores the data of IO metal layers, vmetal and hmetal data of the specific run. You can verify the number of metal layers from the "ioPlacer.log" file after the run with the "config.tcl" before the run. 

Visualizing the Floorplan : To view the floorplan layout, use the Magic tool with the DEF file data:

To invoke the Magic tool within the current directory, locate the technology file for Magic and provide the LEF and DEF file information as follows -
```
$ magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.floorplan.def &
```
![334470416-733ef30c-3374-4977-a2f4-883830dfcb25](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/1a5c868c-4c2d-474b-acaf-8700f1e62057)

![FP1](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/82ab4188-bb27-474e-af63-5d91ae9c5b04)

Layout Checks : We can now verify all the data through the layout.

- "IO PIN MODE = 1" represents all the pins in the die are equidistant from each other which can be seen in the figure.
- Clock Pins : The clock pins are slightly wider than the data pins so as to facilitate the drive strength.
- Decap cells are present near the boundries
- Tap cells (which help avoid latchup conditions) are placed at the middle with diagonnaly equidistant from each other.
- Type "what" in the magic command window to get the data of utilization factor and metal layers

![FP2](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/194a2c80-1a11-4b63-9008-649919b2340e)

![FP5](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/15810110-232f-48e6-afbe-ec88b1ee455e)

*Note : Floorplan doesnt take into consideration of standard cells but it can still be seen in the lower left corner of the layout which will be later on get placed in their respective position in the "Placement" stage.

![FP4](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/c87836b8-cb9b-4853-ba43-deee56263862)

Adjusting Parameters : In the openLANE flow the parameters of certain floorplan variable can be changed on the fly . For example, change the pin placement strategy using the following switch to see the diffeence-
```
% set ::env(FP_IO_MODE) 2
2
% run_floorplan
```
This will generate a floorplan with pins unevenly distributed across the die.

![UnevenPin](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/5d2d4e4b-c928-4c44-b321-ae120e7ba3f3)


## Placement
Placement involves the arrangement of standard cells in their specific position on the power-ground grid of the core.

Binding Netlist with Physical Cells : During this step the netlist is bound/mapped with physical cells having specific dimensions i.e. fixed height and varible widths.  These cells are library-specific and come with detailed information, including dimensions, timing data, and setup conditions. 

Key Objectives:
- Distribution of Cells: Cells are placed on the floorplan created in the previous step.
- Proximity to I/O Ports: Cells should be placed close to their respective input/output ports to meet the timing constraints.

### Optimization of Placement Using Estimated Wirelength and Capacitances
To start from the last point of the last section, we often face the challenge of not able to place the cells near to their respective IO ports. Sometimes the cells are far away from the port due to previous floorplans or other reasons which leads to more wiring cost. The more the wiring, the more resistance and capacitance effect it will have, leading to signal noise issues and greater slew values.

Solutions:
- Repeaters/Buffers: Placing repeaters or buffers between ports and cells can maintain signal integrity by replicating the original signal, though at the cost of some additional area.
- Datapath Checking: Verify the datapath under ideal clock conditions and setup conditions to ensure proper placement.

![Screenshot (1609)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/fcd46fa0-7675-41c6-81a6-013e41ca6abb)

## Congestion Aware Placement using RePlAce
In this step, congestion-driven placement is performed, leaving timing-driven placement for later stages. To start the flow on the openlane terminal post floorplan, run the command 
```
$ run_placement
```
![334473681-9c1da2b7-6064-4b5b-a404-d1d1b725b037](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/88a8b0f1-42a7-4daf-95ee-d640366554a7)

There are two section of the placement step-
1. Global Placement - Course grain placement with no legalisation
2. Detail Placement - Fine grain placement with legalisation

*Note : Legalisation refers to the placement of standard cells in exact rows of the grid without any overlaps.

For congestion driven placement, the goal is to reduce the wirelength and openlane tool uses Half Perimeter Wire Length (HPWL) method to define the location of the cells. The parameters for this method can be seen in the ```config.tcl``` file in the ```picorv32a/runs/``` directory. Again, one thing to note in the file is that the OVFL value that represents the overflow condition should converge during the simulation.

Visualization of Placement : To view the placement layout in Magic, invoke the tool in the placement directory and use the ```picorv32a.placement.def``` file as the input argument. (Similar to floorplan steps)

![place1](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/91a645ae-1aae-4cb4-a503-f46d1fdd5f35)

![place2](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/c85f0343-fa50-4adb-80df-26738c192bec)

![place3](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/7818c094-6238-4e35-aa24-dc0cce6b3a4e)


By following these steps, the placement phase in the OpenLANE flow is completed, ensuring that cells are optimally arranged to meet design specifications and constraints.

## Cell Design and Characterization Flow
Moving a bit apart from the flow in openLANE, we will discuss about the library cell characterization and respective design flow as cells are the basic block behind all the netlist,floorplan and placement. 

A library file contains all the standard cells (AND,OR,NOT,Buffer,latch,ICG) with different dimensions and threshold voltage as well as various macros, IPs and decap cells, with timing information. The specific cell design process to add it to the library involves the following-
1. Inputs
   - Process Design Kits (PDKs) : Provided by foundries, containing technology-specific data
   - DRC, LVS Rule with lambda-based design rules : Ensure the physical layout meets manufacturing constraints and matches the schematic.
   - SPICE model : Include parameters for PMOS and NMOS transistors.
   - User defined specs : Define cell dimensions, supply voltage, metal layers, drawn gate length, etc.

2. Design Steps
   - Circuit Design : Create the transistor-level schematic.
   - Layout Design : Develop the physical layout of the cell.
   - Characterization : Analyze timing, power, and noise characteristics.

3. Outputs
   - CDL (Circuit Description Language)
   - GDSII, LEF, Extractwd SPICE netlist (.cir)
   - Timing, Noise, power (.libs)   
   
### Characterization Flow
Characterization of a cell is the most important aspect of a library cell generation which includes timing, power and noise analysis of the cell. The characterization typically involves the following steps-
- Reading the SPICE models (pmos & nmos model files)
- Reading extracted SPICE netlist (RC values)
- Recognizing the behaviou of the circuit and reading its subcircuit
- Attaching the necessary power and ground
- Applying the stimulus
- Providing necessary output capacitance to measure the values of charge, current and delays
- Providing simulation commands like tran and dc
At last all these data output is fed to the "GUNA" characterization tool which outputs the config file.

The following terms define some of the timing characterization values of cells which helps calculating propagation delay and transition time-
```
- slew_low_rise_thr
- slew_high_rise_thr
- slew_low_fall_thr
- slew_high_rise_thr
- in_rise_thr
- in_fall_thr
- out_rise_thr
- out_fall_thr
```
Propagation delay = Out_time_threshold - In_time_threshold

- Propagation delay is calculated by the difference of output and input time at 50% point of both the waveforms.

Transition Time = (slew_high_rise - slew_low_rise) or (slew_high_fall - slew_low_fall)

- Transition time is calculated by the difference of time between 80% & 20% point of the same waveform. 

![download (40)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/adb7e4fe-996d-42b7-b383-dd52c15b7fa8)

![download (41)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/0fc2a48e-ac21-4969-bbed-1a01671d54d8)



# Module-3 : Design Library Cell Using MAGIC Layout and NGSPICE Characterization
In this module we will discuss about the library cell characterization and layout generation through "Magic" tool with emphasis on fabrication steps of a 16 mask CMOS inverter to better understand the design rules of the layout. Further, we'll analyze the timing characteristics of the inverter (rise time, fall time, and propagation delay) using Magic and then import this design into our library "picorv32a," generating the LEF files.

## SPICE Deck Creation for CMOS Inverter
SPICE is a powerful, general-purpose analog electronic circuit simulator that is used to verify the integrity of circuit designs and predict circuit behavior. The circuit is described in a SPICE netlist, which includes component values and connectivity, along with accurate models for the components (transistors, diodes, etc.), based on process technology data. Simulation parameters and analysis types (transient, AC, DC) are specified, and the output results are analyzed to verify the circuit's performance.

Here in this report we will discuss about a CMOS inverter and hence in the first step we will create a SPICE deck or netlist for the inverter circuit.

![Picture16](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/0ab30571-911c-4215-a86d-158ffed85783)

Example of SPICE deck program for CMOS inverter :
``` 
M1 out in vdd vdd pmos w=0.375u l=0.25u      // ModelName Drain Gate Source Substrate Type Width Length
M2 out in 0 0 nmos w=0.375u l=0.25u          // ModelName Drain Gate Source Substrate Type Width Length

cload out 0 10f                              // Output load capacitance
vdd vdd 0 0.25                               // Supply voltage
vin in 0 0.25                                // Input Voltage

** Simulation Commands**
.op
.dc vin 0 2.5 0.05                           // DC Analysis Input ZeroValue OneValue Steps

** .include tsmc_025um_model.mod **
.lib "tsmc_025um_model.mod"                  // Model file containing the description of pmos and nmos
.end
```

### Static Simulation of CMOS Inverter

Whatever we have done in the above setup is called as the static simluation or DC analysis of a CMOS inverter which produces a voltage transfer curve (VTC) when plotted.

The robustness of a CMOS inverter can be studied through its static behavior, which is one reason for its wide use.

The main characteristic of a VTC which defines the specfic inverter is the switching thershold or trip point which is nothing but the point where both input and output has the same voltage value. At this point both pmos and nmos are at satuaration region leading to a huge amount of current flow. 

Switching Threshold Conditions : 
1. Vgs = Vds     (Gate-source voltage is equal to drain-source voltage)
2. Idsp = -Idsn   (The summation of drain current of pmos and nmos is always zero)

The switching threshold depends heavily on the transistor sizing (W/L ratio of the transistors). A higher W/L ratio for PMOS shifts the VTC to the right, while a higher W/L ratio for NMOS shifts it to the left. The switching threshold can be calcculated by drawing a 45 degree angled line from origin and the point at which it intersects with the curve is taken as the trip point.

![download (16)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/3d2cd71a-25dd-448d-840d-b9493fb502a6)

Example VTC and Switching Threshold :
 
![download (17)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/84b27762-4151-49c7-ab46-37c76a83c547)

### Dyanamic Simulation of CMOS Inverter 
Dynamic simulation of a CMOS inverter refers to the analysis of its performance under varying input signals over time. This type of simulation helps in understanding the transient behavior and timing characteristics of the inverter, which are crucial for characterization of cells. We will use this transient analysis to calculate the timing characteristics i.e. rise time, fall time & propagation delay of the inverter through SPICE simulations.

Example SPICE Deck for Transient Analysis :
```
M1 out in vdd vdd pmos w=0.375u l=0.25u      // ModelName Drain Gate Source Substrate Type Width Length
M2 out in 0 0 nmos w=0.375u l=0.25u          // ModelName Drain Gate Source Substrate Type Width Length

cload out 0 10f                              // Output load capacitance
vdd vdd 0 0.25                               // Supply voltage
vin in 0 pulse 0 2.5 0 10p 10p 1n 2n         // Input Voltage: pulse 0 to 2.5V with specified delays and pulse width

** Simulation Commands **
.op
.tran 10p 4n                                 // Transient Analysis: Step time 10p, Total time 4n
.lib "tsmc_025um_model.mod"                  // Model file containing the description of pmos and nmos
.end
```

![download (18)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/f8c671df-3afe-4b10-8b44-0517cc2bfc71)

This SPICE deck will produce time-dependent waveforms from which delay values of the inverter can be calculated by setting the load capacitances. The setup is carried out in NGSPICE for further analysis.

## Inception of Layout and 16 Mask CMOS Fabrication Process
The 16-mask CMOS process is a specific technology where 16 photolithographic masks are employed during the fabrication. the mask is an opaque plate which blocks the UV light to react with certain areas of
photo-resist. Part of resist is exposed to UV light, which gets washed away giving us the open areas for further process steps. We will brief about these fabrication steps to help understand the depth of design rules during the layout generation of the cell. 

### Selecting a Substrate
We start with choosing a substrate as a base for fabrication. In general a lightly doped (~10^15 cm-3) P-type silicon substrate with high resistivity (5 ~ 50 ohm) is selected.

![download (19_1)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/bce3d333-af8d-4a59-9592-87e86ae4dbbd)

### Creating Active Region for Transistor
An isoltaion over active area is created by growing a thin layer of (~40nm) silicon oxide (SiO2) followed by a layer of (~80nm) of silicon nitride (Si3N4) and photoresist (~1um) for photolithography process ( Action between UV light and photoresist). Then the hard mask is placed over the photoresist help avoid the area which will be etched out. This mask is reffered to as the mask-1 .

![download (23_1)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/8d68323e-1ebe-48df-a344-8de11daa65c0)

Once this is done we expose it to UV light and use the developing solution to dissolve and remove the exposed region. Then remove the mask and etch off Si3N4 followed by removing the phototresist which was below the mask. To create an isolation between the two active rgions where pmos and nmos will be fabricated, we put it again in a oxidation furnace which grows a second layer of SiO2 in the gaps .This process is called as "LOCOS" or "Local Oxidation of Silicon" and the structure of oxide is referred as "bird's beak". Finally the remaing Si3N4 is etched out to get only the oxidised layer on the top.


### N-Well and P-Well Formation
Once the isolation is done between active regions, we again grow the photoresist layer over it and put the mask-2 at the other region where we want create the well.Expose it to the UV light to remove the exposed area and take out the mask. Now through ion implantation process we diffuse boron into the substrate at 200Kev to create the p-well.  

![download (21_1)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/a8976557-7dd5-4d80-8633-7089bff9507e)

Similarly, to create the n-well we use the photoresist and the 3rd mask followed by diffusion of phosphorous into substrate at 400KeV. This require more enrgy as phosphorus is a heavy metal.

Later to improve the depth of diffusion of the wells, the wafer undergoes a high-temperature annealing process in a furnace. This step is known as "drive-in diffusion". The high temperature causes the dopant atoms to move from areas of high concentration (surface) into the silicon lattice, spreading out to create a uniform dopant distribution at the desired depth. This whole process is also known as "Twin Tub Process" as we create both p-well and n-well.

![Screenshot (1636)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/6d4e524f-4599-43e6-bb43-edf9c8bdd37b)

### Formation of Gate Terminal
The threshold voltage Vt of a device is highly dependent upon the doping concentraion of p and n region as well as oxide capacitance. In order the control both, before gate formation we modify the doping concentraion of p-well by depositing photoresist and creating mask-4 followed by boron diffusion at low energy (~60KeV). This facilitates the desired concentration of p-region.

![download (25_1)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/3f052baf-27be-4a6a-8f89-a976e8df9097)

Similar steps are taken for the n-region creating the mask-5 and diffusing arsenic afterwards at a low temperature.

Again to control the oxide capacitance we etch off the damaged silicon oxide (because of repeated ion implantation) by dissolving it into HF solution followed by growing a thin layer (~10nm) of high quality SiO2 which controls the threshold voltage Vt.

Now for gate formation, we use a polysilicon layer(0.4um) over it and then the photoresist and mask-6 (also referred as polysilicon mask) for the pattern generation. In most cases, bfore etching out the photoresist, we dope it with n-type ion implant for low gate resistance of the device. Once the UV exposer and removal of photoresist is done, we get the following structure of the gate (right one)-

![download (27_1)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/b59e2944-7ab4-4c46-a985-eb3b170b3f2b)

### Lightly Doped Drain (LDD) Formation
We want to achieve a doping profile of P+,P-,N in nmos and N+,N-,P in pmos region. The P+ & N+ represent the source drain doping whereas N and P rpresnt the substrate dopings. The profile P- & P+ represents the lightly doped drain in the well. So basically there are two reasons for this extra LDD formation -
1. Hot Electron Effect : When device size is reduced, the elctric field provided by the power supply becomes stronger leading to breakage of Si-Si bonds by the high energy carriers as well as crosses the 3.2eV barrier between the Si and SiO2 conduction band and hence penetrating into the oxide.
2. Short Channel Effect : Due to the reduced size of the device, the drain field penetrates to the channel leading to effects like channel length modulation, velocity saturation and DIBL.

Therefore in order to avoid these phenomenon we use the LDD formation technique.

It has similar process of photoresist deposition and mask-7 creation as well as phosphorous (N-) diffusion for nmos. For pmos we use the mask-8 with diffusion of boron (P-) for LDD formation.

![download (29_1)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/2e4fa6ac-d72d-4f66-8470-6b3400d3686f)

In order to protect the LDD in later processes of formation of source and drain with N+ or P+ dopings, we need to create the side-wall spacers. A thick layer (~0.1um) of Si3N4 or SiO2 is deposited over the surface and hence  plasma anisotropic etching is carried out which forms the side-wall spacers around the gates which protect from the exposer of diffusion into LDD.

![download (31_1)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/40fb63f1-44b5-47a5-848a-30326ec40df9)

### Source Drain Formation 
Before the source and drain formation, a thin layer of screen oxide is deposited over the surface in order to avoid channeling during the implantation.

Now in similar fashion we deposit the photoresist and mask-9 above it and implant the arsenic (N+) on the exposed area with a energy of 75KeV. This creates the source and drain region for the nmos transistor. Similarly for pmos source drain we use the mask-10 and boron (P+) as implant for it with 50KeV energy. 

![Screenshot (1641)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/bdc7e948-214a-41b7-80d6-5e43c26953c2)

Afterwardss, to increase the penetration depth of the diffused implants we put it in a high temperature furnace for the annealing process.

### Contacts and Local Interconnect Formation
Contacts are the only thing that can be accessed by the user to control the pmos and nmos.

First of all remove the the thin screen oxide by dissolving it in HF solution. Now deposit the titanium on the wafer by the process of sputtering which acts as a low resistant contact. Further the wafer is heated at about 650-700 degree centigrade in N2 ambient condition to form the local contact TiSi2. Again due to this another material TiN is formed which is used for the local communication interconnect.

![download (33_1)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/1bab1814-9fb0-40dd-a93e-9b6f22cb55b2)

So now to bring the bottom connection up, we will use the layer of photoresist and mask-11 exposing the non-contact areas. Now the excess TiN is etched out using RCA cleaning which is nothing but a combination of deionized H2O, NH4OH and H2O2 solutions and hence the TiN contacts remains.

![download (35_1)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/dacbfb18-04fc-4e79-87be-92d37666598f)

### Higher Level Metal Formation
To avoid the non-linear surface topography of the wafer we need to planarize it. To achieve this deposit a thick layer (~1um) of SiO2 doped with phosphorous or boron (known as phosphosilicate or borophospho-silicat) which act as a protective layer for the mobile Na ion as well as boron reduces the overall temperature. Now the wafer surface is polished using a process called CMP (Chemical Mechanical Polishing) and outputs something similar the figure below followed by drilling the contacts into it by the same photolithography technique using the mask-12. 

![download (37_1)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/27fc21c7-b3b7-41f5-8106-329b0a6edb37)

Now remove the mask and etch out the SiO2 to drill the contact holes making it accessible to upper layers and use a thin layer (~10nm) of TiN which is very good adhesion material for TiSi2. The next step is to deposit a blanket tungsten layer for the contact and carry out the CMP process for planarizing it.

![Screenshot (1652)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/d1d729f1-b916-4e56-9f0d-808259f4c457)

Now to bring the contacts outside of the chip we deposit a aluminium layer (which is a higher metal layer) and pattern it through photolithography to take it to top layer. Now use the same method of SiO2 depostion followed by CMP and tugsten-aluminium layers while using the masks13-15 to take it outside of the chip. Nowadays instead of aluminium, copper is used as the alternative for higher metal layers due to its low resistivity.

![download (39_1)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/98f3ab71-04d9-4761-926c-6531cdf78f8f)

At the end, in order to protect the chip a Si3N4 layer is deposited on the top metal layer wchich uses the final mask-16.

![Screenshot (1656)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/b7de523e-cd71-407d-8016-7409796b5878)

This ends the total process of fabricating a 16 mask CMOS inverter with source , drain, gate output terminals.

![Screenshot (1657)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/435e0439-3fbe-4ff8-a67a-96ef4d2b17bb)



## Layout and SPICE Parameter Extraction Through "Magic"
For reference we have taken a pre-built cmos inverter cloning it from the github repo - https://github.com/nickson-jose/vsdstdcelldesign

After cloning it to the ```/openlane``` directory, several files will be present. Our point of interest is the ```sky130_inv.mag``` file, which stores the layout information.

Invoking the Magic Tool : Invoke the Magic tool using the tech file and pass ```sky130_inv.mag``` as the argument to view the layout in the Magic window.
```
$ magic -T <tech_file_path> sky130_inv.mag
```
![335428851-e37f273e-c399-4e7b-b24b-0786ebf4a06a](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/584fc877-66f0-485b-bb36-4b6947348610)

Viewing the Layout : Once the layout is open in the Magic tool, it will appear in the Magic window as shown below-

![InverterLayout](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/1b9c9d3d-8cdf-4fbf-8e89-e6468f13bc30)

We can check drc errors in the layout through the ```DRC``` tab and from the drop down list, select the ```DRC Find Next Error``` and it will lead to the area of the error as well as list down the errors in command line. We will discuss about the design rules in Magic in later sections. 

![335452811-4b4f8a74-bca6-41f1-aedf-fae99a89b188](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/a64d1764-f876-4561-9bf0-95c9991a9ecb)

Extracting the Layout : Now to extract the layout, use the following command in the command line of Magic-
```
% extract all
```
To extract the netlist along with parasitics for use in NGSpice, use the following commands-
```
% ext2spice cthresh 0 rthresh 0
% ext2spice
```
![335454543-6aacae3f-0688-406e-987f-a4fd0e33aca1](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/97219964-6084-48b9-a754-121202f5cf86)

After executing these commands, the following extracted files will be generated in your directory-

![335454796-15df62b3-1410-48db-a772-d51a31351b06](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/db75c2dc-1560-4f88-acae-769f1ecdcf5c)

These steps allow you to prepare the CMOS inverter layout and extract the necessary files for further analysis and characterization using NGSpice.


## SPICE Deck Creation and Timing Characterization of the Inverter
After extracting the SPICE netlist from the layout, we modified it for transient analysis and created the final SPICE deck for the inverter.

![Screenshot 2024-06-01 055227](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/44711b2b-fee4-4c26-bc1a-bdebbe5d9c32)

Running the Simulation : We simulated the inverter through NGSPICE to obtain the output waveforms and calculate the timing characteristics.

![Screenshot 2024-06-01 054747](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/586fefab-8ac7-4419-bbad-fd08b0d1c02f)

![Screenshot 2024-06-01 054706](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/00f711ad-3419-48d5-b5b4-4db6c3380b46)

Timing Characterization : The rise and fall delays of the output waveform were calculated at 20% (0.66V) and 80% (2.64V) levels, and the rise and fall propagation delays were calculated at 50% (1.65V) levels of both input and output by taking the data from the displayed waveforms.

![Screenshot 2024-06-01 054637](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/86847f87-2733-4b81-9888-cb22eb5f5a0f)

- Rise Delay = 2.23949e-09 - 2.17978e-09 = 0.05971ns
- Fall Delay = 4.09326e-09 - 4.05065e-09 = 0.04261ns
- Rise Propagation Delay = 2.20701e-09 - 2.14989e-09 = 0.05712ns
- Fall Propagation Delay = 4.07529e-09 - 4.05057e-09 = 0.02472ns

These values summarize the timing characterization of the standard library cell at a nominal temperature of 27 degrees Celsius.

## Magic Tool Options and DRC Rules
In this section, we will explore the DRC rules for the open-source SkyWater 130nm PDK. Below is the process stack diagram for the SkyWater 130nm process node. For more detailed information on the rule set, visit theSkyWater PDK documentation - https://skywater-pdk.readthedocs.io/en/main/rules/periphery.html#rules-periphery--page-root .

![image](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/114ed88f-5354-4d8a-b2bb-973727b4e12b)

To check and verify some of the rules as part of the exercise, we have sourced files from the website - http://opencircuitdesign.com/open_pdks/archive/drc_tests.tgz which contains various layout models in Magic.

![image](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/6ea40030-172e-4512-a845-00daa54cd01c)

![image](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/eccb26cc-ebff-4221-a583-1b87af682b2d)

The ```.mag``` files provide the layout files, while the ```.magicrc``` file tells the Magic tool where to find the tech file.

Exercise 1 : Resolving DRC Errors for Poly and nres Poly Spacing

As the first exercise for resolving the drc rule we have taken an example of poly and nres poly spacing which is in the ```poly.mag``` file. According to the documentation, the poly-to-poly distance should be at least 0.480 microns. However, the poly-to-nres poly distance is not considered. Our goal is to add this rule to the sky130A.tech file 

We first checked the distance between the poly and nres poly through creating a box and viewing its height on the terminal. Clearly the space is 0.210 microns which is less than the desired space.

![Screenshot 2024-06-01 105944](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/c453a061-daa9-4d0c-b98c-c7c0ca3d407a)

Now to resolve this issue we added the following rule set to ```poly.9``` which will define the rules for poly and nres poly spacings.

![Screenshot 2024-06-01 110505](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/79fec960-c969-43a0-bb45-22a6e7582e16)

We then loaded the updated tech file into Magic through the Magic command line and checked if the DRC error was resolved. The following figure confirms that the issue is resolved as there is an error can be seen for the poly to nres-poly spacing which earlier wasn't there.

![335780573-13321c51-9a81-4f59-a36c-322c8c9a7b8f](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/abff5ea8-3a77-419d-804c-44b051b96387)

Exercise 2: Adding DRC Rules for Spacings between nres Poly and Diffusion

In the next exercise, we added a DRC rule for spacings between nres poly and diffusion. In this case, we are not checking the diffusion rules, so they can be ignored for now. In the example, there are errors for the spacing between poly and diffusion, but no errors for the nres poly and diffusion.

![Screenshot 2024-06-01 152029](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/c3ff0faf-6460-45bf-8627-fc0c0b9d9d97)

To add the rule as per the documentation, we have added the following error constrcut in the tech file which serves the error for all diffusion spacings.

![Screenshot 2024-06-01 152507](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/17d63e20-3b5d-41cc-bc71-919de16b00a5)

After loading the updated tech file and checking the DRC, the errors can be seen, highlighted by yellow circles in the figure.

![Screenshot 2024-06-01 152935](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/f6ab5552-9047-45c3-a883-f285fa956c65)

Exercise 3: Geometrical Constructs of DRC Rules and Missing Rules in n-well Structures

In the next few exercises, we dealt with the geometrical constructs of DRC rules and missing rules through the n-well structures defined in ```nwell.mag``` file. The following figure shows part of the documentation containing the DRC rules for the n-well in the Sky130 nm PDK.

![Screenshot 2024-06-01 154922](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/7a7fda54-80c5-47d6-83bd-1d7f84abe97d)

To describe the DRC error in geometrical constructs, we used an example of dnwell placed inside the nwell. According to rules nwell.5 and nwell.6, the outer and inner layer of the dnwell must be surrounded by nwell layers with minimum widths of 0.4µm and 1.03µm, respectively.

To describe the errors in geometrical construct we have to invoke the command ```cif ostyle drc``` in the Magic terminal. We have taken an example of inner layer error of dnwell which can be seen through the command ```cif see dnwell_shrink``` (Refer to the figure).

![Screenshot 2024-06-01 162028](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/90d4c0b9-3dd6-45f4-8233-c1db4eb14274)

Exercise 4: Fixing Missing or Incorrect DRC Rules in n-well Layout

Moving towards the next exercise, we identified some missing or incorrect DRC rules in the n-well layout and fixed them according to the documentation. In the figure below, there are no errors in the n-well structure based on the current rule set in the tech file.

![Screenshot 2024-06-01 163826](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/401b3bf1-3096-4f18-89d0-a8e193efca5a)

However, according to rule nwell.4, all n-wells should contain metal-contacted taps inside them. Therefore, to fix this missing rule, we modified the tech file by adding the following constructs -

![image](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/9f3da849-98da-452e-b9a4-7a95f6a4cc38)

PS: The ```bloat-all``` operator has set of all n-well that contains tap. What we have done is, we have taken the set of all n-well and from that, removed all the set of n-well containing taps. Whatever now is leftover, is taken as an error.

Afterwards, we have defined the error in the "N-well" section as full variants, which means that this error will be showed whenever we run the drc in ```full``` style.

![Screenshot 2024-06-01 174222](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/108a6f0a-c4b8-4cc5-814d-a21decdfd2a1)

After updating the tech file and running the DRC check, it can be seen in the figure the n-well without the tap shows an drc error whereas the n-well with tap, at the right side, doesnt show any errors. Hence we have fixed the missing drc rule.

![Screenshot 2024-06-01 183728](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/f3e24948-d0b7-43fa-9c5a-6bfa245f2b9b)





# Module-4 : Pre-layout Timing Analysis and Clock Tree Synthesis
Till this point we discussed about the extraction of the layout of a standard cell as well as calculated the timing characterization values for the cell. Our next goal is to plug in this newly designed standard cell to our working design "picorv32a" and check its functionality. In this module we will be focusing on how to import the data associated with the cell to the design library in form of LEF as well as make it ready for PnR stages.

OpenLane, being a place and route tool, relies heavily on LEF files. A LEF file provides information about the cell's power & ground rails, inner PR boundary and input/output port information. While the extracted layout file (.mag) contains comprehensive details, including power, ground, and logic implementation, these details are often redundant for PnR processes. Therefore, extracting a simplified LEF file that focuses on the necessary elements is crucial. 

## Synthesis and Timing Analysis of New Custom Cell
### Conversion of Grid Info to Track Info
Tracks are essentially the paths through which routing occurs, connecting the outside IO pins to the cell's IO nodes. To get the track information of the SkyWater 130nm PDK, move to the directory ```openlane/pdks/sky130A/libs.tech/openlane/sky130_fd_sc_hd/``` and open the ```tracks.info``` file.

PS : The first element in a track info represents the track layer followed by horizontal (X) or vertical (Y) direction followed by offset value followed by metal pitch 

![Screenshot 2024-06-01 221016](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/a03c81cf-6133-4c00-a8b0-389ddf5920d2)

The Place and Route (PnR) tool follows specific guidelines to execute its automated process, including -
- The IO port must lie on the intersection of vertical and horizontal tracks
- Widths of standard cells should be odd multiple of track pitch
- Height should be odd multiple of vertical track pitch

So in order to check the guideline that our designed inverter's IO ports lie on the intersection of vertical or horizontal tracks or not, we changed the grid dimension of layout in magic as per the layer1 tracks (As the IO ports are on layer1) and verified its correctness. Refer to the figure for the intersection point at input A and output Y.

![Screenshot 2024-06-01 221306](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/1aa099d7-e95d-4859-bab1-192b5661d96e)

![Screenshot 2024-06-01 221246](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/d2470e14-5d89-4116-9af0-cf06fca054ac)

The second and third guidelines of the PnR tool can also be verified through the grid dimensions, as the grid dimension refers to the pitch, and the inner PR boundary contains 3 horizontal boxes and 7 vertical boxes. Therefore, our design is fully compatible with the PnR tool.

### Conversion of Magic Layout to Standard Cell LEF
Setting Up Ports : The first step for this would be the conversion of all the labels of inverter into specific ports. This can be done through the "Edit" tab and then selecting the "Text" option. 

Now set the port name (Text String), size, layer attached and enable the port for all the labels. The enable number should be given as per you want it on the LEF file.

![Screenshot 2024-06-01 231223](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/bb1f55e1-55ad-4470-9879-ba281f2190cb)

![Screenshot 2024-06-01 231102](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/874088d4-1399-4134-ae98-0a171b0ff015)

![Screenshot 2024-06-01 231017](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/7605ec7d-d5b6-4bcc-b7e8-d4d16e193741)

![Screenshot 2024-06-01 231307](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/d6fa89f8-73cd-4bbf-b659-34b3e9a01a13)

Define Port Attributes : Now we have to define the port class (input/output/inout) and port use attributes (power/ground/signal) for every port in the magic command terminal and save it with a custom name for further use.
```
% port class <input/output/inout>
% port use <signal/power/ground>
```

![Screenshot 2024-06-01 232151](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/8831babb-82da-4cc0-be24-bdfd5cb3b081)

Generating LEF File : Now open the newly saved .mag file in magic and use the command ```lef write``` to generate the LEF file in the current directory.

![Screenshot 2024-06-01 232744](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/9ed3e163-5842-41c8-a41e-cee47ce1b66f)

Now open the .lef file to see all the contents as we have assigned before. See the figure to verify all the properties.

![Screenshot 2024-06-01 232841](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/1c6f10d1-deb7-4363-aa9a-39c81ecfe6c8)

### Additon of New Std Cell to Library and Its Synthesis
To integrate the newly designed custom cell into the "picorv32a" design and verify its functionality, we have follwed these steps:

Copy LEF File : Copy the lef file of the cell to the source directory of the design library ```openlane/designs/picorv32a/src/```.

![Screenshot 2024-06-02 083748](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/541d2585-0aad-4e08-8c9e-cbba1d6226b9)

Copy Library Files : Copy the technology library (typical, fast and slow corner) files from the ```/vsdstdcelldesign/libs/``` directory to the same source directory of picorv32a design ```openlane/designs/picorv32a/src/```. The library files contain the information of cells in different PVT corners such as tt,ff & ss .

![Screenshot 2024-06-02 092935](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/0c294637-6946-47e2-8826-ca55f46395a2)

![Screenshot 2024-06-02 092159](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/2d55b65f-96e3-4a21-b1ba-4c07e2d4435d)

Update confi.tcl File : Modify the ```config.tcl``` of the picorv32a design by adding these paths through switches for the run -
```
% set ::env(LIB_SYNTH) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__typical.lib"
% set ::env(LIB_FASTEST) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__fast.lib"
% set ::env(LIB_SLOWEST) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__slow.lib"
% set ::env(LIB_TYPICAL) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__typical.lib"

% set ::env(EXTRA_LEF) [glob $::env(OPENLANE_ROOT)/designs/$::env(DESIGN_NAME)/src/*.lef]
```
![Screenshot 2024-06-02 095616](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/8b6e8cba-2165-48dc-8c37-2c0b53d40f05)

*Note : The LIB_SYNTH switch is used for the synthesis whereas LIB_FASTEST, LIB_SLOWEST & LIB_TYPICAL switches are used for STA analysis. The "EXTRA_LEF" switch adds the extra lef file for the process.

Initiating OpenLANE Flow : Invoke the openLANE tool and add the new LEF files to the OpenLANE session and then run the synthesis.
```
% set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
% add_lefs -src $lefs
```
![Screenshot 2024-06-02 114950](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/60ce01e1-21ea-415b-b3d1-3bf04e57dcde)

![Screenshot 2024-06-02 115654](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/b1757779-fe35-4577-9141-8f569728b986)


Our synthesis is successful with adding the new standard cells. Now we will move the timing aspects of the cells and its effects.

We can see at the end of the synthesis there are two parameters ```tns``` and ```wns``` are present. These represents the Total Negative Slack and Worst Negative Slack respectively. Slack is the difference of required time and arrival time for a cell. In order to reduce these timing violations we can take certain measures such as delay and area balance i.e. improvement in delay at the cost of area. 

As discussed earlier, in the openLANE flow there exists some switches for every stage. We can try to implement it through the terminal to change the variables taken for area and delays in the circuit during synthesis.

![image](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/e4c43a2e-1978-4d57-ae86-98627ff2b524)

We can play around the following synthesis switches to reduce the slack but as of now our concern is to add the custom cell to the library, we will be focusing on this slack issue on static timing analysis part.

![Screenshot 2024-06-03 082923](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/ea667c32-655a-445f-8310-f9d5b64733ed)

Verify Custom Cell in merged.lef : To verify whether our custom inverter cell information is added to design or not, check the ```merged.lef``` file in the ```/picorv32a/tmp/``` directory.

![Screenshot 2024-06-03 084022](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/a5ee9383-6920-4369-abdd-4d7c7e161efb)

Run Floorplan and Placement : Run the floorplan followed by placement to see the results. 
Visualize Placement in Magic : Open the magic tool and pass the new .def as argument to view the placement result window.

![image](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/1e9d7d57-d875-4069-a2ea-b1c541242854)

![download (43)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/70e350df-af22-4a8e-9d81-8b9b2d114f09)

Our custom cell (sky130_vsdInv) can be seen with its layout, being placed on the design. Hence, we have successfully integrated our custom cell to the design and now can move to further optimizations. 



### Introduction to Clock Tree Synthesis and Delay Table
Clock Tree Synthesis (CTS) is primarily aimed at distributing the clock signal from a single source to all the sequential elements (flip-flops and registers) in a chip. The goal of CTS is to ensure that the clock signal reaches all these elements with minimal skew and jitter, ensuring synchronized operation. For this purpose we use buffers in the path of clock nets. This in turn requires more power. So to reduce power consumption in CTS (also called as Power Aware CTS) we use techniques like clock gating through AND gates and OR gates which turns on or off with an enable signal from the user.

Example for Buffer Timing Calculation and Delay Table :

![download (44)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/563a0188-9c4f-4af2-b61d-b69cdd754502)

Consider the following assumptions for timing calculation -
- c1 = c2 = c3 = c4 = 25fF
- cbuf1 = cbuf2 = 30fF

Therefore, total cap at node-A = 60fF, node-B = 50fF, node-C = 50fF

The observations can be listed as -
- 2 Levels of buffers are present in the diagram
- AT every level, each node is driving same load
- Identical buffer at same level

Note that the load capacitance of buffers are not equal to the output capacitances. Output capacitances are varying, hence it produces a variety of delays. So in order to create timing models a table is created between varying input slew (from 10-100ps) to the varying ouput capacitances (from 10-100fF) and the respective delay values are noted. This is called as the delay table which characterizes the delays of the buffers.

From this delay table we can estimate the delays at each node and hence will be able to calculate the skew (The difference of arrival time of clock pulse between two FF/registers) produced at he end. It plays a crucial role during the static timing analysis as it may violate the setup and hold times.


## Timing Analysis Using OpenSTA
### Setup Time And Intro to FF Setup Time
A flip-flop stores or passes data depending on the clock edge, consisting of two MUX structures. This causes a delay (setup time) before data passes to output. The setup time affects the clock period of the circuit.

![download (45)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/66e9b22f-3590-40f3-834f-96e6110cf940)

In an ideal clock scenario (no clock tree/buffers), the combinational delay between launch and capture flip-flop should be less than the clock period to synchronize data. Due to flip-flop internal delay (setup time or MUX1 Delay), the clock period is reduced, constraining the combinational delay:

Combinational Delay(θ)=Clock Period(T)−Setup Time(S)

![download (46)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/8be7596c-6146-43fe-ac1b-45ca0deb9263)

### Clock Jitter
The clock signals or pulses are basically generated by a PLL inside a chip. But due to non-idealities and inherent resistance and capacitance variation inside the PLL, there is a small window is created based on  the arrival of the clock pulse to the required clock edge. This temporary variation of clock period is know as the jitter. 

![download (47)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/dad97bfd-3203-498a-9034-80be68723d21)

Due to this jitter, another variable is added to the constraint of the combinational delay which can be represented as-

Combinational Delay(θ) = Clock Period(T) − Setup Time(S) − Jitter(SU)

### Setup Time for Real Clock
In real clocks, delays of extra buffers in the clock path produce skew (difference in arrival time of clock signal between flops). The constraint for combinational delay with real clocks is- 

Combinational Delay(θ) + Δ1 < Clock Period(T) + Δ2 − Setup Time(S) − Jitter(SU)

Δ1 and Δ2 are basically the delay of buffers present in the clock path of FF1 and FF2.

![download (48)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/278c4690-42c1-4a9c-95b7-9a6a3f4d928e)

The left hand side term is called as arrival time whereas the right hand side term is called as required time. The difference between required time and arrival time is called as setup slack.

### Hold Time for Ideal and Real Clock
Hold time is the minimum time that data input (D) must remain stable after the clock edge for reliable latching by a sequential element (flip-flop). It is due to the second MUX present in the capture flop which takes some amount of time to send out the data or latch the data. 

![download (49)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/42ba4f5b-9c2d-4d9b-9e3a-8a174f56a3b7)

For ideal clocks : Combinational Delay(θ) > Hold Time(H)

But for real cases due to wire RC delays in clock path there might be skew between the clock edges like earlier cases as well as uncertainty due to PLL. 

So for real clocks : Combinational Delay(θ)+ Δ1 > Hold Time(H) + Δ2 + Hold Uncertainty(HU)

![download (50)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/2a2ac0f4-e1ae-4077-8f06-584f771dc985)

In this case also the left side term is reffered as the arrival time whereas the right side term is referred as required time. Only difference is the slack is defined as the differece of time between the arrival time and the required time for the flops. 


### Timing Analysis Using OpenSTA
openSTA is a the open source tool used to perform the static timing analysis during various phases of the flow. In the first section we have configured the openSTA tool outside of the openLANE flow to better understand the requirements and output of the analyzer. This tool is used basically for the slack reduction through various switches available for the synthesis, placement and CTS. 

A STA environment requires some necessary files to execute it process which includes -
- Synthesized netlist (.v file)
- Library Files (.lib)
- Constraints (.sdc file)

To load our design we need to create a script cotaining our custom design and the following library files for min, max and typical corner analysis. In this flow we have created a ```pre_sta.conf``` file for the same containing the verilog data, min/max analysis, linking design, reading sdc followed by reports for tns and wns (slack information). We have creted this file in the ```/openlane``` directory.

![Screenshot (1732)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/aff81c71-8f6d-458c-b6ff-5fa090ef5cdb)

Followed by creating this file we have created the constraints file linked with it (similar to base.sdc file for openLANE flow) which contains the information of clock definitions, input output delays and max fanout along with some additional data like clock port, clock period, driving cell and load capacitance specific to our design. We have named the file as ```my_base.sdc``` in the ```openlane/designs/picorv32a/src``` directory.

![Screenshot (1731)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/3146cf75-bbcb-4ee2-93b4-f7f3ae25fc33)

After the files for the tool are configured run the ```% sta pre_sta.conf``` command to execute the flow.

![Screenshot (1702)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/ab72e913-1122-4f62-b3fa-6bf977776e1d)

We can see after the STA analysis there is slack violation is present (below image) in the design and hence we need to fix it to some extent before CTS, which adds buffers to meet the timings. 

So first we tried to look out for the cells with high fanout which were causing the delays and checked it characteristics through the command -
```
% report_net -connections <net_number>
```
![Screenshot 2024-06-05 132522](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/5c907cd0-81ec-4eb0-9292-cda129765a50)

As these were having high fanouts of 5 and 6, we limited the max fanout number and ran the synthesis again for updated netlist. 
```
% set ::env(SYNTH_MAX_FANOUT) "value"
% run_synthesis
```
![image](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/6379dc50-e9b5-408a-9da9-ae7fb56942b9)

Again we increased the drive strength of few cells with higher capacitances values to reduce the overall value. We replaced the existing cells with their respective upsized cells for this purpose and again executed the sta analysis with report checks.

```
% replace_cell <Instance> <LibraryCellName>
% report_checks -fields {net cap slew input_pins} -digits 4
```
![image](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/83526898-bab3-441d-bf75-05ddf0f33f85)

After the modifications now we can see a slightly reduced slack and now we can move further to the CTS step.

![Screenshot 2024-06-05 142315](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/3af8d89a-fd55-4dcb-8e3b-44bd2cd1322d)

Now overwrite the current design file (picorv32a.synthesis.v) with the new modified netlist which includes the replaced cells with larger drive strength using the below command and afterwards run floorplan and placement to update the correspondinf def files.

```
% write_verilog /home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/04-06_12-17/results/synthesis/picorv32a.synthesis.v
```

## Clock Tree Synthesis and Signal Integrity
A clock tree is a network of interconnections in the design that distributes the clock signal from a single source to various parts of the circuit. Furthermore, the variation in the arrival times of the clock signal at different points in the circuit is called as the clock skew. Minimizing clock skew is essential for maintaining synchronization and hence the main goal of clock tree synthesis.

To achieve this a clock tree topology is implemented called as H-tree structure or algorithm which connects the midpoints of paths between two element which in turn help reducing the skew.

Another problem which arises due to this is the signal integrity issue which is produced due to the wire resistance and capacitance of the clock net. To overcome this challenge we use repeaters or buffers over the clock network which amplifies the signal over long distances.

Below is the figure describing H-tree routing strategy-

![download (51)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/795aa527-ae8d-4d1a-9d37-544c6423533f)


You can see there is also a yellow line surrounding the clock nets. These are parts of clock net shielding. They are connected to either VDD or GND which doesnt switch. Basically what it does is, it protects the critical clock nets from crosstalk issues which is produced due to the changing/switching signal in the nearby nets.

Crosstalk is mainly due to the charging and discharging of the capacitors associated with the critical nets. It leads to issues like glitches and delta delay which in turn detoriates the original signal. Therfore it is necessary to create the clock net shielding to help avoid this phenomenon. The only downpoint with this is, it prevents crosstalk at the cost of more routing resources.

### CTS Using TritonCTS Tool
TritonCTS is an open-source tool designed for this clock tree synthesis purpose. In this section we have performed CTS for the previous synthesized deisgn with STA analysis.

Before diving into the flow we will look at the switches available for the CTS flow which can be found in  ```README.md``` file in the ```/openlane/configurations``` directory which can be used for optimization purpose if necessary.

![Screenshot (1705)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/3e191fb8-4c17-4247-9471-a58371c8722c)

PS : Moreover, we can also look for the TCL proc for each step like synthesis, FP, place, CTS in the ```openlane/scripts``` directory under ```tcl_commands``` folder. Basically proc is behind the steps code that gets executed when we run a specific program. For example we have taken an instance for the ```run_cts``` command which goes through a set of stages for checking necessary files and hands over the control to openROAD tool for execution.

![Screenshot (1709)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/c38cd7a9-699a-45c2-8b9d-7dd6eb329011)

Once these checkings are done we can move to perform CTS in openlane flow using our previous design. 

After the placement is done, run the ```% run_cts``` command in the terminal.

![Screenshot (1706)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/3ac6ed3a-faa8-4d98-a87e-52287b9abcdf)

As in this step the tool adds the extra buffers to the design in order to meet the timing constaints, a new design file ```picorv32a.synthesis_cts.v``` is created in the synthesis folder. From now on this new netlist will be used for the future purposes.

![Screenshot (1707_1)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/e3d62d2c-aa34-4364-a964-31e81121efee)

 As the new buffers are being added to the netlist, it will have impact to the skew values and hence the STA analysis needs to be done one more time for this real clock scenario. 

 This time we will analyze the timings through invoking the ```openROAD``` flow rather than invoking the openSTA outside of the original flow. OpenROAD includes the openSTA project which helps us perform the analysis. But for this we have to, like earlier, define library and constraints files as well as have to create a ".db" file by merging the .lef and .def file. We have created the file named as ```pico_cts.db``` for the same purpose and has executed it.

 In the first part we have created the db file -
 ```
% read_lef <PathOfDirectory>
% read_def <PathOfDirectory>

% write_db pico_cts.db
```
![Screenshot (1715)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/36edb643-5e18-4d2c-946f-bfe8f8ef5e7d)

In the next part we have read the db file along with new netlist, min/max libraries and sdc file and performed the report checks for timing analysis.
```
% read_db pico_cts.db
% read_verilog <directory>
% read_liberty -max <directory>
% read_liberty -min <directory>
% read_sdc <directory>
% set_propagated_clock [all_clocks]
% report_checks -path_delay min_max -format full_clock_expanded -digits 4
```
![Screenshot (1716)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/5b6027df-9603-4038-87cc-498b0fb6e507)

Now we can see the updated timing results and clearly the slack is met, which is a good sign for us. 

![Screenshot (1717)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/48094ee0-42da-442c-b974-a395343bc8ce)

The only problem with this analysis is that we have considered the slowest and fastest corner library and as of now the TritonCTS is not yet optimized for that corners as it is still in devlopment as an opensource tool. But it is quite optimized for the typical library and hence we will test it again by chnaging the library files to typical corners.

```
% read_liberty $::env(LIB_SYNTH_COMPLETE)
```
*The "LIB_SYNTH_COMPLETE" switch redirects to the typical library.

![Screenshot (1721)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/b3eee235-9eed-4c90-9fad-0aa7f9a71f96)

Now we see the slack value for the typical corner and it is met in the end.

![Screenshot (1722)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/e1a54454-11b2-490f-88e2-ec9b6dd6c7f5)

Now as part of the assessment, we have tried looking at the impact of bigger CTS buffer on setup and hold time by removing the lower drive strength buffer from the available buffer list and running the CTS.

Use the following command to check the buffer list-
```
% echo $::env(CTS_CLK_BUFFER_LIST)
```
To remove the specific buffer from the list, use the following command along with the index of the buffer as argument, twice, -
```
% set ::env(CTS_CLK_BUFFER_LIST) [lreplace $::env(CTS_CLK_BUFFER_LIST) 0 0 ]
```
Now run the CTS.

*One thing to note is that we have to change the def file to ```picorv32a.placement.def``` as we are performing CTS which is followed by placement stage.

![Screenshot (1725)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/c1aaba7c-a96c-40ce-a094-751f71b9c48d)

Now invoke the openROAD flow for STA analysis while repeating all the previous steps.

![Screenshot (1726)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/e4fc7dc9-3ccb-43d5-8c48-85fdf0f36481)

![Screenshot 2024-06-05 231728](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/03de81a6-3626-4c86-80f6-a6c93b6db3da)

Clearly slack value is reduced and also we have checked the setup and hold time for the same using the commands-
```
% report_clock_skew -hold
% report_clock_skew -setup
```
Note: To add the removed buffer again we can use the following command-
```
% set ::env(CTS_CLK_BUFFER_LIST) [linsert $::env(CTS_CLK_BUFFER_LIST) 0 sky130_fd_sc_hd_clkbuf_1]
```
![Screenshot (1729_1)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/972f8ac4-9e0f-4040-aaf0-501714fec2ff)

This ends the CTS process and in the next module we will move to the Power Distribution Network as well as routing stage, thereby completing the RTL2GDS flow. 


# Module-5 : Final Steps for RTL2GDS -Routing and Post-Route Timing Analysis 
So upto this module we have covered synthesis, floorplan, routing, placement and CTS with a partial static timing analysis. Now, we move to the routing phase, where we define the optimal path to connect two elements, aiming to use less routing resources with minimum timing violations due to its RC effects. We will use TritonRoute tool to perform the routing in our previous design, followed by timing violations check and improvement through openSTA. This will conclude our whole process of RTL2GDS of the RISC-V architecture processor.

## Routing
Routing involves the physical interconnection of cells and ports to drive signals and clocks. Our goal is to achieve the best possible route or path to connect the nets with less number of twist and turns in the path which will allow us to use less routing resources.  Specific routing algorithms are employed to determine the optimal paths by creating source and target terminals and iteratively finding the best route between them. One such algorithm is 'Lee's Maze Routing Algorithm'.

### Lee's Maze Algorithm
Lee's algorithm begins with pre-placed cells from the floorplan, which act as obstructions for routing. It creates a routing grid within the core area, defining source and target points. The algorithm labels adjacent grid boxes with increasing integers, representing the cost, until it reaches the target with minimal cost. Below is an example of such grid labeling from source to target - 

![download (52)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/9666f34b-7a4c-4e4c-bcef-7dc997f108df)

Clearly, we can see that it takes a minimum of 9 grid boxes to reach the target. Now, we have multiple paths to reach that point. However, as discussed earlier, there should be minimal zigzagging in the path due to lithography issues. Therefore, the algorithm traces the path with the fewest bends, as shown in the second figure. This concludes Lee's algorithm.

There are also other routing algorithms like Steiner tree and line search algorithm which follows the same principle of shortest routing path while using less memory and time than the Lee's algorithm.

### DRC Rules Associated with Routing
While doing the routing step we also have to consider the DRC violations due to the interconnect wires which is produced beacuse of lithography processes. Below we mentioned some of the DRC issues which needs to be taken care of :
- Wire Width
- Wire Pitch
- Wire Spacing
- Signal short
- Via Widths
- Via Spacings

![download (53)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/c1de4c8e-2570-4a88-895d-df03faf13225)

### Parasitic Extraction
Another crucial aspect of routing is the resistance and capacitance produced by the variable widths and heights of the connecting wires, which leads to delays in the circuit. To measure this delay, we perform parasitic extraction, which outputs the RC delay values in SPEF format. This process will be discussed in more detail during the lab assessments.

## Power Distribution Network
Before moving into the routing stage we have to deploy the power and ground straps for power delivery along the cells. Generally this is a step which is done just after the floorplan but due to the openLANE flow adjustment, this is deployed after the CTS step.

![download (54)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/d4008e86-fe07-4838-b471-1f132f07857c)

We can visualize through the above figure that the power and ground lines enter the core through the specific power/ground pads creating a ring structure around it. Later on these lines are distributed along the core using the straps (vertical lines). The standard cell rows and other macros (like memory cell) are then supplied power with the horizontal lines creating a mesh structure. A robust Power Distribution Network (PDN) is crucial for ensuring the reliable operation of an integrated circuit.

### PDN Through OpenLANE
As the CTS is now completed, the current def file will be used to produce the PDN network in the design. To run the pdn generation flow, use the command-
```
% gen_pdn
```
The gen_pdn  performs the following operations-
- writes lef
- reads def
- creates power and ground stripes

![Screenshot 2024-06-05 234815](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/a1ffa98e-e194-4d78-bd6d-b87e843b1f64)

![Screenshot 2024-06-05 235004](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/9fee1ef5-deb8-4f41-8844-318dd5e1e029)

Using the .def file produced we can visualize the power network in the Magic window.

![Screenshot 2024-06-06 001004](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/a63d36f1-fa2f-4cf7-a1a0-7f135eac9794)

## Routing Through TritonRoute
Basically the routing is divided into two sections-
- Global Routing : Rectangular coarse grid structures with routing guides generation
- Detail Routing : Uses the global routes to realize segments and vias, creating fine grid structures for connectivity between points (uses various algorithms)

The global routing is done by the "FastRoute" whereas the data is then being used by "TritonRoute" tool for the detailed routing.

TritonRoute is an open-source detail-routing tool that is part of the OpenROAD project. Detailed routing involves defining the precise paths for interconnections within an integrated circuit design, ensuring that all signals are correctly routed without violations.

TritonRoute has three features which enables it for routing -
- Routing within route guides 
  ![download (55)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/97dd72a0-6ef2-4812-af0f-fad064b74112)
- Inter-guide connectivity through access points
  ![download (56)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/53640cec-f6df-422e-980a-3213117536d2)
- MILP(Mixed Integer Linear Programming) based panel routing
  ![download (57)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/efeb17b9-6e5a-42ec-bdcc-2507645dbf10)

Following are the requirements for the execution of the tool as well as the produced output -
- Inputs to TritonRoute : LEF file, DEF file, Preprocessed route guide
- Constranits : Route guide honoring, Connectivity constraints, Design rules
- Outputs : Detailed route with optimized wire length and vias

Switches : We can see the available switches for the TritonRoute in the ```/openroad/configurations``` directory.

![Screenshot 2024-06-06 005501](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/44e57fb9-0653-4de1-a553-3efe2478978a)

Invoking the Tool : Now run the routing process after the pdn has been created. Use the command ``` run_route``` for the execution.

We can check the optimization factors of the routing through using the switches ```echo $::env(ROUTING_CORES)``` and ```echo $::env(ROUTING_OPT_ITERS)``` which will ensure the optimization factor as well as the memmory and runtime taken by the routing tool.

![Screenshot 2024-06-06 005837](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/2610119b-5bcd-42df-b59e-1debd4d94832)

Parasitic Extraction : One thing to notice in the run is that it automatically extracts the post-route parasitics in the design into a .spef file which can be found in the "results/routing/" directory. Parasitic extraction identifies the parasitic resistances and capacitances associated with the interconnections in the design, which is then used for accurate timing analysis, power analysis, and signal integrity verification.

![Screenshot (1736)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/68c055e6-5e65-4aa2-b680-b3820ffabc86)

After the execution, the final design layout can be viewed using the ```picorv32a.cts.def``` file present in the ```/runs/results/cts``` directory, through Magic.

![Screenshot 2024-06-06 011416](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/110198a7-6276-4f51-9a1f-9f40be6f9bd0)

![Screenshot 2024-06-06 011524](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/b36c94eb-bb1b-4107-94af-0b5626d13506)

![Screenshot 2024-06-06 011910](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/d314392e-d549-46a0-a169-1daea2703f15)

Finally we get the full layout of the RISC-V processor with all the necessary components and without any timing violations. This is the end of the PnR flow and hence we have successfully performed all the necessary steps and optimization techniques to make it feasible as a chip.

# Conclusion
Open-source tools are revolutionizing the semiconductor industry by providing widespread access to advanced design and verification technologies. This project showcased their impact by using OpenLane and SkyWater 130nm PDK for a complete RTL-to-GDSII flow in designing a custom RISC-V processor.

We began with the synthesis of picorv32 design producing a synthesized netlist. After synthesis, we proceeded with floorplanning and placement. Initial static timing analysis (STA) revealed slack violations, prompting several optimization steps, such as cell upsizing to improve drive strength and maintaining appropriate fanout levels.

A key aspect of this project was the design, characterization, and integration of a custom inverter cell. We extracted the LEF file for this cell and used it in the OpenLane flow, ensuring accurate representation in the new design. The characterization involved defining the cell's functionality and timing characteristics to meet the design specifications.

Clock Tree Synthesis (CTS) ensured efficient clock distribution, followed by detailed routing and parasitic extraction for accurate timing and power analysis. Library cell characterization ensured that each cell met performance specifications, and physical verifications, including DRC and LVS checks, confirmed the design's integrity. The final layout, verified using Magic, showed all components correctly placed and interconnected without timing violations.

This project demonstrated how open-source tools can effectively achieve an optimized, manufacturable RISC-V processor design, highlighting their significance in the semiconductor industry.

# Appendix
1. For more information visit https://github.com/efabless/openlane2
2. Tim Ansell - Skywater PDK: Fully open source manufacturable PDK for a 130nm process https://www.youtube.com/live/EczW2IWdnOM?si=lctS_oMLOfPLJU4v
3. Mohamed Shalan - OpenLane, A Digital ASIC Flow for SkyWater 130nm Open PDK https://www.youtube.com/live/Vhyv0eq_mLU?si=vtTc57Oq7si5CrXv
