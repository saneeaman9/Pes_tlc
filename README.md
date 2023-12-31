# PES_TLC

# Introduction
In this project, traffic light controller on a four-way road using a sensor is proposed. A sensor on the farm way is to detect if there are any vehicles and change the traffic light to allow the vehicles to cross the highway. Otherwise, highway light is always green since it has higher priority than the farm way.

# Working
  The circuit has three input signals (C, clk, rst_n) and two output signals (light_farm, light_highway). Input signal ‘C’ refers to the sensor on the farm way to detect if there are any vehicles on highway. If sensor detects any vehicle on highway (i.e., C = 1), then the traffic light on farm way i.e., the output ‘light_farm’ turns red and the traffic light on highway i.e., the output ‘light_highway’ turns green. When there are no vehicles on highway (i.e., C = 0), light_farm turns green and light_highway turns red. But highway has higher priority than the farm way. Both the output signals are of 3-bit size.
  
  In output signal, "001" represents Green light, "010" represents Yellow light and “100” represents Red light. ‘Clk’ is the clock signal and ‘rst_n’ is the active low reset signal. The circuit is designed to work at the positive edge of the clock signal, while at the negative edge of the rst_n the output signal is reset to default state i.e., Red light.
  
There are four cases in this controller circuit:
1. Green on highway and red on farm way
2. Yellow on highway and red on farm way
3. Red on highway and green on farm way
4. Red on highway and yellow on farm way.

<br>

# iverilog, GTKWave & yosys.
<details>
<summary>Steps to install iVerilog & GTKWave</summary>
<br>

## iverilog and GTKWave
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
$   git clone https://github.com/saneeaman9/Pes_tlc.git
$   cd Pes_tlc
$   iverilog pes_tlc.v pes_tlc_tb.v
$   ./a.out
$   gtkwave tlc_out.vcd
```

</details>


<details>
<summary>Steps to install yosys.</summary>
<br>

## yosys – Yosys Open SYnthesis Suite
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


</details>



<br>


# Block Diagram
 ![block](https://github.com/saneeaman9/Pes_tlc/assets/75088597/6eea77e1-b941-4e90-8137-761905c90a18)


# RTL Simulation :
+ Command to exectue
  ```
  iverilog pes_tlc.v pes_tlc_tb.v                                                                                                      
  ./a.out                                                                                                                                            
  gtkwave pes_tlc_tb.vcd
  ```
![2](https://github.com/saneeaman9/Pes_tlc/assets/75088597/6940ddcd-dc8b-4f16-b87c-3dad6af58cf1)



 # RTL Synthesis :
+ Command to exectue
  ```
  yosys                                                                                                                                                 
  read_liberty -lib ../lib/sky130_fd_sc_hd_tt_025C_1v80.lib                                                                
  read_verilog pes_tlc.v                                                                                                                   
  synth -top pes_tlc                                                                                                                          
  abc -liberty ../lib/sky130_fd_sc_hd_tt_025C_1v80.lib                                                                
  write_verilog -noattr pes_tlc_net.v
  show
  ```

  ![1](https://github.com/saneeaman9/Pes_tlc/assets/75088597/6476137a-9679-4687-813f-c653cda9ed19)


  ![3](https://github.com/saneeaman9/Pes_tlc/assets/75088597/05c457a8-097c-4c1e-b99b-155f2aff9169)


![5](https://github.com/saneeaman9/Pes_tlc/assets/75088597/6b18a190-c512-427a-8eab-c2846210db7e)

![8](https://github.com/saneeaman9/Pes_tlc/assets/75088597/bcc88467-7e18-46f3-9b2a-74553103a157)



  # GLS Simulation:
+ Command to exectue
  ```
  iverilog ../my_lib/verilog_model/primitives.v ../my_lib/verilog_model/sky130_fd_sc_hd.v pes_tlc_net.v pes_tlc_tb.v
  ./a.out
  gtkwave pes_tlc_tb.vcd
  ```

![4](https://github.com/saneeaman9/Pes_tlc/assets/75088597/d6384750-fa0f-4850-b4c3-520852016c0d)

![Screenshot 2023-10-20 175908](https://github.com/saneeaman9/Pes_tlc/assets/75088597/423d1f28-4076-4f8b-a1fc-f5ab3935041d)

## OpenLane2 Installation

[Follow this link to install openlane2.](https://github.com/efabless/openlane2)

## Magic

Magic is a venerable VLSI layout tool, written in the 1980's at Berkeley by John Ousterhout, now famous primarily for writing the scripting interpreter language Tcl. Due largely in part to its liberal Berkeley open-source license, magic has remained popular with universities and small companies. The open-source license has allowed VLSI engineers with a bent toward programming to implement clever ideas and help magic stay abreast of fabrication technology. However, it is the well thought-out core algorithms which lend to magic the greatest part of its popularity. Magic is widely cited as being the easiest tool to use for circuit layout, even for people who ultimately rely on commercial tools for their product design flow.
More about magic at http://opencircuitdesign.com/magic/index.html

Run following commands one by one to fulfill the system requirement.

```bash
sudo apt-get install m4
sudo apt-get install tcsh
sudo apt-get install csh
sudo apt-get install libx11-dev
sudo apt-get install tcl-dev tk-dev
sudo apt-get install libcairo2-dev
sudo apt-get install mesa-common-dev libglu1-mesa-dev
sudo apt-get install libncurses-dev
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

# OpenLane Flow

* To begin, go to the OpenLane folder.
* Create a folder with your "design_name" in the designs folder.
* Then `cd pes_tlc`.
* Now create a new directory `src` and add your design verilog file here.
* Also download [these](https://github.com/saneeaman9/Pes_tlc/tree/main/src) files into the src folder.
  
![Screenshot 2023-10-31 093139](https://github.com/saneeaman9/Pes_tlc/assets/75088597/d975c33f-a229-44b8-a2bb-6230c986c45b)

* Now in the Openlane folder create a folder `pdks`.
* Add the sky130A files here.


### Getting started with the Flow

* In the OpenLane folder enter the following commands.

```bash
sudo make mount
./flow.tcl -interactive
package require openlane 0.9
prep -design pes_tlc
```

![1](https://github.com/saneeaman9/Pes_tlc/assets/75088597/9923f1f2-2ad4-49ab-b687-780a87b29f57)


![2](https://github.com/saneeaman9/Pes_tlc/assets/75088597/5f2a0b74-7a59-4239-a63f-ab9f5c490167)

### Synthesis

* Enter ```run_synthesis```

![3_1](https://github.com/saneeaman9/Pes_tlc/assets/75088597/d0b91960-141c-4822-b14e-2e42b8248046)

**Physical Cells**

![3](https://github.com/saneeaman9/Pes_tlc/assets/75088597/87e0b2b9-7b5c-4415-a529-2fb06bc5c650)


### Floorplan

* Enter ```run_floorplan```

![4](https://github.com/saneeaman9/Pes_tlc/assets/75088597/89afc79b-bbcb-4ef1-a2be-79e3e5162800)


* To view the floorplan go to `/home/bigpoppa/OpenLane/designs/pes_tlc/runs/RUN_2023.10.30_13.41.08/results/floorplan
`
 
* Enter the command:

```bash

magic -T /home/bigpoppa/OpenLane/pdks/sky130A/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.nom.lef def pes_tlc.def &

```

![4](https://github.com/saneeaman9/Pes_tlc/assets/75088597/d6270688-7141-4130-921b-bf4be7e8a8c3)


### Placement

* Enter ```run_placement```

* To view the design 

```bash

magic -T /home/bigpoppa/OpenLane/pdks/sky130A/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.nom.lef def pes_tlc.def &

```

![6 placement](https://github.com/saneeaman9/Pes_tlc/assets/75088597/cbc11bb3-d6a2-4103-b830-86077a6c12e7)



### CTS

* Enter ```run_cts```

**Reports from the CTS**

![7_1](https://github.com/saneeaman9/Pes_tlc/assets/75088597/a6cee785-140c-4022-a9ef-b5c336a3ec9d)


![7_2](https://github.com/saneeaman9/Pes_tlc/assets/75088597/67b6e283-d753-4bba-ba87-b968cf7c8372)

![7_3](https://github.com/saneeaman9/Pes_tlc/assets/75088597/6878c571-4207-4afd-ba56-f947b564f5ae)

![7_4](https://github.com/saneeaman9/Pes_tlc/assets/75088597/68cb4024-45fa-4b16-8ad8-252e404b9d93)



### Routing

* Enter ```run_routing```.

* To view the design go to `/home/bigpoppa/OpenLane/designs/pes_tlc/runs/RUN_2023.10.30_13.41.08/results/routing`

* Enter the command

```bash

magic -T /home/bigpoppa/OpenLane/pdks/sky130A/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.nom.lef def pes_tlc.def &

```

![8 routing](https://github.com/saneeaman9/Pes_tlc/assets/75088597/5d1ff93d-d70b-4b11-9f0c-18087ec8d1e9)

![8_1 routing](https://github.com/saneeaman9/Pes_tlc/assets/75088597/0d9fda73-bd97-4f21-88dc-21760e29ddc1)


**Congestion Report**

![final congestion report](https://github.com/saneeaman9/Pes_tlc/assets/75088597/f973107c-8d99-40e4-9972-a82ad011182a)

**Power & Clk Skew Report**

![report power](https://github.com/saneeaman9/Pes_tlc/assets/75088597/8604e1e4-720b-4c22-ae4a-848d7dac227c)

**Summary Report & Area Report**

![summary report and area report](https://github.com/saneeaman9/Pes_tlc/assets/75088597/b6e27fa3-c754-45b7-b816-2b3ecd5a65ae)
