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
## Simulation
  ## Noise rejection (Kp = 8)

Standard deviation of the controller output:

| | With FIR | Without FIR | Reduction |
|---|---|---|---|
| Kd = 0 | 23.3 | 65.6 | 2.8× |
| Kd = 120 | 194.4 | 1456.3 | 7.5× |

The derivative term amplifies sensor noise by about 8× even with the filter. The FIR removes most of that.

### Step response (Kp = 8)

| | Overshoot | Cycles to 98% |
|---|---|---|
| FIR + PID, Kd = 0 | 0.11% | 927 (9.3 µs) |
| FIR + PID, Kd = 120 | 0.16% | 985 (9.9 µs) |
| PID only, Kd = 0 | 0.12% | 960 |

### Stability limit

Peak-to-peak of the plant output in steady state, with desired state = 1000:

| Kp | 8 | 50 | 100 | 200 | 250 | 300 | 400 |
|---|---|---|---|---|---|---|---|
| FIR + PID, Kd = 0 | 2 | 6 | 9 | 31 | **239** | 324 | 357 |
| FIR + PID, Kd = 120 | 2 | 6 | 11 | 33 | 59 | **314** | 345 |
| PID only, Kd = 0 | 2 | 6 | 8 | 18 | 16 | 22 | 29 |
| PID only, Kd = 120 | 3 | 7 | 9 | 15 | 18 | 19 | 27 |


### Conclusion

The filter trades about 3 to 8× less output noise for a lower maximum stable gain: instability begins around `Kp` 200 to 250 with it, and above 400 it is not used. 
