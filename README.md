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

![5](https://github.com/saneeaman9/Pes_tlc/assets/75088597/6b18a190-c512-427a-8eab-c2846210db7e)

![8](https://github.com/saneeaman9/Pes_tlc/assets/75088597/bcc88467-7e18-46f3-9b2a-74553103a157)



  # GLS Simulation:
+ Command to exectue
  ```
  iverilog ../my_lib/verilog_model/primitives.v ../my_lib/verilog_model/sky130_fd_sc_hd.v pes_tlc_net.v pes_tlc_tb.v
  ./a.out
  gtkwave pes_tlc_tb.vcd
  ```
![7](https://github.com/saneeaman9/Pes_tlc/assets/75088597/6b36f97d-9f0a-425c-992e-4bfa87d53ff2)
