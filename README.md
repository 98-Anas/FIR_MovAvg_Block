# FIR Filter in Verilog

This repository contains the implementation of a Finite Impulse Response (FIR) Filter using Verilog. The design is based on a 3rd-order Moving Average Filter and includes both the FIR filter and the supporting D Flip-Flop (DFF) module.

## Table of Contents

- [Introduction](#introduction)
- [Features](#features)
- [Implementation Details](#implementation-details)
- [Modules](#modules)
  - [FIR_Filter Module](#fir_filter-module)
  - [DFF Module](#dff-module)
- [Code](#code)
- [Schematic](#schematic)
- [Usage](#usage)
- [License](#license)

---

## Introduction

FIR filters are widely used in digital signal processing applications due to their simplicity and stability. This implementation uses a 3rd-order Moving Average Filter to compute the output as the weighted sum of the current and past input samples.

## Features

- 3rd-order Moving Average FIR Filter.
- Parameterized input width for flexibility.
- Modular design with separate FIR and DFF components.

## Implementation Details

The FIR filter uses four coefficients, each equal to `0.25` (scaled by 128 to avoid floating-point arithmetic). These coefficients are used to compute the weighted sum of the current and three previous input samples.

### Coefficients

| Coefficient | Binary Value |
|-------------|--------------|
| `b0`        | `6'b100000`  |
| `b1`        | `6'b100000`  |
| `b2`        | `6'b100000`  |
| `b3`        | `6'b100000`  |


### FIR Filter Output Equation

The output of the FIR filter is calculated as the weighted sum of the current input sample and three delayed input samples:

\[
\text{data\_out} = (\text{data\_in} \times b_0) + (x_1 \times b_1) + (x_2 \times b_2) + (x_3 \times b_3)
\]

Where:
- **data_in**: The current input sample.
- **x1**, **x2**, **x3**: Delayed versions of the input sample.
- **b0**, **b1**, **b2**, **b3**: Coefficients (weights) applied to each input sample.



Where:
- `data_in` is the current input sample.
- `x1`, `x2`, and `x3` are delayed input samples (generated using DFFs).

---

## Modules

### FIR_Filter Module

The `FIR_Filter` module performs the filtering operation.

#### Parameters
- `N`: Bit width of the input and output signals (default: 16).

#### Ports
| Port Name   | Direction | Width     | Description                  |
|-------------|-----------|-----------|------------------------------|
| `clk`       | Input     | 1         | Clock signal                 |
| `reset`     | Input     | 1         | Reset signal                 |
| `data_in`   | Input     | `N`       | Current input sample         |
| `data_out`  | Output    | `N`       | Filtered output sample       |

#### Internal Components
1. **D Flip-Flops (DFFs):**
   - Used to delay the input samples.
   - Generates `x1`, `x2`, and `x3` from `data_in`.

2. **Multipliers:**
   - Multiplies each input sample with its corresponding coefficient.

3. **Adder:**
   - Computes the sum of the weighted input samples.

---

### DFF Module

The `DFF` module is a simple D Flip-Flop with asynchronous reset.

#### Parameters
- `N`: Bit width of the data signal (default: 16).

#### Ports
| Port Name       | Direction | Width | Description               |
|-----------------|-----------|-------|---------------------------|
| `clk`           | Input     | 1     | Clock signal              |
| `reset`         | Input     | 1     | Reset signal              |
| `data_in`       | Input     | `N`   | Data to be delayed        |
| `data_delayed`  | Output    | `N`   | Delayed output signal     |

#### Functionality
- On the rising edge of the clock, `data_in` is passed to `data_delayed`.
- If `reset` is asserted, `data_delayed` is set to `0`.

---

## Code

```verilog
`timescale 1ns / 1ps
//////////////////////////////////////////////////////////////////////////////////
module FIR_Filter(clk, reset, data_in, data_out);

parameter N = 16;

input clk, reset;
input [N-1:0] data_in;
output reg [N-1:0] data_out; 

// coefficients defination
// Moving Average Filter, 3rd order
// four coefficients; 1/(order+1) = 1/4 = 0.25 
// 0.25 x 128(scaling factor) = 32 = 6'b100000
wire [5:0] b0 =  6'b100000; 
wire [5:0] b1 =  6'b100000; 
wire [5:0] b2 =  6'b100000; 
wire [5:0] b3 =  6'b100000;
wire [N-1:0] x1, x2, x3; 

// Create delays i.e x[n-1], x[n-2], .. x[n-N]
// Instantiate D Flip Flops
DFF DFF0(clk, 0, data_in, x1); // x[n-1]
DFF DFF1(clk, 0, x1, x2);      // x[x[n-2]]
DFF DFF2(clk, 0, x2, x3);      // x[n-3] 

//  Multiplication
wire [N-1:0] Mul0, Mul1, Mul2, Mul3;  
assign Mul0 = data_in * b0; 
assign Mul1 = x1 * b1;  
assign Mul2 = x2 * b2;  
assign Mul3 = x3 * b3;  
 
// Addition operation
wire [N-1:0] Add_final; 
assign Add_final = Mul0 + Mul1 + Mul2 + Mul3; 

// Final calculation to output 
always@(posedge clk)
data_out <= Add_final; 

endmodule


module DFF(clk, reset, data_in, data_delayed);
parameter N = 16;
input clk, reset;
input [N-1:0] data_in;
output reg [N-1:0] data_delayed; 

always@(posedge clk, posedge reset)
begin
    if (reset)
    data_delayed <= 0;
    else
    data_delayed <= data_in; 
    
end

endmodule
```
---

## Schematic
![Moving Average FIR Block](https://github.com/user-attachments/assets/1b30f0c5-f300-4de0-bb97-ce27778fdd76)



---

## Usage

1. Clone the repository:
   ```bash
   git clone <repository-url>
   

2. Open the Verilog files in a simulator or synthesis tool (e.g., Vivado, ModelSim).

3. Simulate the design using the provided testbench (create your own testbench if not included).

4. Analyze the waveforms and verify the functionality of the FIR filter.

---

## License

This project is licensed under the MIT License. See the LICENSE file for details.

