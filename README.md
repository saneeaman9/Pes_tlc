# iiitb_tlc - Traffic Light Controller

# Contents

* [Introduction](https://github.com/majilokesh/iiitb_tlc#introduction)<br>
* [Working](https://github.com/majilokesh/iiitb_tlc#working)<br>
* [iverilog and GTKWave](https://github.com/majilokesh/iiitb_tlc#iverilog-and-gtkwave)<br>
* [yosys](https://github.com/majilokesh/iiitb_tlc#yosys--yosys-open-synthesis-suite)<br>
* [GLS - Gate Level Simulation](https://github.com/majilokesh/iiitb_tlc#gls---gate-level-simulation)
* [Openlane](https://github.com/majilokesh/iiitb_tlc#openlane)
* [Magic](https://github.com/majilokesh/iiitb_tlc#magic)
* [Generating Layout](https://github.com/majilokesh/iiitb_tlc#generating-layout)
* [Block Diagram](https://github.com/majilokesh/iiitb_tlc#block-diagram)
* [Simulation](https://github.com/majilokesh/iiitb_tlc#simulation)
* [Inverter Standard Cell Layout Extraction](https://github.com/majilokesh/iiitb_tlc#inverter-standard-cell-layout-extraction)
* [Openlane Flow Stages](https://github.com/majilokesh/iiitb_tlc#openlane-flow-stages)
* [Future Works](https://github.com/majilokesh/iiitb_tlc#future-works)


# Introduction
In this project, traffic light controller on a four-way road using a sensor is proposed. A sensor on the farm way is to detect if there are any vehicles and change the traffic light to allow the vehicles to cross the highway. Otherwise, highway light is always green since it has higher priority than the farm way.

# Working
  The circuit has three input signals (C, clk, rst_n) and two output signals (light_farm, light_highway). Input signal ‘C’ refers to the sensor on the farm way to detect if there are any vehicles on highway. If sensor detects any vehicle on highway (i.e., C = 1), then the traffic light on farm way i.e., the output ‘light_farm’ turns red and the traffic light on highway i.e., the output ‘light_highway’ turns green. When there are no vehicles on highway (i.e., C = 0), light_farm turns green and light_highway turns red. But highway has higher priority than the farm way. Both the output signals are of 3-bit size.
  
  In output signal, "001" represents Green light, "010" represents Yellow light and “100” represents Red light. ‘Clk’ is the clock signal and ‘rst_n’ is the active low reset signal. The circuit is designed to work at the positive edge of the clock signal, while at the negative edge of the rst_n the output signal is reset to default state i.e., Red light.
  
There are four cases in this controller circuit:
1. Green on highway and red on farm way
2. Yellow on highway and red on farm way
3. Red on highway and green on farm way
4. Red on highway and yellow on farm way

# iverilog and GTKWave
Icarus Verilog (iverilog) is a Verilog simulation and synthesis tool. It operates as a compiler, compiling source code written in Verilog (IEEE-1364) into some target format. For batch simulation, the compiler can generate an intermediate form called vvp assembly. This intermediate form is executed by the ''vvp'' command. For synthesis, the compiler generates netlists in the desired format. The compiler proper is intended to parse and elaborate design descriptions written to the IEEE standard IEEE Std 1364-2005.

GTKWave is a VCD waveform viewer based on the GTK library. This viewer support VCD and LXT formats for signal dumps. Waveform dumps are written by the Icarus Verilog runtime program vvp. The user uses $dumpfile and $dumpvars system tasks to enable waveform dumping, then the vvp runtime takes care of the rest. The output is written into the file specified by the $dumpfile system task. If the $dumpfile call is absent, the compiler will choose the file name dump.vcd or dump.lxt, depending on runtime flags.

**To install iverilog and GTKWave in Ubuntu, open your terminal and type the following commands**

```
$ sudo apt-get update
$ sudo apt get iverilog
$ sudo apt get install iverilog gtkwave
```

**To clone the Repository and download the Netlist files for Simulation, enter the following commands in your terminal**
```
$   sudo apt install -y git
$   git clone https://github.com/majilokesh/iiitb_tlc.git
$   cd iiitb_tlc
$   iverilog iiitb_tlc.v iiitb_tlc_tb.v
$   ./a.out
$   gtkwave tlc_out.vcd
```

# yosys – Yosys Open SYnthesis Suite
Yosys is a framework for Verilog RTL synthesis. It currently has extensive Verilog-2005 support and provides a basic set of synthesis algorithms for various application domains.

Synthesis transforms the simple RTL design into a gate-level netlist with all the constraints as specified by the designer. In simple language, Synthesis is a process that converts the abstract form of design to a properly implemented chip in terms of logic gates.

Synthesis takes place in multiple steps:
  * Converting RTL into simple logic gates.
  * Mapping those gates to actual technology-dependent logic gates available in the technology libraries.
  * Optimizing the mapped netlist keeping the constraints set by the designer intact.

Yosys can be adapted to perform any synthesis job by combining the existing passes (algorithms) using synthesis scripts and adding additional passes as needed by extending the yosys C++ code base.

Yosys is free software licensed under the ISC license (a GPL compatible license that is similar in terms to the MIT license or the 2-clause BSD license).

You need a C++ compiler with C++11 support (up-to-date CLANG or GCC is recommended) and some standard tools such as GNU Flex, GNU Bison, and GNU Make. TCL, readline and libffi are optional (see `ENABLE_*` settings in Makefile). Xdot (graphviz) is used by the ``show`` command in yosys to display schematics.

For example on Ubuntu the following commands will install all prerequisites for building yosys:
```
$ sudo apt-get install build-essential clang bison flex \ libreadline-dev gawk tcl-dev libffi-dev git \ graphviz xdot pkg-config python3 libboost-system-dev \ libboost-python-dev libboost-filesystem-dev zlib1g-dev
```
To configure the build system to use a specific compiler, use one of the following command:
```
$ make config-clang
$ make config-gcc
```
For other compilers and build configurations it might be necessary to make some changes to the config section of the Makefile.
```
$ vi Makefile            # ..or..
$ vi Makefile.conf
```
To build Yosys simply type 'make' in this directory.
```
$ make
$ sudo make install
```
Note that this also downloads, builds and installs ABC (using yosys-abc as executable name).

Tests are located in the tests subdirectory and can be executed using the test target. Note that you need gawk as well as a recent version of iverilog (i.e. build from git). Then, execute tests via:
```
$ make test
```

# GLS - Gate Level Simulation
GLS is generating the simulation output by running test bench with netlist file generated from synthesis as design under test. Netlist is logically same as RTL code, therefore, same test bench can be used for it.

**Why GLS?**

The main reasons for running GLS are as follows:

  * To verify the power up and reset operation of the design and also to check that the design does not have any unintentional dependencies on initial conditions.
  * To give confidence in verification of low power structures, absent in RTL and added during synthesis. 
  * It is a probable method to catch multicycle paths if tests exercising them are available.
  * Power estimation is done on netlist for the power numbers. 
  * To verify DFT structures absent in RTL and added during or after synthesis. Scan chains are generally inserted after the gate level netlist has been created. Hence, gate level simulations are often used to determine whether scan chains are correct. GLS is also required to simulate ATPG patterns. 
  * Tester patterns (patterns to screen parts for functional or structural defects on tester) simulations are done on gate level netlist.
  * To help reveal glitches on edge sensitive signals due to combination logic. Using both worst and best-case timing may be necessary.
  * It is a probable method to check the critical timing paths of asynchronous designs that are skipped by STA.
  * To avoid simulation artifacts that can mask bugs at RTL level (because of no delays at RTL level).
  * Could give insight to constructs that can cause simulation-synthesis mismatch and can cause issues at the netlist level.
  * To check special logic circuits and design topology that may include feedback and/or initial state considerations, or circuit tricks. If a designer is concerned about some logic then this is good candidate for gate simulation. Typically, it is a good idea to check reset circuits in gate simulation. Also, to check if we have any uninitialized logic in the design which is not intended and can cause issues on silicon.
  * To check if design works at the desired frequency with actual delays in place.
  * It is a probable method to find out the need for synchronizers if absent in design. It will cause “x” propagation on timing violation on that flop.

Below picture gives an insight of the procedure. Here while using iverilog, you also include gate level verilog models to generate GLS simulation.

![image](https://user-images.githubusercontent.com/72696170/183296780-4bad9547-69e9-4cee-b791-acb5a38951bf.png)

# Openlane
OpenLane is an automated RTL to GDSII flow based on several components including OpenROAD, Yosys, Magic, Netgen, CVC, SPEF-Extractor, CU-GR, Klayout and a number of custom scripts for design exploration and optimization. The flow performs full ASIC implementation steps from RTL all the way down to GDSII.
To know more go to https://github.com/The-OpenROAD-Project/OpenLane

### Installation instructions 
```
$   apt install -y build-essential python3 python3-venv python3-pip
```
Docker installation process: https://docs.docker.com/engine/install/ubuntu/

goto home directory->
```
$   git clone https://github.com/The-OpenROAD-Project/OpenLane.git
$   cd OpenLane/
$   sudo make
```
To test the open lane
```
$ sudo make test
```
It takes approximate time of 5min to complete. After 43 steps, if it ended with saying **Basic test passed** then open lane installed succesfully.

# Magic
Magic is a venerable VLSI layout tool, written in the 1980's at Berkeley by John Ousterhout, now famous primarily for writing the scripting interpreter language Tcl. Due largely in part to its liberal Berkeley open-source license, magic has remained popular with universities and small companies. The open-source license has allowed VLSI engineers with a bent toward programming to implement clever ideas and help magic stay abreast of fabrication technology. However, it is the well thought-out core algorithms which lend to magic the greatest part of its popularity. Magic is widely cited as being the easiest tool to use for circuit layout, even for people who ultimately rely on commercial tools for their product design flow.
More about magic at http://opencircuitdesign.com/magic/index.html

Run following commands one by one to fulfill the system requirement.

```
$   sudo apt-get install m4
$   sudo apt-get install tcsh
$   sudo apt-get install csh
$   sudo apt-get install libx11-dev
$   sudo apt-get install tcl-dev tk-dev
$   sudo apt-get install libcairo2-dev
$   sudo apt-get install mesa-common-dev libglu1-mesa-dev
$   sudo apt-get install libncurses-dev
```
**To install magic**
goto home directory

```
$   git clone https://github.com/RTimothyEdwards/magic
$   cd magic/
$   ./configure
$   sudo make
$   sudo make install
```
type **magic** terminal to check whether it installed succesfully or not. type **exit** to exit magic.

# Generating Layout

### Non-interactive OpenLane flow
Open terminal in home directory and type the following commands:
```
$   cd OpenLane/
$   cd designs/
$   mkdir iiitb_iiitb_tlc
$   cd iiitb_iiitb_tlc/
$   wget https://raw.githubusercontent.com/majilokesh/iiitb_tlc/main/config.json
$   mkdir src
$   cd src/
$   wget https://raw.githubusercontent.com/majilokesh/iiitb_tlc/main/iiitb_tlc.v
$   cd ../../../
$   sudo make mount
$   ./flow.tcl -design iiitb_tlc
```

STEPS RUNNING:

```

[STEP 1] : Running Synthesis.
[STEP 2] : Running Single-Corner Static Timing Analysis.
[STEP 3] : Running Initial Floorplanning, Setting Core Dimensions.
[STEP 4] : Running IO Placement.
[STEP 5] : Running Power planning with power {VPWR} and ground {VGND}.
[STEP 6] : Generating PDN.
[STEP 7] : Performing Random Global Placement.
[STEP 8] : Running Placement Resizer Design Optimizations.
[STEP 9] : Writing Verilog.
[STEP 10] : Running Detailed Placement.
[STEP 11] : Running Placement Resizer Timing Optimizations.
[STEP 12] : Writing Verilog, Routing.
[STEP 13] : Running Global Routing Resizer Timing Optimizations.
[STEP 14] : Writing Verilog.
[STEP 15] : Running Detailed Placement.
[STEP 16] : Running Global Routing, Starting FastRoute Antenna Repair Iterations.
[STEP 17] : Running Fill Insertion.
[STEP 18] : Writing Verilog.
[STEP 19] : Running Detailed Routing, No DRC violations after detailed routing.
[STEP 20] : Writing Verilog, Running parasitics-based static timing analysis.
[STEP 21] : Running SPEF Extraction at the min process corner.
[STEP 22] : Running Multi-Corner Static Timing Analysis at the min process corner.
[STEP 23] : Running SPEF Extraction at the max process corner.
[STEP 24] : Running Multi-Corner Static Timing Analysis at the max process corner.
[STEP 25] : Running SPEF Extraction at the nom process corner...
[STEP 26] : Running Single-Corner Static Timing Analysis at the nom process corner...
[STEP 27] : Running Multi-Corner Static Timing Analysis at the nom process corner...
[STEP 28] : Running Magic to generate various views, Streaming out GDS-II with Magic, Generating MAGLEF views...
[STEP 29] : Streaming out GDS-II with Klayout...
[STEP 30] : Running XOR on the layouts using Klayout...
[STEP 31] : Running Magic Spice Export from LEF...
[STEP 32] : Writing Powered Verilog.
[STEP 33] : Writing Verilog.
[STEP 34] : Running LEF LVS.
[STEP 35] : Running Magic DRC, Converting Magic DRC Violations to Magic Readable Format, Converting Magic DRC Violations to Klayout Database, Converting DRC Violations to RDB Format, No DRC violations after GDS streaming out, Running Antenna Checks.
[STEP 36] : Running OpenROAD Antenna Rule Checker.
[STEP 37] : Running CVC, Saving final set of views, 
Saving runtime environment, 
Generating final set of reports, Created manufacturability report at 'designs/iiitb_tlc/runs/RUN_2022.06.07_10.39.52/reports/manufacturability.rpt', 
Created metrics report at 'designs/iiitb_tlc/runs/RUN_2022.06.07_10.39.52/reports/metrics.csv', 
There are no max slew violations in the design at the typical corner, There are no hold violations in the design at the typical corner, There are no setup violations in the design at the typical corner.

[SUCCESS]: Flow complete.

```

### Interactive OpenLane flow
Open terminal in home directory and then type the following:
```

$ cd OpenLane/ 
$ sudo make mount 

./flow.tcl -interactive
package require openlane 0.9
prep -design iiitb_tlc

run_synthesis
run_floorplan
run_placement
run_cts
run_routing
run_magic
run_magic_spice_export
run_magic_drc
run_netgen
run_magic_antenna_check

```

To see the layout we use a tool called magic which we installed earlier.Type the following command in the terminal opened in the path to your design/runs/latest run folder/results/final/def/
 
```
magic -T /home/lokesh/openlane/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../../tmp/merged.nom.lef def read iiitb_tlc.def &
```

# Block Diagram

 ![image](https://user-images.githubusercontent.com/72696170/181302041-489c49ad-2ba5-4083-ac92-8a216c5a46e1.png)
 
The block diagram of the traffic light controller is shown in the figure above.

# Simulation
RTL simulation waveform:

![pre](https://user-images.githubusercontent.com/72696170/184681752-a64da83f-97fa-4caa-9ee5-fc45561564ff.png)

GLS simulation waveform:

![post](https://user-images.githubusercontent.com/72696170/184681803-e74315e0-bbed-4c91-aef5-014fb02a85a3.png)


# Inverter Standard Cell Layout Extraction
The Magic layout of a CMOS inverter will be used so as to intergate the inverter with the iiitb_tlc design. To do this, inverter magic file is sourced from vsdstdcelldesign by cloning it within the openlane directory as follows:

```
git clone https://github.com/nickson-jose/vsdstdcelldesign
```

This creates a vsdstdcelldesign named folder in the openlane directory.

To invoke magic to view the sky130_inv.mag file, the sky130A.tech file must be included in the command along with its path. To ease up the complexity of this command, the tech file can be copied from the magic folder to the vsdstdcelldesign folder.

The sky130_inv.mag file can then be invoked in Magic very easily by typing the following command in terminal in the vsdstdcelldesign directory:

```
magic -T sky130A.tech sky130_inv.mag &
```

Layout of sky130_vsdinv cell:

![ss1](https://user-images.githubusercontent.com/72696170/187520717-fe0f8bdf-bacd-44d2-9e21-87afe2557b0a.png)

In Sky130 the first layer is called the local interconnect layer or Locali.

To verify whether the layout is that of CMOS inverter, verification of P-diffusiona nd N-diffusion regions with Polysilicon can be observed.

Other verification steps are to check drain and source connections. The drains of both PMOS and NMOS must be connected to output port (here, Y) and the sources of both must be connected to power supply VDD (here, VPWR).

**LEF or library exchange format**: A format that tells us about cell boundaries, VDD and GND lines. It contains no info about the logic of circuit and is also used to protect the IP.

### Create port definition
Once the layout is ready, the next step is extracting LEF file for the cell. However, certain properties and definitions need to be set to the pins of the cell which aid the placer and router tool. For LEF files, a cell that contains ports is written as a macro cell, and the ports are the declared PINs of the macro. Our objective is to extract LEF from a given layout (here of a simple CMOS inverter) in standard format. Defining port and setting correct class and use attributes to each port is the first step.

The easiest way to define a port is through Magic Layout window and following are the steps:
* In Magic Layout window, first source the .mag file for the design (here inverter). Then Edit >> Text which opens up a dialogue box.

![ss2](https://user-images.githubusercontent.com/72696170/187522004-14034054-cb4b-4127-b7e7-1c89f73427c0.png)

* For each layer (to be turned into port), make a box on that particular layer and input a label name along with a sticky label of the layer name with which the port needs to be associated. Ensure the Port enable checkbox is checked and default checkbox is unchecked as shown in the figure:

![ss3](https://user-images.githubusercontent.com/72696170/187522485-509c2843-09e8-4af5-a147-29764677caf6.png)

In the above two figures, port A (input port) and port Y (output port) are taken from locali (local interconnect) layer. Also, the number in the textarea near enable checkbox defines the order in which the ports will be written in LEF file (0 being the first).

* For power and ground layers, the definition could be same or different than the signal layer. Here, ground and power connectivity are taken from metal1 (Notice the sticky label).

![image](https://user-images.githubusercontent.com/72696170/187597872-49f9dab3-8912-4e48-90ac-46e948b2cc2e.png)

![image](https://user-images.githubusercontent.com/72696170/187597908-563ba176-70f2-411c-8be0-6e5e7c1b940d.png)

### Standard Cell LEF generation
Before the CMOS Inverter standard cell LEF is extracted, the purpose of ports must be defined:

Select A area in magic toplevel and then type the following command in magic tkcon:
```
port class input
port use signal
```
Select Y area in magic toplevel and then type the following command in magic tkcon:
```
port class output
port use signal
```
Select VPWR area in magic toplevel and then type the following command in magic tkcon:
```
port class inout
port use power
```

Select VGND area in magic toplevel and then type the following command in magic tkcon:
```
port class inout
port use ground
```

For LEF extraction type the following command in magic tkcon:

```
lef write
```

This generates ```sky130_vsdinv.lef``` file.

# OpenLane Flow Stages
### 1. Synthesis
In order to integrate the CMOS inverter standard cell in the OpenLane flow, invoke openlane as usual and carry out following steps:
```
prep -design iiitb_tlc
set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
add_lefs -src $lefs
run_synthesis
```
![n1](https://user-images.githubusercontent.com/72696170/187526655-0873def5-6807-4325-927e-5c3eb23fe7d9.png)

**Synthesis statistics report:**

![finalstat](https://user-images.githubusercontent.com/72696170/187134319-fb39e76b-83ff-4415-a347-79e4032f350b.png)

**Gate count = 15**

**Flop Ratio = Ratio of total number of flip flops / Total number of cells present in the design = 2 / 15 = 0.133**

### 2. Floorplan
Next floorplan is run by typing the following command:
```
run_floorplan
```

![n2](https://user-images.githubusercontent.com/72696170/187528825-b7780eae-3e96-4959-9b68-bb0338d13797.png)

**Floorplan components report**

![fp1](https://user-images.githubusercontent.com/72696170/187528958-8b5fed43-846d-4715-8738-2484a15c7d3d.png)
![fpn](https://user-images.githubusercontent.com/72696170/187531001-994554e9-2fd5-4b18-8813-3604fa13cfcd.png)

To check the floorplan layout invoke magic from the results/floorplan directory:
```
magic -T /home/lokesh/vsd/OpenLane/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.nom.lef def read iiitb_tlc.def &
```

**Floorplan Layout**

![fpmag](https://user-images.githubusercontent.com/72696170/187532804-d1cf3437-6abe-4ba3-949d-3ae2cd3236e2.png)


### 3. Placement
After floorplan type the following command to do placement:
```
run_placement
```
![n3](https://user-images.githubusercontent.com/72696170/187529699-8507d1d7-72f7-4937-98ce-9e64079580af.png)

**Placement report**

![pl](https://user-images.githubusercontent.com/72696170/187530134-4e6f6934-0b5f-46ab-9783-9b20e8b5e423.png)

To check the placemnt layout invoke magic from the results/placement directory:
```
magic -T /home/lokesh/vsd/OpenLane/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.nom.lef def read iiitb_tlc.def &
```

**Placement Layout**

![goodmagic](https://user-images.githubusercontent.com/72696170/187387878-6f0734d6-0582-49e7-9413-9c50abe26108.png)

sky130_vsdinv cell integrated inside iiitb_tlc design:

![vsdinvintlc](https://user-images.githubusercontent.com/72696170/187387237-4a3ce9e6-4765-45ce-a05e-ef2bfc88794c.png)


### 4. Clock Tree Synthesis
The purpose of building a clock tree is enable the clock input to reach every element and to ensure a zero clock skew. H-tree is a common methodology followed in CTS. To run CTS type the following command after placement:
```
run_cts
```

![n4](https://user-images.githubusercontent.com/72696170/187535234-fd1edb05-f5d9-4266-ba01-2baee98e0835.png)

**CTS report**

![cts1](https://user-images.githubusercontent.com/72696170/187537368-a59250ea-2eef-490e-a534-730cbc36d184.png)
![cts2](https://user-images.githubusercontent.com/72696170/187537400-f92e8880-06fc-4c71-8126-ed7cc35182a6.png)
![cts3](https://user-images.githubusercontent.com/72696170/187537421-7e93eb69-ef28-4834-a779-72cb70f52dcb.png)

To calculate performance i.e. frequency of clock run OpenSta in OpenLane. Type the following commands in OpenLane:

![opensta_command](https://user-images.githubusercontent.com/72696170/192508623-56505a76-6e95-4418-b60f-47a04e5a45ca.png)

**OpenSta report**

![3 slack](https://user-images.githubusercontent.com/72696170/192698965-a8c0cfda-2d6c-4211-9b01-90050b2c5aef.png)

**Performance = 1 / (Data Arrival Time + Setup Time) = 1 / (0.65 + 0.07) = 1.38 GHz**


### 5. Power Distribution Network

Unlike the general ASIC flow, Power Distribution Network generation is not a part of floorplan run in OpenLANE. PDN must be generated after CTS and post-CTS STA analyses:
```
gen_pdn
```

We can confirm the success of PDN by checking the current def environment variable: ```echo $::env(CURRENT_DEF)```

![pdn](https://user-images.githubusercontent.com/72696170/189105590-9297c488-9d9a-4f37-a5fe-5bd8007e908b.png)

* gen_pdn - Generates the Power Distribution network
* The power distribution network has to take the design_cts.def as the input def file.
* This will create the grid and the straps for the Vdd and the ground. These are placed around the standard cells.
* The standard cells are designed such that it's height is multiples of the space between the Vdd and the ground rails. Here, the pitch is 2.72. Only if the above conditions are adhered it is possible to power the standard cells.
* The power to the chip, enters through the power pads. There is each for Vdd and Gnd
* From the pads, the power enters the rings, through the via
* The straps are connected to the ring. Vdd straps are connected to the Vdd ring and the Gnd Straps are connected to the Gnd ring. There are horizontal and the vertical straps
* Now the power has to be supplied from the straps to the standard cells. The straps are connected to the rails of the standard cells
* If macros are present then the straps attach to the rings of the macros via the macro pads and the pdn for the macro is pre-done.
* There are definitions for the straps and the railss. In this design straps are at metal layer 4 and 5 and the standard cell rails are at the metal layer 1. Vias connect accross the layers as required.

### 6. Routing

OpenLANE uses the TritonRoute tool for routing. There are 2 stages of routing:

  1. Global routing: Routing region is divided into rectangle grids which are represented as course 3D routes (Fastroute tool).
  2. Detailed routing: Finer grids and routing guides used to implement physical wiring (TritonRoute tool).

Features of TritonRoute:

1.  Honouring pre-processed route guides
2.  Assumes that each net satisfies inter guide connectivity
3.  Uses MILP based panel routing scheme
4.  Intra-layer parallel and inter-layer sequential routing framework

Running routing step in TritonRoute as part of openLANE flow:

```run_routing```

* run_routing - To start the routing
* The options for routing can be set in the config.tcl file.
* The optimisations in routing can also be done by specifying the routing strategy to use different version of TritonRoute Engine. There is a trade0ff between the optimised route and the runtime for routing.
* For the default setting picorv32a takes approximately 30 minutesaccording to the current version of TritonRoute.
* This routing stage must have the CURRENT_DEF set to pdn.def
* The two stages of routing are performed by the following engines:
  *  Global Route : Fast Route
  *  Detailed Route : Triton Route
* Fast Route generates the routing guides, whereas Triton Route uses the Global Route and then completes the routing with some strategies and optimisations for finding the best possible path connect the pins.

![routing](https://user-images.githubusercontent.com/72696170/189105715-fef7ff29-ba87-4d78-b418-69ab11cd89c9.png)

To calculate area type **'box'** command in Tkcon.tcl after selecting the design toplevel in magic.
![area](https://user-images.githubusercontent.com/72696170/192511349-87081e65-cdb2-417a-8a95-43f57e032cfe.png)

**Area = 4581.68 μm²**


# FUTURE WORKS
* Magic DRC check
* Antenna check
* LVS check
* GSDII file generation for tapeout

# AUTHOR
Lokesh Maji

# ACKNOWLEDGEMENT
Kunal Ghosh, Director, VSD Corp. Pvt. Ltd.

# CONTACTS
* Lokesh Maji, M.Tech - VLSI, Batch: 2022-24, IIITB, majilokesh10@gmail.com
* Kunal Ghosh, Diretor, VSD Corp. Pvt. Ltd., kunalpghosh@gmail.com

# REFERENCES
  [1]   Verilog HDL: A Guide to Digital Design and Synthesis, Samir Palnitkar, Prentice Hall Professional, 2003

  [2]   https://www.fpga4student.com

  [3]   https://vlsicoding.blogspot.com

  [4]   S. V. Kishore, V. Sreeja, V. Gupta, V. Videesha, I. B. K. Raju and K. M. Rao, "FPGA based traffic light controller," 2017 International       Conference on Trends in Electronics and Informatics (ICEI), 2017, pp. 469-475, doi: 10.1109/ICOEI.2017.8300971.
