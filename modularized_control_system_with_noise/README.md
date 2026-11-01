# FPGA-Based Closed-Loop Control System (FIR + PID)
## Overview
This folder contais a modularized digital control system implemented in SystemVerilog. The architecture integrates a 5 stage pipelined
FIR Moving Average Filter with a 3 stage pipelined PID controller. For the interconnection between those 2 modules the AXI4-Stream protocol
was used. 
## System Architecture
The system is designed to process sensor data in real-time while maintaining high clock frequencies (100 MHz+).
### Components:
* FIR Filter(8 tap moving average)
* PID controller(implements control coupled with Anti-Windup to prevent accumulator saturation.)
* AXI4-Stream interconnect(ensures compatability with heterogeneous SoC environments)
## Verification of the design via closed loop TB:
The system was verified using a SystemVerilog testbench that simulates a physical "Plant" (integrator model).
* The plant is an integrator: `y[n+1] = y[n] + 0.0005 * u[n]`
* Sensor noise: integer noise in ±14 added to the plant output.
* Input: step to a setpoint of 1000, `Ki = 0`.
## Project Visuals:
  ![The block design of my project](../docs/block_design.png)
  * The block design of my project.
  ![The output that is produced when applying a step function of amplitude 1000](../docs/pid_whole_system.png)
  * Output(plant_state) when a step function of amplitude 1000 is applied as a setpoint.
## Future Work
1. ### Utilization of Fixed-Point-Arithmetic
* Replacement of current integer only logic with Fixed-Point_Arithmatic/
* The implementation of bit-shift scaling will allow for gain coefficients (K < 1), something essential for the stability of high frequency systems.

