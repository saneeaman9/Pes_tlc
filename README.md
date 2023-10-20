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

