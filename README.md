# OpenSource Physical Design
  This repository contains all the information studied and created during the [Advanced Physical Design Using OpenLANE / SKY130](https://www.vlsisystemdesign.com/advanced-physical-design-using-openlane-sky130/) workshop. It is primarily foucused on a complete RTL2GDS flow using the open-soucre flow named OpenLANE. [PICORV32A](https://github.com/cliffordwolf/picorv32) RISC-V core design is used for the purpose.

# Table of Contents
  - [Introduction To RTL to GDSII Flow](#introduction-to-rtl-to-gdsii-flow)
  - [About Google SkyWater PDK](#about-google-skywater-pdk)
  - [List of All Open-Source Tools Used](#list-of-all-open-source-tools-used)
  - [Setting Up Environment](#setting-up-environment)
  - [Day 1 - Inception of open-source EDA, OpenLANE and Sky130 PDK](#day-1---inception-of-open-source-eda-openlane-and-sky130-pdk)
    - [IC Design Terminologies](#ic-design-terminologies)
    - [Introduction To RISC-V](#introduction-to-risc-v)
    - [SoC Design and OpenLANE](#soc-design-and-openlane)
      - [Open-Source PDK Directory Structure](#open-source-pdk-directory-structure)
      - [What is OpenLANE](#what-is-openlane)
    - [Open-Source EDA Tools](#open-source-eda-tools)
      - [OpenLANE Initialization](#openlane-initialization)
      - [Design Preparation](#design-preparation)
      - [Design Synthesis and Results](#design-synthesis-and-results)
  - [Day 2 - Good floorplan vs bad floorplan and introduction to library cells](#day-2---good-floorplan-vs-bad-floorplan-and-introduction-to-library-cells)
    - [Chip Floorplanning](#chip-floorplanning)
      - [Utilization Factor and Aspect Ratio](#utilization-factor-and-aspect-ratio)
      - [Power Planning](#power-planning)
      - [Pin Placement](#pin-placement)
      - [Floorplan using OpenLANE](#floorplan-using-openlane)
      - [Review Floorplan Layout in Magic](#review-floorplan-layout-in-magic)
    - [Placement](#placement)
      - [Placement and Optimization](#placement-and-optimization)
      - [Placement using OpenLANE](#placement-using-openlane)
    - [Cell Design and Characterization Flows](#cell-design-and-characterization-flows)
      - [Cell Design Flow](#cell-design-flow)
      - [Characterization Flow](#characterization-flow)
  - [Day 3 - Design library cell using Magic Layout and ngspice characterization](#day-3---design-library-cell-using-magic-layout-and-ngspice-characterization)
    - [CMOS Inverter Design using Magic](#cmos-inverter-design-using-magic)
    - [Extract SPICE Netlist from Standard Cell Layout](#extract-spice-netlist-from-standard-cell-layout)
    - [Transient Analysis using NGSPICE](#transient-analysis-using-ngspice)
  - [Day 4 - Pre-layout timing analysis and importance of good clock tree](#day-4---pre-layout-timing-analysis-and-importance-of-good-clock-tree)
    - [Magic Layout to Standard Cell LEF](#magic-layout-to-standard-cell-lef)
    - [Timing Analysis using OpenSTA](#timing-analysis-using-opensta)
    - [Clock Tree Synthesis using TritonCTS](#clock-tree-synthesis-using-tritoncts)
  - [Day 5 - Final steps for RTL2GDS](#day-5---final-steps-for-rtl2gds)
    - [Generation of Power Distribution Network](#generation-of-power-distribution-network)
    - [Routing using TritonRoute](#routing-using-tritonroute)
    - [SPEF File Generation](#spef-file-generation)
  - [References](#references)
  - [Acknowledgement](#acknowledgement)
 
# Introduction To RTL to GDSII Flow
  RTL to GDSII Flow contains all the steps involved in converting a logical Register Transfer Level(RTL) Design to a fabrication ready GDSII(Graphic Data Stream) format. GDSII is a database file format which is an industry standard for data exchange of IC layout artwork, which in turn get into Tapeout 
  The RTL to GSDII flow consists of following steps:
  - RTL Synthesis
  - Static Timing Analysis(STA)
  - Design for Testability(DFT)
  - Floorplanning
  - Placement
  - Clock Tree Synthesis(CTS)
  - Routing
  - GDSII Streaming
 
 All the steps are further discussed in details in the repository.
  
# About Google Open Source SkyWater PDK
  Google and SkyWater Technology Foundry in collaboration have released a completely open-source Process Design Kit(PDK) in May, 2020. The current release target to a SKY130 (i.e. 130 nm) process node is available as [SkyWater Open Source PDK](https://github.com/google/skywater-pdk). The PDK provides Physical VLSI Designer with a wide range of flexibility in design choices. All the designs and simulations listed in this repository are carried out using the same SkyWater Open Source PDK.

# List of All Open-Source Tools Used
  | Name of Tool | Application / Usage |
  | --- | --- |
  | [Yosys](https://github.com/YosysHQ/yosys) | Synthesis of RTL Design |
  | ABC | Mapping of Netlist |
  | [OpenSTA](https://github.com/The-OpenROAD-Project/OpenSTA) | Static Timing Analysis |
  | [OpenROAD](https://github.com/The-OpenROAD-Project/OpenROAD) | Floorplanning, Placement, CTS, Optimization, Routing |
  | [TritonRoute](https://github.com/The-OpenROAD-Project/TritonRoute) | Detailed Routing |
  | [Magic VLSI](http://opencircuitdesign.com/magic/) | Layout Tool |
  | [NGSPICE](https://github.com/imr/ngspice) | SPICE Extraction and Simulation |
  | SPEF_EXTRACTOR | Generation of SPEF file from DEF file |
  
# Setting Up Environment
  Many of the above tools are required for various tasks in Physical VLSI Design. Due to inefficiency. This is made easy by some custom scripts that setup the required tools and environment for them in just a few easy steps. To install all the required tools, one can refer to the below mentioned repositories:
  - [VSDFlow](https://github.com/kunalg123/vsdflow) - Installs Yosys, Magic, OpenTimer, OpenSTA and some other supporting tools
  - [OpenLANE Build Scripts](https://github.com/nickson-jose/openlane_build_script) - Install all required OpenROAD and some supporting tools
  
# Day 1 - Open-source EDA, OpenLANE and Sky130 PDK
 ## Talk to computers
 ## IC Design Terminologies
  During the Physical Designing, therse term are commanly used terms:
  
  - Package: It is a case that surrounds the circuit material to protect it from physical damage or corrosion and allow mounting of the electrical contacts connecting it to the printed circuit board (PCB). eg.IC with 48 pins and Quad Flat No-Leads(QFN) package.
  
   <img src="images/d1_package.png">
 
  - Die: A die is a small block of semiconducting material on which a given functional circuit is fabricated and take whole size of chip.
  
  - Chip: It is situated in centre of package which is connected through wire bonds to the Packages
  
  - Core: It is the actual area of the IC where the logic resides(Gates and MUXs) and executes set of instructions.
  
  - Pads: These are the interfaces between the internal signals of a chip and the external pins
 
  <img src="images/d1_ic_terms.JPG">
 
 ## Introduction To RISC-V
   [RISC-V](https://riscv.org/technical/specifications) is a new ISA that's available under open, free and non-restrictive licences. RISC-V ISA delivers a new level of free, extensible software and hardware freedom on architecture.
   - It is far simpler and smaller than other commercial ISAs available.
   - It avoids micro-architecture or technology dependent features.
   - It has small standard base ISA and multiple standard extensions.
   - It supports variable-length instruction encoding.
   A brief design for RISC-V core can be refered [here](https://github.com/ShonTaware/RISC-V_Core_4_Stage)
   
  #Software to Harware flow 
  
  The application software enters the system software. OS handles I/O operations, memories and many low level functions.This program passes to Compiler which changes the program to Assembly language(machine specific) .Now Assembler converts the instruction set to machine language (binary numbers).Then system software converts the apllication software into binary language.These binary numbers enter our chip layout and according different functionanlity is performed.
  
 
 ## SoC Design and OpenLANE
 
 Open souce ASIC design tools-
 
 -RTL models (github.com, librecores.org)
 -EDA tool (OpenROAD, OpenLANE)
 -PDK Data (SKYWater_130)
 
  <img src="images/d1_tools.png">
 
 
 ### Open-Source PDK Directory Structure
  
   #PDK (Process Design Kit)
  
  It is foundaries/lab specific which consists of tecnology node information, Process Design Rules (to verify DRC, LVC, PEX, etc), device model(Paramenters), I/O libraries, Standard cell libraries, macros files, lef files.
   Therse repo is being used while installing setup [VSDFlow](https://github.com/kunalg123/vsdflow) (for installing Yosys, OpenSTA, Magic, OpenTimer, netgent, etc) and [OpenLANE Build Scripts](https://github.com/nickson-jose/openlane_build_script).
  
 
   All the Process Design Kit(PDK) are listed under the `pdks/` directory. Along with the `Sky130A` we are using some other open-source PDKs and other related files are also available in the directory. The location of the PDK directory is given of `$PDK_ROOT` variable. 
    
   <img src="images/d1_pdk_directory_structure.png">
  
 ### What is OpenLANE
   [OpenLANE](https://github.com/efabless/openlane) is an automated RTL to GDSII flow which includes various open-source components such as OpenROAD, Yosys, Magic, Fault, Netgen, SPEF-Extractor. It also facilitates to add custom design exploration and optimization scripts.
   The detailed diagram of the OpenLANE architecture is shown below:
   
   <img src="images/d1openlane_flow.JPG">
   
   OpenLANE flow consists of several stages. All flow steps are run in sequence. Each stage may consist of multiple sub-stages. OpenLANE can also be run interactively(not autonomous as shown here.
#RTL to GDS flow
<img src="images/d1_RTL2GDS.png"> 
  1. Synthesis
      1. `yosys` - Performs RTL synthesis
      2. `abc` - Performs technology mapping
      3. `OpenSTA` - Pefroms static timing analysis on the resulting netlist to generate timing reports
   <img src="images/d1_synthesis.png">   
  2. Floorplan and PDN
      1. `init_fp` - Defines the core area for the macro as well as the rows (used for placement) and the tracks (used for routing)
      2. `ioplacer` - Places the macro input and output ports
      3. `pdn` - Generates the power distribution network
      4. `tapcell` - Inserts welltap and decap cells in the floorplan
   <img src="images/d1_floorplanning.png">    
  3. Placement
      1. `RePLace` - Performs global placement
      2. `Resizer` - Performs optional optimizations on the design
      3. `OpenPhySyn` - Performs timing optimizations on the design
      4. `OpenDP` - Perfroms detailed placement to legalize the globally placed components
     <img src="images/d1_placement.png"> 
  4. CTS
      1. `TritonCTS` - Synthesizes the clock distribution network (the clock tree)
      <img src="images/d1_cts.png">  
  5. Routing *
      1. `FastRoute` - Performs global routing to generate a guide file for the detailed router
      2. `TritonRoute` - Performs detailed routing
      3. `SPEF-Extractor` - Performs SPEF extraction
      <img src="images/d1_routing.png">  
  6. GDSII Generation
      1. `Magic` - Streams out the final GDSII layout file from the routed def
   
  7. Checks
      1. `Magic` - Performs DRC Checks & Antenna Checks
      2. `Netgen` - Performs LVS Checks
      
 ## Open-Source EDA Tools
 ### OpenLANE Initialization
   For invoking OpenLANE in Linux Ubuntu, we should first run the docker everytime we use OpenLANE. This is done by using the following script:
    
    docker run -it -v $(pwd):/openLANE_flow -v $PDK_ROOT:$PDK_ROOT -e PDK_ROOT=$PDK_ROOT -u $(id -u $USER):$(id -g $USER) openlane:rc6
   
   A custom shell script or commands can be generated to make the task simpler.
   
   - To invoke OpenLANE run the `./flow.tcl` script.
   - OpenLANE supports two modes of operation: interactive and autonomous.
   - To use interactive mode use `-interactive` flag with `./flow.tcl`
   
   <img src="images/d1_openlane_invoke.png"> 
   
 ### Design Preparation
   The first step after invoking OpenLANE is to import the openlane package of required version. This is done using following command. Here 0.9 is the required version of OpenLANE.
   
    package require openlane 0.9
    
   The next step is to prepare our design for the OpenLANE flow. This is done using following command:
       
    prep -design <design-name>
   
   Some additional flags that can be used while preparation are:
     <br />`-tag <name-for-current-run>` - All the files generated during the flow will be stored in a directory named `<name-for-current-run>`
     <br />`-overwrite` - If a directory name mentioned in `-tag` already exists, it will be overwritten.
   
   <img src="images/d1_openlane_design_prep.png"> 
   
   During the design preparation the technology LEF and cell LEF files are merged together to obtain a `merged.lef` file. The LEF file contains information like the layer information, set of design rules, information about each standard cell which is required for place and route. 
    
 ### Design Synthesis and Results
   The first step in OpenLANE flow is RTL Synthesis of the design loaded. This is done using the following command.
   
    run_synthesis
   
   <img src="images/d1_openlane_synthesis.png">
   
# Day 2 - Good floorplan vs bad floorplan and introduction to library cells
 ## Chip Floorplanning
   Chip Floorplanning is the arrangement of logical block, library cells, pins on silicon chip. It makes sure that every module has been assigned an appropriate area and aspect ratio, every pin of the module has connection with other modules or periphery of the chip and modules are arranged in a way such that it consumes lesser area on a chip.
   
 ### Utilization Factor and Aspect Ratio
   Utilization Factor is ratio of the area of core used by standard cells to the total core area. The utilization factor is generally kept in the range of 0.5-0.7 i.e. 50% - 60%. Maintaining a proper utilization factor facilitates placement and routing optimization. simlarly aspect ratio 
   
 ### Power Planning
   Power planning is a step in which power grid network is created to distribute power to each part of the design equally. This step deals with the unwanted voltage drop and ground bounce. Steady state IR Drop is caused by the resistance of the metal wires comprising the power distribution network. By reducing the voltage difference between local power and ground, steady-state IR Drop reduces both the speed and noise immunity of the local cells and macros.
   
 ### Pin Placement
   Pin placement is a important part of floorplanning as the timing delays and number of buffers required is dependent on the position of the pin. There are multiple pin placement option available such as equidistant placement, high-density placement.
 
 ### Floorplan using OpenLANE
   Floorplanning in OpenLANE is done using the following command. 
    
    run_floorplan
   
   Successful floorplanning gives a `def` file as output. This file contains the die area and placement of standard cells.
   
  
 
 ### Review Floorplan Layout in Magic
   Magic Layout Tool is used for visualizing the layout after floorplan. In order to view floorplan in Magic, following three files are required:
    1. Technology File (`sky130A.tech`)
    2. Merged LEF file (`merged.lef`)
    3. DEF File
    
  
   <img src="images/d2_floorplan_magic.JPG">
   <img src="images/d2_floorplan_magic_expand.png">
 
 ## Placement
 ### Placement and Optimization
   The next step after floorplanning is placement. Placement determines location of each of the components on the die. Placement does not just place the standard cells available in the synthesized netlist. It also optimizes the design, thereby removing any timing violations created due to the relative placement on die.
   
 ### Placement using OpenLANE
   Placement in OpenLANE is done using the following command. 
    
    run_placement
   
   The DEF file created during floorplan is used as an input to placement. Placement in OpenLANE occurs in two stages:
   - Global Placement
   - Detailed Placement
   
   Placement is carried out as an iterative process till the value of overflow converges to 0.
   
   <img src="images/d2_placement_invoke_magic_cmd.png">
   <img src="images/d2_placement_magic.png">
 
   <table border="0"><tr><td><img src="images/d2_placement_magic_expand_2 (2)"> </td><td> <img src="images/d2_placement_magic_expand_2.png"> </td></tr></table>
   
 ## Cell Design and Characterization Flows
 ### Cell Design Flow
  In a border view Cell Design flow is are the stages or steps involved in the entire design of a standard cell. The figure below shows the input, output and design steps involved in cell design
  
  <img src="images/d2_cell_design_flow.PNG">
 
 ### Characterization Flow
  There are few problems of Standard Cells in polygon level format (GDSII). Some of them are:
  - Extraction of functionality is complicated and unnecessary as it is known
  - Functional/Delay simulation takes way too long
  - Power extraction for a whole chip takes too long
  - Automatic detection of timing constraints (e.g. Setup time) is difficult

  A solution to above problems is Cell Characterization. It is a simple model for delay, function, constraints and power on cell/gate level. The Characterization Flow consists of the following stages:
  1. Netlist Extraction - Transistors, resistances and capacitances are extracted with special tools and saved as SPICE netlist (or similar)
  2. Specification of parameters - Library-wide parameters have to be specified: e.g. max Transition time
  3. Model selection and specification - The used models determine the required data
  4. Measurement - The cells are simulated with a SPICE-like tool to obtain the required data
  5. Model Generation - The obtained data is fed into the models
  6. Verification - Different checks are performed to ensure the correctness of the characterization
 
# Day 3 - Design library cell using Magic Layout and ngspice characterization
  Every Design is represented by equivalent cell design. All the standard cell designs are available in the Cell Library. A fully custom cell design that meets all rules can be added to the library. To begin with, a CMOS Inverter is designed in Magic Layout Tool and analysis is carried out using NGSPICE tool.
  
 ## CMOS Inverter Design using Magic
  The inverter design is done using Magic Layout Tool. It takes the technology file as an input (`sky130A.tech` in this case). Magic tool provide a very easy to use interface to design various layers of the layout. It also has an in-built DRC check fetaure.
  The snippet below shows a layout for CMOS Inverter with and without design rule violations.
  
  <table border="0">
  <tr>
    <td><img src="images/d3_magic_layout_with_error.png"> </td>
    <td> <img src="images/d3_magic_with_error.png"> </td>
  </tr>
  <tr>
    <td><img src="images/d3_magic_layout_without_error.png"> </td>
    <td> <img src="images/d3_magic_without_error.JPG"> </td>
  </tr>
  </table>
  
 ## Extract SPICE Netlist from Standard Cell Layout
  To simulate and verify the functionality of the standard cell layout designed, there is a need of SPICE netlist of a given layout. To mention in brief, "Simulation Program with Integrated Circuit Emphasis (SPICE)" is an industry standard design language for electronic circuitry. SPICE model very closely models the actual circuit behavior.
  Extraction of SPICE model for a given layout is done in two stages.
  1. Extract the circuit from the layout design.
  
    extract all
  
  2. Convert the extracted circuit to SPICE model.
    
    ext2spice cthresh 0 rthresh 0
    ext2spice
  
  The extracted SPICE model like the first snippet shown below. Some modification are done to the SPICE netlist for the purpose of simulations, which is shown in the second snippet below.
  
  <table border="0">
  <tr>
    <td> <center>Commands for Extraction</center> <br /><img src="images/d3_spice_ext_1.png"> </td>
    <td> <center>.ext file</center> <img src="images/d3_spice_ext_2.JPG"> </td>
  </tr>
  <tr>
    <td> <center>Generated SPICE Netlist</center> <img src="images/d3_spice_1.png"> </td>
    <td> <center>Modified SPICE Netlist</center> <img src="images/d3_spice_2.png"> </td>
  </tr>
  </table>
  
 ## Transient Analysis using NGSPICE
  The SPICE netlist generated in previous step is simulated using the NGSPICE tool. NGSPICE is an open-source mixed-level/mixed-signal electronic spice circuit simulator.
  The command used to invoke NGSPICE is shown below.
  
    ngspice <name-of-SPICE-netlist-file>
    
  Following command is used to plot waveform in ngspice tool.
    
    ngspice 1 -> plot Y vs time A
    
   <img src="images/d3_ngspice_2.png">
   
   Below figure shows the waveform of Inverter output vs input w.r.t. time. Many timing parameters like rise time delay, fall time delay, propagation delay are calculated using this waveform.
   
   <img src="images/d3_ngspice_3.png">
  
# Day 4 - Pre-layout timing analysis and importance of good clock tree
  In order to use a design of standard cell layout in OpenLANE RTL2GDS flow, it is converted to a standard cell LEF. LEF stands for Library Exchange Format. The entire design has to be analyzed for any timing violations after addition or change in the design.
  
 ## Magic Layout to Standard Cell LEF
  Before creating the LEF file we require some details about the layers in the designs. This details are available in a `tracks.info` as shown below. It gives information about the `offset` and `pitch` of a track in a given layer both in horizontal and vertical direction. The track information is given in below mentioned format.
  
    <layer-name> <X-or-Y> <track-offset> <track-pitch>
    
  <img src="images/d4_track_info.png">
  
  Horizontal track pitch = 0.46, verticle track pitch = 0.34, horizontal offset = 0.23, verticle offset = 0.17
  <img src="images/9.png">
  To create a standard cell LEF from an existing layout, some important aspects need to be taken into consideration.
  1. The height of cell be appropriate, so that the `VPWR` and `VGND` properly fall on the power distribution network.
  2. The width of cell should be an odd multiple of the minimum permissible grid size.
  3. The input and ouptut of the cell fall on intersection of the vertical and horizontal grid line.
  
  <img src="images/d4_valid_layout.png">  
  
  Then open our new inverter mag.

  `magic -T sky130A.tech sky130_vsdinv.mag`
   edited config.tcl is shown below:
  <img src="images/2.png"> 
  
  `cp sky130_fd_sc_hd__*  /home/abhishek_v/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/src`
  <img src="images/6.png">
 
 `set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
add_lefs -src $lefs`

 <img src="images/7.png">
 
 <img src="images/12.png">
  <img src="images/13.png">
 
 Then run synthesis
 <img src="images/13.png">
 
 
 
 
  

 ## Timing Analysis using OpenSTA
  The Static Timing Analysis(STA) of the design is carried out using the OpenSTA tool. The analysis can be done in to different ways.
  - Inside OpenLANE flow: This is by invoking `openroad` command inside the OpenLANE flow. In the openroad OpenSTA is invoked.
  - Outside OpenLANE flow: This is done by directly invoking OpenSTA in the command line. This requires extra configuration to be done to specific the verilog file, constraints, clcok period and other required parameters.
   
  OpenSTA is invoked using the below mentioned command.
  
    sta <conf-file-if-required>
  
  The above command gives an Timing Analysis Report which contains:
   1. Hold Time Slack
   2. Setup Time Slack
   3. Total Negative Slack (= 0.00, if no negative slack)
   4. Worst Negative Slack (= 0.00, if no negative slack)
  
  <table border="0">
  <tr>
    <td> <img src="images/14.png"> </td>
    <td> <img src="images/15.png"> </td>
  </tr>
  </table>
  Change layout to meet slack
   <img src="images/16.png">
  
  If the design produces any setup timing violaions in the analysis, it can be eliminated or reduced using techniques as follows:
  1. Increase the clock period (Not always possible as generally operating frequency is freezed in the specifications)
  2. Scaling the buffers (Causes increase in design area)
  3. Restricting the maximum fan-out of an element. 
  <tr>
    <td> <img src="images/18.png"> </td>
    <td> <img src="images/19.png"> </td>
    <td> <img src="images/20.png"> </td>
   </tr>
   </table>
  
 ## Clock Tree Synthesis using TritonCTS
  Clock Tree Synthesis(CTS) is a process which makes sure that the clock gets distributed evenly to all sequential elements in a design. The goal of CTS is to minimize the clock latency and skew.
  There are several CTS techniques like:
  1. H - Tree
  2. X - Tree
  3. Fish bone
  
  In OpenLANE, clock tree synthesis is carried out using TritonCTS tool. CTS should always be done after the floorplanning and placement as the CTS is carried out on a `placement.def` file that is created during placement stage.
  
  The command used for running CTS in OpenLANE is given below.
  
    run_cts
    
   <img src="images/d4_cts_1.JPG">
   <img src="images/d4_cts_2.JPG">
   
   

# Day 5 - Final steps for RTL2GDS
 ## Generation of Power Distribution Network
   In a normal RTL to GDSII flow the generation of power distribution network is done before the placement step, but in the OpenLANE flow generation of PDN is carried out after the Clock Tree Synthesis(CTS). This step generates all the tracks, rails required for routing power to entire chip.
   Generation of power distribution network is done using following command.
   
    gen_pdn
    
   <img src="images/d5_pdn.JPG">
   
 ## Routing using TritonRoute
   OpenLANE uses TritonRoute, an open source router for modern industrial designs. The router consists of several main building blocks, including pin access analysis, track assignment, initial detailed routing, search and repair, and a DRC engine.
   The routing process is implemented in two stages:
   1. Global Routing - Routing guides are generated for interconnects
   2. Detailed Routing - Tracks are generated interatively.
   TritonRoute 14 ensures there are no DRC violations after routing.
   
   The following command is used for routing.
   
    run_routing
    
   <table border="0">
   <tr>
    <td> <img src="images/d5_routing.JPG"> </td>
    
   </tr>
   </table>

   
# References
  - RISC-V: https://riscv.org/
  - VLSI System Design: https://www.vlsisystemdesign.com/
  - OpenLANE: https://github.com/The-OpenROAD-Project/OpenLane

# Acknowledgement
 
  - [Kunal Ghosh](https://github.com/kunalg123), Co-founder, VSD Corp. Pvt. Ltd.
  - [Nickson Jose](https://github.com/nickson-jose) ASIC design Engineer and workshop instructor

