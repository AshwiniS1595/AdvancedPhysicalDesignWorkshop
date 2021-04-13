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
`$./flow.tcl -interactive` 
