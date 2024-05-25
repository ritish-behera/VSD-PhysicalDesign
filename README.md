# VSD- Physical Design Using OpenSource Tools
This repository details the report of all the technical works performed during the VSD- Physical Design workshop through OpenSource tools.
PDK Used : SKyWater 130nm
Date : 22nd May 2024
## Contents
1. Module-1 : Inception of OpenSOurce EDA Tools and Skywater 130 nm PDK
2. Module-2 :
3. Module-3 :
4. Module-4 :
5. Module-5 :

## Introduction
In the current scenario where using the commercial tools or software may feel expensive and difficult to master, the opensopurce EDA tools are trying to fill the gaps
to reach the mass of semiconducter enthusisat. Opensource tools are great way to learn the semiconducter fabrication flow starting from logic synthesis to chip tape out. Though as of now the optimizations of the tools and model libraries are not upto the mark as compared to commercial ones on performance basis, it is still useful for beginners and may turn out as a great deal in the future too.

  In this report, I have documented all my learnings as part of completion of "VSD-Physical design through opensource tools" workshop as well as tried to add a small contribution towards the community which could be useful for new learners to refer.

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

[picture of invoking openLANE tool]

To invoke the tool the flow.tcl file was executed through tcl command in the terminal inside the working directory. It initiated the entire flow of RTL to GDSII without human interaction. The "-interactive" switch can be used to counter the execution of entire flow at once to step by step execution by the user.

[Picture of package installaion, design preparation & synthesis]

After the initialisation of the tool, to install the additional packages the "package require openlane 0.9" command was used. 

Before synthesis we have to go through design setup stage which creates all the directories for the excution og the speciic design as well as prepares the files. Here we are using "picorv32a" design for our synthesis which is available in the openlane work directory. For design prepartion "prep -design picorv32a" command was used.

Once the setup is ready we run the synthesis through "run_synthesis" command which generates synthesized design output and stores inside the picorv32a design directory.

[Picture of synthesis result and report window]

After the synthesis, the files are stored inside the "results" and "reports" directory under "runs" folder of picorv32a design. All the timing reports, cell utilisation report and synthesized netlist can be found inside these directories.  

[Picture of flop ratio data]

Flop ratio represents the ratio between total no of D-flipflops used to the total no of cells present in the synthesized design.

These data can be found inside the "yosys.stat.rpt" file (/runs/reports/yosys.stat.rpt) which assembles all the data of logic block used in the design.

The number of D-flipflops are represented by "sky_fd_sc_hd_dfxtp_4" and it was divided by the total number of cells in the design to get the flop-ratio.

Flop Ratio = xxxx / xxxxx = xxxx

This concludes the first module.























# Appendix
1. For more information visit https://github.com/efabless/openlane2
2. Tim Ansell - Skywater PDK: Fully open source manufacturable PDK for a 130nm process https://www.youtube.com/live/EczW2IWdnOM?si=lctS_oMLOfPLJU4v
3. Mohamed Shalan - OpenLane, A Digital ASIC Flow for SkyWater 130nm Open PDK https://www.youtube.com/live/Vhyv0eq_mLU?si=vtTc57Oq7si5CrXv
