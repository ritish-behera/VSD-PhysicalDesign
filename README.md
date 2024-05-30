# VSD- Physical Design Using OpenSource Tools
This repository details the report of all the technical works performed during the VSD- Physical Design workshop through OpenSource tools.
PDK Used : SKyWater 130nm
Date : 22nd May 2024
## Contents
1. Module-1 : Inception of OpenSOurce EDA Tools and Skywater 130 nm PDK
2. Module-2 : FloorPlanning, PowePlanning, Placement and Introduction to Library Cell Characterization
3. Module-3 : Design Library Cell Using MAGIC Layout and NGSPICE Characterization
4. Module-4 :
5. Module-5 :

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

[Figure of config.tcl and sky130.tcl file]

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

Later to improve the depth of diffusion of the wells, the wafer undergoes a high-temperature annealing process in a furnace. This step is known as "drive-in diffusion". The high temperature causes the dopant atoms to move from areas of high concentration (surface) into the silicon lattice, spreading out to create a uniform dopant distribution at the desired depth.

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

















# Appendix
1. For more information visit https://github.com/efabless/openlane2
2. Tim Ansell - Skywater PDK: Fully open source manufacturable PDK for a 130nm process https://www.youtube.com/live/EczW2IWdnOM?si=lctS_oMLOfPLJU4v
3. Mohamed Shalan - OpenLane, A Digital ASIC Flow for SkyWater 130nm Open PDK https://www.youtube.com/live/Vhyv0eq_mLU?si=vtTc57Oq7si5CrXv
