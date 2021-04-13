# Advanced Physical Design Workshop
![Image of worshop](/Day1/WS_banner.PNG)


### Objectives:
* Design and characterize own standard cell.
* Have a hands-on in the Physical Design domain.
* Generate a full GDSII from a RTL netlist.
* Explore and contribute to open source EDA world.

### Contents:
* ##### Day1 – Inception of open-source EDA, OpenLANE and Sky130 PDK
* ##### Day 2 - Understand importance of good floorplan vs bad floorplan and introduction to library cells
* ##### Day 3 - Design and characterize one library cell using Magic Layout tool and ngspice
* ##### Day 4 - Pre-layout timing analysis and importance of good clock tree
* ##### Day 5 - Final steps for RTL2GDS

## Day1 – Inception of open-source EDA, OpenLANE and Sky130 PDK
### Objectives:
* How to talk to computers
* SoC design and OpenLANE
* Starting RISC-V SoC Reference design
* Get familiar to open-source EDA tools

Openlane is an open source physical design (ASIC) flow comprises of open source EDA tools used in each stage of the design, which takes RTL code and skywater130 foundry PDKs as input and gives out routed layout design GDSII as output. Using the GDSII layout, masks are generated and these masks are used to implement the IP on silicon chip by the foundry.

### Architecture of Openlane
![Image of Architecture](/Day1/Arch.PNG)

Each stage of RTL to GDSII flow has multiple sub-stages
1. #### Synthesis
* yosys - Performs RTL synthesis
* abc - Performs technology mapping
* OpenSTA - Pefroms static timing analysis on the resulting netlist to generate timing reports
2. #### Floorplan and PDN
* init_fp - Defines the core area for the macro as well as the rows (used for placement) and the tracks (used for routing)
* ioplacer - Places the macro input and output ports
* pdn - Generates the power distribution network
* tapcell - Inserts welltap and decap cells in the floorplan
3. #### Placement
* RePLace - Performs global placement
* Resizer - Performs optional optimizations on the design
* OpenPhySyn - Performs timing optimizations on the design
* OpenDP - Perfroms detailed placement to legalize the globally placed components
4. #### CTS
* TritonCTS - Synthesizes the clock distribution network (the clock tree)
5. #### Routing
* FastRoute - Performs global routing to generate a guide file for the detailed router
* CU-GR - Another option for performing global routing.
* TritonRoute - Performs detailed routing
* SPEF-Extractor - Performs SPEF extraction
6. #### GDSII Generation
* Magic - Streams out the final GDSII layout file from the routed def
* Klayout - Streams out the final GDSII layout file from the routed def as a back-up
7. #### Checks
* Magic - Performs DRC Checks & Antenna Checks
* Klayout - Performs DRC Checks
* Netgen - Performs LVS Checks
* CVC - Performs Circuit Validity Checks

Command to start Openlane with interactive flow i,e step by step process to understand and analyse each stage

`./flow.tcl -interactive` 

To import all packages to run the openlane flow

`package require openlane 0.9`

Now we are ready to execute openlane commands. In this workshop **picorv32a** risc processor is considered as an example. To synthesize this processor design, we first need to set up the stage for design.

`prep -design picorv32a`

This merges the technology level and cell level LEFs so that it need not to refer 2 different files. 

![Image1](/Day1/1.PNG)

It creates the set up file under **runs** directory of **picorv32a** design will all empty folders except tmp folder. As we proceed with each stage, files are created in respective folders.

![Image1](/Day1/2.PNG)

Preparation is done, now run the synthesis. This will run the synthesis and internally uses **ABC** open-source tool for technology mapping.

`run_synthesis`

![Image1](/Day1/3.PNG)

Synthesis takes the design in HDL and generates gate-level netlist under results folder

![Image1](/Day1/4.PNG)

And synthesis stage reports are generated under **reports** folder.

![Image1](/Day1/5.PNG)


## Day 2 - Understand importance of good floorplan vs bad floorplan and introduction to library cells
### Objectives:
* Chip Floor planning considerations
* Library Binding and Placement
* Cell design and characterization flows
* General timing characterization parameters

The outcome of synthesis stage is Netlist, which is the connectivity between all the components. The Next stage is FLOORPLAN, here, we wre interested in dimensions of standard cells not wires.
The Utilization factor is defined as ratio of Area occupied by netlist to the Total area of the core. In practical utilization factor will be 0.5 to 0.6.
The Aspect ratio is defined as the ratio of Height of the core to width of core. If Aspect ratio is 1, then chip is square else rectangle.
Preplaced cells are macros or IPs which is a piece of complex logic and used multiple times.
Decoupling capacitor is a huge capacitor, which is completely filled with charges and the voltage across decoupling capacitor is equal to to the voltage across power supply.

`prep -design picorv32a -tag trial_run1`

It will create the set up for synthesis under **trial_run1** folder, instead of generating new directory every time we run the set up command and can be use with **-overwrite** flag to to consider nw changes in config.tcl file

Under configuration folder, all the TCL files has default configurations, it can overriden by configuration files under picorv32a design. And README.m has variables which are required for each stage. These variables are also known as switches.

![Image](/Day2/1.PNG)

In floor plan, we set the die area, core area, aspect ratio, utilization factor and we place the input-output cells, power distribution network and macro placement. To run floorplan:

`run_floorplan`

![Image](/Day2/2.PNG)

This creates the defination file under results of floorplan, in which we can see the die area .

![Image](/Day2/4.PNG)

To see the layout after floor plan in magic

![Image](/Day2/5.PNG)

![Image](/Day2/6.PNG)

