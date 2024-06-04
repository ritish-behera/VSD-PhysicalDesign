# VSD- Physical Design Using OpenSource Tools
This repository details the report of all the technical works performed during the VSD- Physical Design workshop through OpenSource tools.
PDK Used : SKyWater 130nm
Date : 22nd May 2024
## Contents
1. Module-1 : Inception of OpenSOurce EDA Tools and Skywater 130 nm PDK
2. Module-2 : FloorPlanning, PowePlanning, Placement and Introduction to Library Cell Characterization
3. Module-3 : Design Library Cell Using MAGIC Layout and NGSPICE Characterization
4. Module-4 : Pre-layout Timing Analysis and Importance of Good Clock Tree 
5. Module-5 : Final Steps for RTL2GDS Using triton-ROUTE and openSTA

## Introduction
In the current scenario where using the commercial tools or software may feel expensive and difficult to master, the opensopurce EDA tools are trying to fill the gaps
to reach the mass of semiconducter enthusisat. Opensource tools are great way to learn the semiconducter fabrication flow starting from logic synthesis to chip tape out. Though as of now the optimizations of the tools and model libraries are not upto the mark as compared to commercial ones on performance basis, it is still useful for beginners and may turn out as a great deal in the future too.

  In this report, I have documented all my learnings as part of completion of "VSD-Physical design through opensource tools" workshop as well as tried to add a small contribution towards the community which I hope will be helpful for new learners.

## Objective
The following workshop involved the complete RTL to GDSII design flow of a RISC-V procesor architecture considering all the steps from logic or RTL synthesis, static timing analysis, DFT to floorplan, placement, CTS, routing, physical verification as well as sign-off of the chip through the openLANE ASIC design flow.Skywater 130nm PDK is used for all the model libraries.

![Screenshot (1586)Cropped](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/a3b1d2b0-53e9-4f7d-ad24-76d5da502d64)

The openLANE ASIC design flow involves the following opensource tools for different processes-
1. YOSYS & ABC : Logic Synthesis
2. OpenSTA : Static Timing Analysis
3. FAULT : DFT
4. OpenROAD : Physical Design (FP, PP, Placement, Routing)
5. MAGIC & NETGEN : Physical Verification


# Module-1 : Inception of OpenSOurce EDA Tools and Skywater 130 nm PDK
 This module gave the foundation of the chip that has to be designed as well as introduced the OpenLANE ASIC design flow and SKywater 130 nm openPDK.

 The chip contained 48 pins with Quad Flat No-Leads (QFN) die package carrying an area of 7mm x 7mm. The SoC consisted of a CPU with GPIO bank which has to be designed on RISC-V ISA. Apart from this it included various foundry IPs such as PLL, SRAM, ADC & DAC all connected to the IO pads through the wire bonds for the functioning of the chip.

![Screenshot (1574)Cropped](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/33608c68-87c2-43bd-96e5-c97809d62dad)

The first task of this module involved the initiation of the openLANE flow and hence the logic or RTL synthesis of the design through YOSYS and ABC. Besides a minor task of calculating the flop ratio of the synthesized design was added later.

![Screenshot from 2024-05-26 00-28-42](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/be2d9e99-fd10-4f57-87f0-ede688cbe389)

To invoke the tool the flow.tcl file was executed through tcl command in the terminal inside the working directory. It initiated the entire flow of RTL to GDSII without human interaction. The "-interactive" switch can be used to counter the execution of entire flow at once to step by step execution by the user.


![Screenshot from 2024-05-26 00-30-06](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/611c0011-a309-4f2c-a89c-ac9f6885acfc)

After the initialisation of the tool, to install the additional packages the "package require openlane 0.9" command was used. 

Before synthesis we have to go through design setup stage which creates all the directories for the excution og the speciic design as well as prepares the files. Here we are using "picorv32a" design for our synthesis which is available in the openlane work directory. For design prepartion "prep -design picorv32a" command was used.

Once the setup is ready we run the synthesis through "run_synthesis" command which generates synthesized design output and stores inside the picorv32a design directory.

![Screenshot from 2024-05-26 00-30-55](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/8e81f420-7c7e-413e-87f0-a1fd25551e72)

![Screenshot from 2024-05-26 00-34-56](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/e1583f55-2802-41e4-9e6d-b642848216a6)


After the synthesis, the files are stored inside the "results" and "reports" directory under "runs" folder of picorv32a design. All the timing reports and cell utilisation report can be found inside these directories.  

![Screenshot from 2024-05-26 00-36-14](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/43c7c8c1-7c3c-4e9a-b31b-e71aa96ef595)

![Screenshot from 2024-05-26 00-37-09](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/a5bc94db-8ecd-4e64-b775-ac2f6d04e939)

Flop ratio represents the ratio between total no of D-flipflops used to the total no of cells present in the synthesized design.

These data can be found inside the "yosys.stat.rpt" file (/runs/reports/yosys.stat.rpt) which assembles all the data of logic block used in the design.

The number of D-flipflops are represented by "sky_fd_sc_hd_dfxtp_2" and it was divided by the total number of cells in the design to get the flop-ratio.

Flop Ratio = 1613 / 14876 = 0.10842

This concludes the first module.


# Module-2 : FloorPlanning, PowePlanning, Placement and Introduction to Library Cell Characterization
After the synthesis stage comes the floorplanning stage where we arrange the pre-placed cells in the power rail and ground rail grid of the core followed by powerplanning, pin assignment and placement of standard cells. In this module we have executed the floorplanning stage in openLANE and viewed it layout results through the Magic tool. Also a minor task of representing the core dimeison in micron units was done as part of the module.

Afterwards, we have moved to powerplanning, pin assignment and placement steps and at the end briefed about the library cell characterization using the SPICE parameters, DRC rules and timing constraints.

## Floorplanning
Floorplanning typically invovles the ordering or arrangement of IPs which also could be referred as pre-placed cells in the power-ground mess of core of the chip.

![Screenshot (1590)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/d0c738bb-ec7c-4ee6-97e6-bdd9ab459577)

The core of chip is the portion where all the logic blocks are assembled or fabricated whereas the die suurounds the core as well as provides extra area for IO pin assignment. During floorplan stage in order to provide some space for routing and standard cell placement in further steps, we need to set some parameters for it which will be implemented by the tool during the execution.

The two essential parameters are 
1. Utilization Factor
2. Aspect Ratio

The utilization factor refers to the area occupied by the synthesized netlist to the total area of the core i.e. Utilizatiion Factor = Area Occupie by Netlist / Total Area of Core

A utilization factor of 1 which 100% represents that the total area of core is occupied leaving no space for extra logic. Therefore, in practical cases we consider 50-60% of core utilization to leave some space for extra buffers which helps in fixing timing violations as well as maintains signal integrity.

The aspect ratio represents the shape and size of the cell i.e it is the ratio between height and width of the cell. Aspect ratio = Height of Cell/Width of Cell

An aspect ratio of 1 refers to a square shape and rectangles for other values.

![Screenshot (1593)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/73ab5e44-f5ea-4a4b-b3f0-6459219c1d9d)

### Concept of Pre-placed cells
Basically macros are the pre-placed cells which we implement in floorplanning stage before placement and routing. Macros can be either large memories or special IPs which is implemented once and is repeated throughout the chip for further use with the same functionality. These are placed in user defined locations which is automated through place and route tool.

The macors actually go through a step called "Partition" where the large logic is granualirized into separate blocks of small logics with same functionality as a whole. This helps in floorplanning as the same netlist can be repeated multiple times to be placed at different locations.

![Screenshot (1595)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/09e11f6b-eeab-40a3-a067-c3b37b989667)

![Screenshot (1596)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/63f672e6-e9da-4fc4-9556-cd944846bf25)

### De-Cap Cells
Another type of cell which are placed during the floorplanning state is the de-cap cells or de-coupling capacitor cells. As the name suggests the extra capacitor integrated with the cells decouples the actual voltage source from the cell and act itself as the source during switching.

![Screenshot (1597)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/cde30678-0851-4d3c-9206-be0d75166420)

Basically, what happens when a cell is too far from the main source the switching signal gets detoriated before reaching the cells due to the resistive and inductive effect of the interconnects or wires. This leads to noise in the signal and affects the switching of the cells. In order to counter this we add extra capacitors parallel to the cell which charge itself to the same voltage as the source during non-switching activities and acts as a virtual source during the switching activities. These are implemented near the cells with high switching acitivities.

![Screenshot (1598)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/8ca1ea4c-7305-4bf9-9da9-a1c853faccbc)

The power issue of local cells or local communication is solved by de-cap cells but there still exists power issue in global communication that is between macros(with de-cap cells) which are connected together (Driver-Load Configuration) through wires and requires switching simultaneously. In order to solve this we use the powerplanning stage.

![Screenshot (1602)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/27a72e06-7053-4d04-bef7-c710bdad8726)

## Powerplanning
During simultaneous switching, the two main issues which arises in the global communications are-
1. Ground Bounce - Problem due to discharging of multiple cells through a single ground line and hence creating a voltage bump
2. Voltage Droop - problem due to demand of charging multiple cells at the same time through a single vdd line creating a voltage trough

![Screenshot (1600)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/398ffdcc-32db-46d1-8c33-225e00aa29e6)

![Screenshot (1601)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/0510bc8b-d41b-47c7-967c-8e808f1f222f)

In order to mitigate this problem we use a grid or mess of power and ground line running across the core parallelly to provide the required charge near to the cell. This step is done in the power planning stage.

![Screenshot (1603)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/e4dfe341-edbd-4152-b1f6-cdcac1e00c86)

![Screenshot (1604)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/8867808d-8d9b-4b6f-8000-7104ef7ac710)

### Pin Assignment and Logic Cell Blockage 
Apart from the core area, to connect the input and output nodes of the netlist to external environment, pins are assigned at the boundary of the die. This requires handshkaing between frontend and backend VLSI design as the input/output pins should be placed near to the input/output nodes of the logic which rersults in random distribution of pins around the die.

The pins include both data pins and clock pins with the clock pins slightly bigger in size to help reduce the path resistance which affects the drive strength of the clock.

Moreover, in order to not to place any logic blocks and routing channels in the pin area, logical cell blockage is used which reserves the pin locations.

![Screenshot (1606)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/374439ec-9ccc-4694-b803-429ed93ab221)

## Floorplanning through OpenLANE
In the previous steps, we have already invoked the openlane tool and synthesized the netlist into specific gate configurations. Now we will move forward to run the floorplan command in the terminal.

Before diving into the execution, we should be aware of the switches and the default configurations of the floorplan during the execution through openLANE. These can be found in the "openlane/configuration/" directory.

![configurationFoldr](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/09950797-7653-4aff-8005-50b83fe3dd6a)

To view the list of switches that can be used along the floorplan command, open the "README.md" file from the list.

![switchesFile](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/e937fc22-9ae5-440b-b7f8-f4fd40e3f45e)

The "floorplan.tcl" file includes all the default values for the run such as core utilization, no of horizontal metal lines, no of vertical metal lines in the grid and many more that can be seen in this figure. One more thing to note about the openLANE flow floorplanning is that the vmetal and hmetal is always one more than what is specified in the config file.

![FloorplanDefualtTCL](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/89640b4c-963a-498c-974a-373c2c64cac1)

Note: The floorplan follows a speicifc precedance order for parameters with respect to files having least priority to system defualts i.e. "configuration/floorplan.tcl" then "designs/picorv32a/config.tcl" and with highest priority to "designs/picorv32a/sky130A_sky130_fd_sc_hd_config.tcl" file.  

Now use the "run_floorplan" command in the openlane window to execute the synthesized netlist.

![floorplanCommand](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/89f7b882-ea76-4e79-9ac5-e2a0f25cfc1f)

Once the run is completed, all the reports and results are stored in the "openlane/designs/picorv32a/runs" directory.

Move to the "results/floorplan" directory to get the def (Design Exchange Format) file named "picorv32a.design.def" which provides guidelines for the distribution of power and ground connections implemented on the floorplan. The def file basically translates the logical designs into physically manufacturable layouts. In further steps we will see how to view this floorplan through "Magic" tool using the def file.

![floorplandefFile](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/0050f92e-bb03-4918-ada1-f2a6d1acb8fe)

Here the UNITS DISTANCE MICRON 1000 represents that 1 micron is equal to 1000 data base points.

And the dimesion of the core is given as follows ( 0 0 ) ( 660685 671405 ) where the first coordinate represents the bottom left corner of die and second coordinate represents top right corner of the die.

Hence, the dimension of the core in micron units can be calculated from the above values as -
1. Height = ( 671405 - 0 )/1000 = 671.405 microns
2. Width = ( 660685 - 0 )/1000 = 660.685 microns 

So, the area of die = 671.405 x 660.685 = 443587.212425 sq. microns

To get the data of core utilization and aspect ratio of the cells, open the "config.tcl" file located in the runs directory. Additionally this file gives more informationtion such as core margin, IO hmetal, IO vmetal, target density, endcap cell etc.

![configTCLfile](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/ea4b6642-f376-46f0-a669-6f1d3f9f5b6d)

Also check the "logs/floorplan" directory to get all the informations regarding the floorplan run. We can also compare the switch precedance that we earlier discussed through this directory as it stores the data of IO metal layers, vmetal and hmetal data of the specific run. You can verify the number of metal layers from the "ioPlacer.log" file after the run with the "config.tcl" before the run. 

![logListFile](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/22cc2578-0768-446e-8296-cddf79311b64)

Once all the data are verified now move to view the actual layout of the floorplan in the "Magic" tool window.

To invoke the magic tool within the current directory of floorplan use the following command to get the def file data plotted on Magic tool window-

![FloorplanCommand (1)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/733ef30c-3374-4977-a2f4-883830dfcb25)

![FP1](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/82ab4188-bb27-474e-af63-5d91ae9c5b04)

![FP2](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/194a2c80-1a11-4b63-9008-649919b2340e)

We can now verify all the data through the layout.

For example, as we have set the "IO PIN MODE = 1" it represents all the pins in the die are equidistant from each other which can be seen in the figure. Also as we have have mentioned the clock pins are slightly wider than the data pins so as to facilitate the drive strength.

To get the information about the utilization factor and metla layers, type "what" in the magic command window. 

![FP5](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/15810110-232f-48e6-afbe-ec88b1ee455e)

Also notice that, decap cells are present near the boundries whereas tap cells (which help avoid latchup conditions) are placed at the middle with diagonnaly equidistant from each other. Note that flloorplan doesnt take into consideration of standard cells but it can still be seen in the lower left corner of the layout which will be later on get placed in their respective position in the "Placement" stage.

![FP4](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/c87836b8-cb9b-4853-ba43-deee56263862)

Again, in the openLANE floe we can change the parameter of certain floorplan variable on the fly and run it again. For example we can change the pin placement strategy by providing the following switch in the openlane terminal-
```
set ::env(FP_IO_MODE) 2
2
% run_floorplan
```
This will produce the following floorplan with uneven pin assignment in the die.

![UnevenPin](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/5d2d4e4b-c928-4c44-b321-ae120e7ba3f3)



## Placement and Routing
Placement involves the arrangement of standard cells in their specific position on the power-ground grid of the core.

In this step we bind the netlist with physical cells having specific dimensions i.e. fixed height and varible width for standard cells. Moreover these cells are library specific which contains physical cells with variable dimensions, timing informations, venn condition etc. 

During the placement phase the physical cells are placed on the prvious floorplan which indeed gives the distibution of cells. The cells should be placed near to their respective input/output ports to help maintain the timing constraints.

### Optimization of Placement Using Estimated Wirelength and Capacitances
To start from the last point of the last section, we often face the challenge of not able to place the cells near to their respective IO ports. Sometimes the cells are far away from the port due to previous floorplans or other reasons which leads to more wiring cost. The more the wiring, the more resistance and capacitance effect it will have, leading to signal noise issues and greater slew values.

In order to fix these issues there are couple of ways which includes placing repeaters or buffers in between port and the cells. This help maintains the signal integrity and replicates the original signal at the cost of some area. Once the repeaters are placed, check the datapath at ideal condition of clock as well check the set up condition to verify the placement. 

![Screenshot (1609)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/fcd46fa0-7675-41c6-81a6-013e41ca6abb)

## Congestion Aware Placement using RePlAce
During the current step we have done only congestion driven placement leaving the timing driven placement for later. To start the flow run the command "run_placement" on the openlane window after the floorplan is done.

![Screenshot from 2024-05-28 10-11-32](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/9c1da2b7-6064-4b5b-a404-d1d1b725b037)

There are two section of the placement step-
1. Global Placement - Course grain placement with no legalisation
2. Detail Placement - Fine grain placement with legalisation

Note : Legalisation refers to the placement of standard cells in exact rows of the grid with no overlaps

For congestion driven placement, the goal is to reduce the wirelength and openlane tool uses Half Perimeter Wire Length (HOWL) method to define the location of the cells. The parameters for this method can be seen in the config.tcl file in the runs section. Again, one thing to note in the file is that the OVFL value that represents the overflow condition should converge during the simulation.

For viewing the placement layout in Magic we will follow the similar method as floorplan, invking the magic tools in the placement directory and hence putting the "picorv32a.placement.def" file as the input argument.

![place1](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/91a645ae-1aae-4cb4-a503-f46d1fdd5f35)

![place2](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/c85f0343-fa50-4adb-80df-26738c192bec)

![place3](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/7818c094-6238-4e35-aa24-dc0cce6b3a4e)


This ends the placement phase of the flow.

## Cell Design and Characterization Flow
Moving a bit apart from the flow in openLANE, we will discuss about the library cell characterization and respective design flow as cells are the basic block behind all the netlist,floorplan and placement. 

A library file contains all your standard cells (AND,OR,NOT,Buffer,latch,ICG) with different dimension and threshold voltage as well as various macros, IPs and decap cells, with timing information. The designing of a cell to add it to the library involves the following-
1. Inputs
   - Process Design Kits (PDKs) obtained from foundries
   - DRC, LVS Rule with lambda-based design rules
   - SPICE model which involves the parameter of PMOS and NMOS
   - User defined specs (cell height, cell width, supply voltage, metal layers, drwan gate length etc)

2. Design Steps
   - Circuit Design
   - Layout Design
   - Characterization

3. Outputs
   - CDL (Circuit Description Language)
   - GDSII, LEF, Extractwd SPICE netlist (.cir)
   - Timing, Noise, power (.libs)   
   
### Characterization Flow
Characterization of a cell is the most important aspect of a library cell generation which includes timing, power and noise analysisof the cell. The characterization typically involves the following steps-
- Reading the SPICE models (pmos & nmos model files)
- Reading extracted SPICE netlist (RC values)
- Recognizing the behaviou of the circuit and reading its subcircuit
- Attaching the necessary power and ground
- Applying the stimulus
- Providing necessary output capacitance to measure the values of charge, current and delays
- Providing simulation commands like tran and dc
At last all these data output is fed to the "GUNA" characterization tool which outputs the config file.

The following figures define some of the timing characterization values of cells which helps calculating propagation delay and transition time.

![Screenshot (1615)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/291f7017-94f4-4e71-93b8-67b0bb920e0f)

Propagation delay = Out_time_threshold - In_time_threshold

- Propagation delay is calculated by the difference of output and input time at 50% point of both the waveforms.

Transition Time = (slew_high_rise - slew_low_rise) or (slew_high_fall - slew_low_fall)

- Transition time is calculated by the difference of time between 80% & 20% point of the same waveform. 

![Screenshot (1617)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/cec2a8af-c86e-4009-8652-05d9a53af350)
  
![Screenshot (1622)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/8a5d0f17-39e3-4001-9afa-0289bc704c85)



# Module-3 : Design Library Cell Using MAGIC Layout and NGSPICE Characterization
In this module we will discuss about the library cell characterization and layout generation through "Magic" tool with emphasis on fabrication steps of a 16 mask CMOS inverter to better understand the design rules of the layout. Further as part of the cell characterization we will anylyze the timings i.e. rise, fall and propagation delay of the inverter through Magic and later on we will import this complete design to our library "picrov32a" generating the lef files.

## SPICE Deck Creation for CMOS Inverter
SPICE is a powerful, general-purpose analog electronic circuit simulator that is used to verify the integrity of circuit designs and predict circuit behavior. The circuit is described in a SPICE netlist, which includes component values and connectivity. Further, accurate models for the components (transistors, diodes, etc.) are provided, often based on process technology data and analysis types (transient, AC, DC) are specified along with simulation parameters.T he output results are then analyzed using graphical tools to verify the circuit's performance.

Here in this report we will discuss about a CMOS inverter and hence in the first step we will create a SPICE deck or netlist for the inverter circuit.

![Screenshot (1623)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/8cd27fb4-8426-43f0-bc68-2dbb078b9af2)

SPICE deck program :
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

The robustness of a CMOS inverter can be studied through its static behaviour which is a reason of its wide use. 

The main characteristic of a VTC which defines the specfic inverter is the switching thershold or trip point which is nothing but the point where both input and output has the same voltage value. At this point both pmos and nmos are at satuaration region leading to huge amount of current flow. The switching threshold condition can be given as 
1. Vgs = Vds    i.e. Gate-source voltage is equal to drain-source voltage
2. Idsp = -Idsn    i.e. the summation of drain current of pmos and nmos is always zero

This switching threshold is heavily dependent upon the transistor sizing of pmos and nmos which is nothing but the W/L ratio of the transistor. The switching threshold is calcculated by drawing a 45 degree angled line from origin and the point at which it intersects with the curve is taken as the trip point.

![Screenshot (1629)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/9aac8057-4b72-4618-b116-a31ba7eb9134)

The output VTC of inverter having higher W/L ratio of pmos than nmos is shifted towards the right from the ideal position whereas inverter having higher W/L ratio of nmos than pmos is shifted towards the left. An example is given below which depicts the behaviour of the curve depending upon the W/L ratio shifting the switching threshold. 
 
![Screenshot (1628)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/2482a7fc-2354-4176-b817-0e17303aa16e)

### Dyanamic Simulation of CMOS Inverter 
Dynamic simulation of a CMOS inverter refers to the analysis of its performance under varying input signals over time. This type of simulation helps in understanding the transient behavior and timing characteristics of the inverter, which are crucial for characterization of cells. We will use this transient analysis to calculate the timing characteristics i.e. rise time, fall time & propagation delay of the inverter through SPICE simulations.

![Screenshot (1630)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/9a78550d-eb2a-49c8-9369-734022816d26)

To perform the transient analysis we have to make the following changes in the previous program :
```
vin in 0 0 pulse 0 2.5 0 10p 10p 1n 2n                               // ModelName Node dcValue0 dcValue1 Type ZeroValue OneValue Shift RiseDelay FallDelay PulseWidth Period

** Simulation Commands **
.op
.tran 10p 4n
```
This will produce time dependent waveform from which we can calculate the delay values of the inverter by setting the load capacitances. The following setup is carried out in NGSpice in later stages.

## Inception of Layout and 16 Mask CMOS Fabrication Process
The 16-mask CMOS process is a specific technology where 16 photolithographic masks are employed during the fabrication. the mask is an opaque plate which blocks the UV light to react with certain areas of
photo-resist. Part of resist is exposed to UV light, which gets washed away giving us the open areas for further process steps. We will brief about these fabrication steps to help understand the depth of design rules during the layout generation of the cell. 

### Selecting a Substrate
We start with choosing a substrate as a base for fabrication. In general a lightly doped (~10^15 cm-3) P-type silicon substrate with high resistivity (5 ~ 50 ohm) is selected.

![Screenshot (1631)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/8594a9be-191c-475b-9ecf-c342d85b1634)

### Creating Active Region for Transistor
An isoltaion over active area is created by growing a thin layer of (~40nm) silicon oxide (SiO2) followed by a layer of (~80nm) of silicon nitride (Si3N4) and photoresist (~1um) for photolithography process ( Action between UV light and photoresist). Then the hard mask is placed over the photoresist help avoid the area which will be etched out. This mask is reffered to as the mask-1 .

![Screenshot (1632)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/2178dd01-942c-4964-bbf3-697f102ec6ed)

Once this is done we expose it to UV light and use the developing solution to dissolve and remove the exposed region. Then remove the mask and etch off Si3N4 followed by removing the phototresist which was below the mask. To create an isolation between the two active rgions where pmos and nmos will be fabricated, we put it again in a oxidation furnace which grows a second layer of SiO2 in the gaps .This process is called as "LOCOS" or "Local Oxidation of Silicon" and the structure of oxide is referred as "bird's beak". Finally the remaing Si3N4 is etched out to get only the oxidised layer on the top.

![Screenshot (1633)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/98dbf3c4-1bff-49ef-aadc-4eef7d8caf46)

### N-Well and P-Well Formation
Once the isolation is done between active regions, we again grow the photoresist layer over it and put the mask-2 at the other region where we want create the well.Expose it to the UV light to remove the exposed area and take out the mask. Now through ion implantation process we diffuse boron into the substrate at 200Kev to create the p-well.  

![Screenshot (1634)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/d6924f38-bb5a-4e74-b1a3-93fd0021c3a3)

Similarly, to create the n-well we use the photoresist and the 3rd mask followed by diffusion of phosphorous into substrate at 400KeV. This require more enrgy as phosphorus is a heavy metal.

![Screenshot (1635)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/285f4741-f783-4a0c-8d91-4db1baa0946b)

Later to improve the depth of diffusion of the wells, the wafer undergoes a high-temperature annealing process in a furnace. This step is known as "drive-in diffusion". The high temperature causes the dopant atoms to move from areas of high concentration (surface) into the silicon lattice, spreading out to create a uniform dopant distribution at the desired depth. This whole process is also known as "Twin Tub Process" as we create both p-well and n-well.

![Screenshot (1636)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/6d4e524f-4599-43e6-bb43-edf9c8bdd37b)

### Formation of Gate Terminal
The threshold voltage Vt of a device is highly dependent upon the doping concentraion of p and n region as well as oxide capacitance. In order the control both, before gate formation we modify the doping concentraion of p-well by depositing photoresist and creating mask-4 followed by boron diffusion at low energy (~60KeV). This facilitates the desired concentration of p-region.

![Screenshot (1661)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/6397ca1c-c842-4716-8149-162db25cd9f5)

![Screenshot (1662)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/85eefe9a-e52c-4459-915c-7f9fb8c66a99)

Similar steps are taken for the n-region creating the mask-5 and diffusing arsenic afterwards at a low temperature.

Again to control the oxide capacitance we etch off the damaged silicon oxide (because of repeated ion implantation) by dissolving it into HF solution followed by growing a thin layer (~10nm) of high quality SiO2 which controls the threshold voltage Vt.

Now for gate formation, we use a polysilicon layer(0.4um) over it and then the photoresist and mask-6 (also referred as polysilicon mask) for the pattern generation. In most cases, bfore etching out the photoresist, we dope it with n-type ion implant for low gate resistance of the device.

![Screenshot (1663)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/c118d51f-f744-47c7-9145-23a43831e74d)

Once the UV exposer and removal of photoresist is done, we get the following structure of the gate- 

![Screenshot (1664)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/3d4c9b72-a25e-4537-a4ee-fa64785147bd)

### Lightly Doped Drain (LDD) Formation
We want to achieve a doping profile of P+,P-,N in nmos and N+,N-,P in pmos region. The P+ & N+ represent the source drain doping whereas N and P rpresnt the substrate dopings. The profile P- & P+ represents the lightly doped drain in the well. So basically there are two reasons for this extra LDD formation -
1. Hot Electroon Effect : When device size is reduced, the elctric field provided by the power supply becomes stronger leading to breakage of Si-Si bonds by the high energy carriers as well as crosses the 3.2eV barrier between the Si and SiO2 conduction band and hence penetrating into the oxide.
2. Short Channel Effect : Due to the reduced size of the device, the drain field penetrates to the channel leading to effects like channel length modulation, velocity saturation and DIBL.

Therefore in order to avoid these phenomenon we use the LDD formation technique.

It has similar process of photoresist deposition and mask-7 creation as well as phosphorous (N-) diffusion for nmos. For pmos we use the mask-8 with diffusion of boron (P-) for LDD formation.

![Screenshot (1665)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/7ea394cd-8df7-47d5-ae0e-085530ed8caa)

![Screenshot (1640)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/fb02d80b-5aa4-4c07-9762-770d5f0b6fff)


In order to protect the LDD in later processes of formation of source and drain with N+ or P+ dopings, we need to create the side-wall spacers. A thick layer (~0.1um) of Si3N4 or SiO2 is deposited over the surface and hence  plasma anisotropic etching is carried out which forms the side-wall spacers around the gates which protect from the exposer of diffusion into LDD.

![Screenshot (1639)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/a6af7172-c044-46e2-9841-5fcc944b2d39)

![Screenshot (1638)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/440d821d-0a82-40c4-a03f-cd0be4cb85c5)

### Source Drain Formation 
Before the source and drain formation, a thin layer of screen oxide is deposited over the surface in order to avoid channeling during the implantation.

Now in similar fashion we deposit the photoresist and mask-9 above it and implant the arsenic (N+) on the exposed area with a energy of 75KeV. This creates the source and drain region for the nmos transistor. Similarly for pmos source drain we use the mask-10 and boron (P+) as implant for it with 50KeV energy. 

![Screenshot (1641)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/bdc7e948-214a-41b7-80d6-5e43c26953c2)

Afterwardss, to increase the penetration depth of the diffused implants we put it in a high temperature furnace for the annealing process.

### Contacts and Local Interconnect Formation
Contacts are the only thing that can be accessed by the user to control the pmos and nmos.

First of all remove the the thin screen oxide by dissolving it in HF solution. Now deposit the titanium on the wafer by the process of sputtering which acts as a low resistant contact. Further the wafer is heated at about 650-700 degree centigrade in N2 ambient condition to form the local contact TiSi2. Again due to this another material TiN is formed which is used for the local communication interconnect.

![Screenshot (1643)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/02b3a044-0274-46a3-8af3-5720991e24df)

![Screenshot (1644)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/b10d1a74-c1b7-4e5e-8daf-d4ec4d6466fb)


So now to bring the bottom connection up, we will use the layer of photoresist and mask-11 exposing the non-contact areas. Now the excess TiN is etched out using RCA cleaning which is nothing but a combination of deionized H2O, NH4OH and H2O2 solutions and hence the TiN contacts remains.

![Screenshot (1645)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/ca1a4794-4983-45dc-94e4-56d6c679f5be)

![Screenshot (1647)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/8cd364ff-a2a8-40b1-9256-a02bb393f144)

### Higher Level Metal Formation
To avoid the non-linear surface topography of the wafer we need to planarize it. To achieve this deposit a thick layer (~1um) of SiO2 doped with phosphorous or boron (known as phosphosilicate or borophospho-silicat) which act as a protective layer for the mobile Na ion as well as boron reduces the overall temperature. Now the wafer surface is polished using a process called CMP (Chemical Mechanical Polishing) and outputs something similar the fgure below- 

![Screenshot (1649)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/3a3ef481-f7db-47da-9f39-e302ba5846a9)

The next step is to drilling the contacts into it by the same photolithography technique using the mask-12. 

![Screenshot (1650)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/d2b34eaa-2780-46eb-a5fe-7436cc1e5a82)

Now remove the mask and etch out the SiO2 to drill the contact holes making it accessible to upper layers and use a thin layer (~10nm) of TiN which is very good adhesion material for TiSi2. The next step is to deposit a blanket tungsten layer for the contact and carry out the CMP process for planarizing it.

![Screenshot (1652)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/d1d729f1-b916-4e56-9f0d-808259f4c457)

Now to bring the contacts outside of the chip we deposit a aluminium layer (which is a higher metal layer) and pattern it through photolithography to take it to top layer. Now use the same method of SiO2 depostion followed by CMP and tugsten-aluminium layers while using the masks13-15 to take it outside of the chip. Nowadays instead of aluminium, copper is used as the alternative for higher metal layers due to its low resistivity.

![Screenshot (1654)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/b2de6856-cd8c-494f-96cb-53b199257e10)

![Screenshot (1655)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/6a239c8e-443e-4985-a57a-00579424ec92)

At the end, in order to protect the chip a Si3N4 layer is deposited on the top metal layer wchich uses the final mask-16.

![Screenshot (1656)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/b7de523e-cd71-407d-8016-7409796b5878)

This ends the total process of fabricating a 16 mask CMOS inverter with source , drain, gate output terminals.

![Screenshot (1657)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/435e0439-3fbe-4ff8-a67a-96ef4d2b17bb)

## Layout and SPICE Parameter Extraction Through "Magic"
For reference we have taken a pre-built cmos inverter cloning it from the github repo - https://github.com/nickson-jose/vsdstdcelldesign

After cloning it to /openlane directory we can see several files contained in it.  Our point of interest is the "sky130_inv.mag" file which stores the layout information. Invoke the Magic tool through the tech file and pass "sky130inv.mag" as the argument. It will show the layout in the magic window.

![layoutInvoking](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/e37f273e-c399-4e7b-b24b-0786ebf4a06a)

![InverterLayout](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/1b9c9d3d-8cdf-4fbf-8e89-e6468f13bc30)

We can check drc errors in the layout through the "DRC" tab and from the drop down list, select the "DRC Find Next Error" and it will lead to the area of the error as well as list down the errrors in command line. We will discuss about the design rules in Magic in later sections. 

![drc](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/4b4f8a74-bca6-41f1-aedf-fae99a89b188)

Now to extract the whole layout use the following command in the command line of Magic-
```
extract all
```
Now for the extraction of netlist with parasitics that will be used in NGSpice we will use the following commands-
```
ext2spice cthresh 0 rthresh 0
ext2spice
```
![ExtractionCommands](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/6aacae3f-0688-406e-987f-a4fd0e33aca1)

After execxuting these codes we will get the following extracted files in our directory-

![OutputExtFile](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/15df62b3-1410-48db-a772-d51a31351b06)

## SPICE Deck Creation and Timing Characterization of the Inverter
After extracting the SPICE netlist from the layout we modified it as following for the transient analysis and created the final SPICE deck for the inverter.

![Screenshot 2024-06-01 055227](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/44711b2b-fee4-4c26-bc1a-bdebbe5d9c32)

Then we simulated it through NGSPICE to obtains it output waveforms as well as to calculate timing characteristics.

![Screenshot 2024-06-01 054747](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/586fefab-8ac7-4419-bbad-fd08b0d1c02f)

![Screenshot 2024-06-01 054706](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/00f711ad-3419-48d5-b5b4-4db6c3380b46)

Now the rise and fall delay of the output waveform was calculated at 20% (0.66) and 80% (2.64) values as well as the rise and fall propagation delay was calculated at 50% (1.65) values of both input and output.

![Screenshot 2024-06-01 054637](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/86847f87-2733-4b81-9888-cb22eb5f5a0f)

- Rise Delay = 2.23949e-09 - 2.17978e-09 = 0.05971ns
- Fall Delay = 4.09326e-09 - 4.05065e-09 = 0.04261ns
- Rise Propagation Delay = 2.20701e-09 - 2.14989e-09 = 0.05712ns
- Fall Propagation Delay = 4.07529e-09 - 4.05057e-09 = 0.02472ns

This values sums up the timing characterization of the standard library cell at a nominal temperature of 27 degree centigrade.

## Magic Tool Options and DRC Rules
In this section we will go through the DRC rules for the open source pdks. The following figure represents the process stack diagram for the SkyWater 130nm process node. Further for more detailed information on the rules set visit https://skywater-pdk.readthedocs.io/en/main/rules/periphery.html#rules-periphery--page-root .

![image](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/114ed88f-5354-4d8a-b2bb-973727b4e12b)

To check and verify some of the rules as part of the exercise, we have sourced files from the website - http://opencircuitdesign.com/open_pdks/archive/drc_tests.tgz which contains various layout models in magic.

![image](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/6ea40030-172e-4512-a845-00daa54cd01c)

![image](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/eccb26cc-ebff-4221-a583-1b87af682b2d)

The .mag files gives the layout files whereas the .magicrc tells the magic tool where to find the tech file.

As the first exercise for resolving the drc error we have taken an example of poly and nres poly spacing which is in the poly.mag file. As per the rule in the documentation the poly to poly distance should be at least 0.480 microns. But it doesnt consider the poly to nres poly distance . So our aim in this exercise was to add the rule to the tech file "sky130A.tech". 

We first checked the distance between the poly and nres poly through creating a box and viewing its height on the terminal. Clearly the space is 0.210 microns which is less than the desired space.

![Screenshot 2024-06-01 105944](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/c453a061-daa9-4d0c-b98c-c7c0ca3d407a)

Now to resolve this issue we added the following rules set to poly.9 which will also define the rules for poly and nres poly spacings.

![Screenshot 2024-06-01 110505](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/79fec960-c969-43a0-bb45-22a6e7582e16)

Now we will load the updated tech file to Magic through magic command line as well as will check the drc error is fixed or not. The following figure verifies that the issue is solved as there is no drc error found.

![Screenshot 2024-06-01 110905](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/13321c51-9a81-4f59-a36c-322c8c9a7b8f)

In the next exercise we have tried adding the drc rule for spacings between nres poly and diffusion. In this case we are not checking the diffusion rules, so that can be ignored for now. In the follwing example there exists errors for the spacing between poly and diffusion but no such error can be seen for the npoly res and diffusion.

![Screenshot 2024-06-01 152029](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/c3ff0faf-6460-45bf-8627-fc0c0b9d9d97)

In order to add the rule as per the documentation we have added the following error constrcut in the tech file which serves the error for all diffusion spacings.

![Screenshot 2024-06-01 152507](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/17d63e20-3b5d-41cc-bc71-919de16b00a5)

Now after going through similar process of loading the tech file again and checking the drc, the errors can be seen (Highlighted by yellow circles in the figure).

![Screenshot 2024-06-01 152935](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/f6ab5552-9047-45c3-a883-f285fa956c65)


In the next few exercise we have dealt geometrical construct of drc rules and missing rules through the n-well structures defined in nwell.mag file. The below figure shows the part of the documentation which contains the drc rules for the n-well in sky130 nm pdk.

![Screenshot 2024-06-01 154922](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/7a7fda54-80c5-47d6-83bd-1d7f84abe97d)

To describe the drc error in geometrical constructs we have taken an example of dnwell placed inside the nwell. As per the rule nwell.5 and nwell.6, the outer and inner layer of the dnwell must be surrounded by layers of nwell with minimum widths of 0.4um and 1.03um respectively. 

To describe the errors in geometrical construct we have to invoke the command "cif ostyle drc" in the Magic terminal. We have taken an example of inner layer error of dnwell which can be seen through the command "cif see dnwell_shrink". (Refer to the figure)

![Screenshot 2024-06-01 162028](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/90d4c0b9-3dd6-45f4-8233-c1db4eb14274)

Moving towards the next exercise, we have identified some missing or incorrect drc rules in the n-well layout and fixed that according to the documentation. In the figure below which we have taken as an example, there exists no error in the n-well structure as per the current rule set of the tech file.

![Screenshot 2024-06-01 163826](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/401b3bf1-3096-4f18-89d0-a8e193efca5a)

But according to the nwell.4 rule, all n-wells should contain metal-contacted tap inside it. Therefore to fix this missing rule we have to modify the tech file by adding these constructs -

![image](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/9f3da849-98da-452e-b9a4-7a95f6a4cc38)

PS: the bloat-all operator has set of all n-well that contains tap. What we have done is, we have taken the set of all n-well and removed all the set of n-well containing taps from that. Whatever now is leftover, is taken as an error.

Afterwards, we have defined the error in the "N-well" section as full variants, which means that this error will be showed whenever we run the drc in 'full' style.

![Screenshot 2024-06-01 174222](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/108a6f0a-c4b8-4cc5-814d-a21decdfd2a1)

Now we will check our updated tech file by loading it again and will run the drc check. As can be seen in the figure the n-well without the tap shows an drc error whereas the n-well with tap, at the right side, doesnt show any errors. Hence we have fixed the missing drc rule.

![Screenshot 2024-06-01 183728](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/f3e24948-d0b7-43fa-9c5a-6bfa245f2b9b)


# Module-4 : Pre-layout Timing Analysis and Importance of Good Clock Tree 
Till this point we discussed about the extraction of the layout of a standard cell as well as calculated the timing characterization values for the cell. Our next goal is to plug in this newly designed standard cell to our working design "picorv32a" and check its functionality. In this module we will be focusing on how to import the data associated with the cell to the design library in form of LEF as well as make it ready for PnR stages.

Openlane is just a place and route tool and hence heavily dependent upon the LEF file. A LEF file provides information about the cell's power & ground rails, inner PR boundary and input/output port information. But the layout which we have extracted (.mag) contains extra informations like power, ground and logic implemented which is of no significant use in PnR. This is what makes extracting LEF file more important. 

## Synthesis and Timing Analysis of New Std Cell
### Conversion of Grid Info to Track Info
Tracks are basically the paths through which the routing takes place. It connects the outside IO pins to cell's IO nodes. To get the track information of the sky130 pdk move to the directory "openlane/pdks/sky130A/libs.tech/openlane/sky130_fd_sc_hd/" and open the "tracks.info file".

PS : The first term in a track info represents the track layer followed by horizontal (X) or vertical (Y) direction followed by offset value followed by pitch i.e. [Tracklayer Horizontal/Vertical Offset Pitch]

![Screenshot 2024-06-01 221016](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/a03c81cf-6133-4c00-a8b0-389ddf5920d2)

The PnR tool follows some specific guidelines to execute its automated process. It includes -
- The IO port must lie on the intersection of vertical and horizontal tracks
- Widths of standard cells should be odd multiple of track pitch
- Height should be odd multiple of vertical track pitch

So in order to check the guideline that our designed inverter's IO ports lie on the intersection of vertical or horizontal tracks or not, we changed the grid dimension of layout in magic as per the layer1 tracks (As the IO ports are on layer1) and verified its correctness. Refer to the figure for the intersection point at input A and output Y.

![Screenshot 2024-06-01 221306](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/1aa099d7-e95d-4859-bab1-192b5661d96e)

![Screenshot 2024-06-01 221246](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/d2470e14-5d89-4116-9af0-cf06fca054ac)

The 2nd and guidelines of the PnR tool can also be verified through the grid as the the grid dimesion refers to the pitch and the inner PR boundar contains 3 horizontal box and 7 vertical box. So our design is completely compatible for the PnR tool.

### Conversion of Magic Layout to Standard Cell LEF
The first step for this would be the conversion of all the labels of inverter into specific ports. This can be done through the "Edit" tab and then selecting the "Text" option. 

Now set the port name (Text String), size, layer attached and enable the port for all the labels. The enable number should be given as per you want it on the LEF file.

![Screenshot 2024-06-01 231223](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/bb1f55e1-55ad-4470-9879-ba281f2190cb)

![Screenshot 2024-06-01 231102](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/874088d4-1399-4134-ae98-0a171b0ff015)

![Screenshot 2024-06-01 231017](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/7605ec7d-d5b6-4bcc-b7e8-d4d16e193741)

![Screenshot 2024-06-01 231307](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/d6fa89f8-73cd-4bbf-b659-34b3e9a01a13)

Once this is done we have to define the port class (input/output/inout) and port use attributes (power/ground/signal) for every port in the magic command terminal and save it with a custom name for further use.

![Screenshot 2024-06-01 232151](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/8831babb-82da-4cc0-be24-bdfd5cb3b081)

Now open the newly saved .mag file in magic and use the command "lef write" to generate the LEF file in the current directory.

![Screenshot 2024-06-01 232744](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/9ed3e163-5842-41c8-a41e-cee47ce1b66f)

Now open the .lef file to see all the contents as we have assigned before. See the figure to verify all the properties.

![Screenshot 2024-06-01 232841](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/1c6f10d1-deb7-4363-aa9a-39c81ecfe6c8)

### Additon of New Std Cell to Library and Its Synthesis
For adding the standard cell to "picorv32" design directory copy the lef file of the cell to the source directory of the design library (openlane/designs/picorv32a/src/).

![Screenshot 2024-06-02 083748](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/541d2585-0aad-4e08-8c9e-cbba1d6226b9)

Now again repeat the steps for the technology library (typical, fast and slow corner) files from the "libs" directory of "vsdstdcelldesign" to the src folder of picorv32a. The library files contain the information of cells in different PVT corners such as tt,ff & ss .

![Screenshot 2024-06-02 092935](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/0c294637-6946-47e2-8826-ca55f46395a2)

![Screenshot 2024-06-02 092159](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/2d55b65f-96e3-4a21-b1ba-4c07e2d4435d)

Once this is done modify the "config.tcl" of the picorv32a design by adding these paths or switches for the run -
```
set ::env(LIB_SYNTH) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__typical.lib"
set ::env(LIB_FASTEST) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__fast.lib"
set ::env(LIB_SLOWEST) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__slow.lib"
set ::env(LIB_TYPICAL) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__typical.lib"

set ::env(EXTRA_LEF) [glob $::env(OPENLANE_ROOT)/designs/$::env(DESIGN_NAME)/src/*.lef]

```
![Screenshot 2024-06-02 095616](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/8b6e8cba-2165-48dc-8c37-2c0b53d40f05)

The LIB_SYNTH switch is used for the synthesis whereas LIB_FASTEST, LIB_SLOWEST & LIB_TYPICAL switches are used for STA analysis. The "EXTRA_LEF" switch adds the extra lef file for the process.

Moving on, invoke the openLANE tool and add these two command lines to consider the new LEF file in the tool and then run the synthesis.
```
set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
add_lefs -src $lefs
```
![Screenshot 2024-06-02 114950](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/60ce01e1-21ea-415b-b3d1-3bf04e57dcde)

![Screenshot 2024-06-02 115654](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/b1757779-fe35-4577-9141-8f569728b986)


Our synthesis is successful with adding the new standard cells. Now we will move the timing aspects of the cells and its effects.

You can see at the end of the synthesis there are two parameters tns and wns are present. These represents the Total Negative Slack and Worst Negative Slack respectively. Slack is the difference of required time and arrival time for a cell. In order to reduce these timing violations we can take certain measures like delay and area balance, which means that we will improve the delay at the cost of area. 

As discussed earlier, in the openLANE flow there exists some switches for every procedure. So we can try to implement it on the terminal to change the variables taken for area and anything that realtes to delays in the circuit.

![image](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/e4c43a2e-1978-4d57-ae86-98627ff2b524)

We can play around the following switches to reduce the slack but as of now our concern is to add the custom cell to library we will be focusing on this on static timing analysis part.

![Screenshot 2024-06-03 082923](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/ea667c32-655a-445f-8310-f9d5b64733ed)

We can check that whether our custom inverter cell information is added or not by checking the "merged.lef" file in the "tmp" directory.

![Screenshot 2024-06-03 084022](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/a5ee9383-6920-4369-abdd-4d7c7e161efb)

Now run the floorplan followed by placement to see the results. Open the magic tool to view the placement statistics.

![image](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/1e9d7d57-d875-4069-a2ea-b1c541242854)

![WhatsApp Image 2024-06-03 at 9 03 21 AM](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/30e88e96-fd4d-4d78-bba7-40306d10ac4c)

We can see our custom cell (sky130_vsdInv) being placed on the figure with its layout.

This indicates that we are successfull with placing our standarrd cell in the design and now can move to further otimizations. 



### Introduction to Delay Table
Clock Tree Synthesis (CTS) is primarily aimed at distributing the clock signal from a single source to all the sequential elements (flip-flops and registers) in a chip. The goal of CTS is to ensure that the clock signal reaches all these elements with minimal skew and jitter, ensuring synchronized operation. For this purpose we use buffers in the path of clock nets. This in turn requires more power. So to reduce power consumption in CTS (also called as Power Aware CTS) we use techniques like clock blocking through AND gates and OR gates which turns on or off with an enable signal from the user. This is called as clock gating.

Before adding the gates we need to evaluate the timing characteristics of the buffers of clock path. For exampl, for the calculation of timing of the belowe figure we have taken the following assumption-
- c1 = c2 = c3 = c4 = 25fF
- cbuf1 = cbuf2 = 30fF
Therefore, total cap at node-A = 60fF, node-B = 50fF, node-C = 50fF

Moreover, the observations can be listed as -
- 2 Levels of buffers are present in the diagram
- AT every level, each node is driving same load
- Identical buffer at same level
Note that the load capacitance of buffers are not equal to the output capacitances. Output capacitances are varying, hence it produces a variety of delays. So in order to create timing models a table is created between varying input slew (from 10-100ps) to the varying ouput capacitances (from 10-100fF) and the respective delay values are noted. This is called as the delay table which characterizes the delays of the buffers.

![Screenshot (1689)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/a0e5651a-de2e-417e-be15-8348709b9c03)

From this delay table we can estimate the delays at each node and hence will be able to calculate the skew (The difference of arrival time of clock pulse between two FF/registers) produced at he end. It plays a crucial role during the static timing analysis as it may violate the setup and hold times.

## Timing Analysis With Ideal Clocks Using OpenSTA
### Setup Time And Intro to FF Setup Time
A flipflop is a basic sequential element which stores or pass data depending upon the clock edge. It consists of two MUX structures to perform the data storing activity according to the clock pulses. This in turn produces some delay before passing the data to output. This specific delay affects the clock period of the circuit containg it which is known as set up time.

![Screenshot (1691)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/9f448f0e-6da5-40fc-83fa-9eef59f981c7)

In an ideal clock scenario where no clock tree or buffers are added, the combinational delay between launch and capture flipflop should be less than the clock period so as to synchronize the data. But due to the internal delay of the FFs,i.e. setup time (MUX 1 delay), the FFs take some time to settle down the data. This reduces the total clock period and hencce puts a constraint to the combinational delay.

So the constraint can be expressed as, combinational delay(theta) = Clock Period(T) - Setup Time(S)

![Screenshot (1690)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/481d5206-32ce-4c9a-b97f-716a67bf74e0)

### Clock Jitter
The clock signals or pulses are basically generated by a PLL inside a chip. But due to non-idealities and inherent resistance and capacitance variation inside the PLL, there is a small window is created based on  the arrival of the clock pulse to the required clock edge. This temporary variation of clock period is know as the jitter. 

![Screenshot (1693)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/b244e94c-a22a-406c-8d43-b3b61954fb91)

Due to this jitter, another variable is added to the constraint of the combinational delay which can be represented as-

combinational delay(theta) = Clock Period(T) - Setup Time(S) - Jitter(SU)

### Setup Time for Real Clock
A real clock considers the delays of extra buffers present in the clock path hence producing the skew. Basically skew is the difference between arrival time of clock signal between flops. So in case of a real clock the constraint can be modified as - combinational delay(theta) + Delta1 < Clock Period(T) + Delta2  - SetupTime(S) - Jitter (SU)

Delta1 and Delta2 are basically the delay of buffers present in the clock path of FF1 and FF2.

![Screenshot (1694)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/d524e66b-e6eb-4e7a-a474-3e1207b53569)

The left hand side term is called as arrival time whereas the right hand side term is called as required time. The difference between required time and arrival time is called as setup slack.

### Hold Time for Ideal Clock
Hold Time is the minimum amount of time that the data input (D) must remain stable after the clock edge for the data to be reliably latched by a sequential element (like a flip-flop). It is produced due to 2nd MUX present in the capture flop which takes some amount of time to send out the data or latch the data. 

![Screenshot (1695)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/a145e9ca-90d9-4a23-af01-0862ac3238bf)

It puts the constraint that the combinational delay should be greater than capture flop delay i.e. Combinational Delay (Theta) > Hold Time(H).

But for ideal cases due to wire RC delays in clock path there might be skew between the clock edges like earlier cases as well as uncertainty due to PLL. So for real clock it can be stated as - combinational delay(Theta) + Delta1 > Hold Time(H) + Delta2 + Hold Uncertainty(HU)

![image](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/d02767d8-8812-430e-8dba-9373f8c70e9b)

In this case also the left side term is reffered as the arrival time whereas the right side term is referred as required time. Only difference is the slack is defined as the differece of time between the arrival time and the required time for the flops. 


### Timing Analysis Using OpenSTA













## Clock Tree Synthesis TritonCTS and Signal Integrity
A clock tree is a network of interconnections in the design that distributes the clock signal from a single source to various parts of the circuit. Furthermore, the variation in the arrival times of the clock signal at different points in the circuit is called as the clock skew. Minimizing clock skew is essential for maintaining synchronization and hence the main goal of clock tree synthesis.

To achieve this a clock tree topology is implemented called as H-tree structure or algorithm which connects the midpoints of paths between two element which in turn help reducing the skew.

Another problem which arises due to this is the signal integrity issue which is produced due to the wire resistance and capacitance of the clock net. To overcome this challenge we use repeaters or buffers over the clock network which amplifies the signal over long distances.

Below figure describes both H-tree structure as well as clock tree buffers.

![Screenshot 2024-06-03 152533](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/fa4d40fb-7b04-4557-a7d4-dbf78fbb1d20)

You can see there is also a yellow line surrounding the clock nets. These are a part of clock net shielding. They are connected to either VDD or GND which doesnt switch. Basically what it does is it protects the critical clock nets from crosstalk issues which is basically produced due to the changing signal between nearby nets.

Crosstalk is mainly due to the chargin and discharging of the capacitors associated with the critical nets. It leads to issues like glitches and delta delay which in turn detoriates the original signal. Therfore it is necessary to create the clock net shielding to help avoid this phenomenon. The only downpoint with this is it prevents crosstalk at the cost of more routing resources.

# Module-5 : Final Steps for RTL2GDS Using triton-ROUTE and openSTA
SO upto this module we have dealt with synthesis, floorplan, routing, placement and CTS with a partial static timing analysis. Now comes thew routing phase where we defines the best possible path to connect two elements in order to use less roting resources with minimum timing violations due to its RC effects. Further we will use the tritonRoute tool to do the routing in our previous design followed by timing violations check and improvement through openSTA. This will conclude our whole process of RTL2GDS of the RISC-V architecture processor.

## Routing
Routing is basically the physical interconnection between cells and ports to drive signals and clocks. Our goal is to achieve the best possible route or path to connect the nets with less no of twist and turns which will allow us to use less routing resources. For this we use specific routing algorithms which takes measures to connect the nets by creating source and traget terminals and iterates the best possible path between them. One such algorithm is 'Lee's Maze Routing Algorthim'.

### Lee's Maze Algorithm
This algorith starts with existing pre-placed cells during the floorplan which acts as the obstruts for routing. It creates a routing grid inside the core with standard dimension and defines the source and traget. Then it starts with labeling adjacent grid box with integers and keeps it increasing till it reaches the target with minimum cost. Below we have taken an example of such labeling of grids till it reaches from source to target.

![Screenshot (1698)](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/b5acf4c0-f959-4a2a-aa2b-c512890a93ef)

Clearly we can see that it takes 9 minimum grid boxes to reach the target and hence now we have multiple paths to reach that point. But as we have discussed there should be minimum zigzag in path (due to lithography issues), the algorithm traces the path with minimum bends i.e. the 2nd figure . This concludes the Lee's algorithm. 

There are also other routing algorithms like Steiner tree and line search algorithm which follows the same principle of shortest routing path while using less memory and time than the Lee's algorithm.

### DRC Rules Associated with Routing
While doing the routing step we also have to consider the DRCviolation due to the interconnect wires which is produced due to the lithography processes. Below we mentioned some of the DRC issues which needs to be taken care of :
- Wire Width
- Wire Pitch
- Wire Spacing
- Signal short
- Via Widths
- Via Spacings
  
![Screenshot 2024-06-04 112105](https://github.com/ritish-behera/VSD-PhysicalDesign/assets/158822580/61dee48c-b24f-452b-a8fe-e46487ab7f63)












# Appendix
1. For more information visit https://github.com/efabless/openlane2
2. Tim Ansell - Skywater PDK: Fully open source manufacturable PDK for a 130nm process https://www.youtube.com/live/EczW2IWdnOM?si=lctS_oMLOfPLJU4v
3. Mohamed Shalan - OpenLane, A Digital ASIC Flow for SkyWater 130nm Open PDK https://www.youtube.com/live/Vhyv0eq_mLU?si=vtTc57Oq7si5CrXv
