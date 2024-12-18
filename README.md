# Moving Average Block on FPGA
# Low-pass finite impulse response Filter in Verilog

This repository contains the implementation of a Finite Impulse Response (FIR) Filter using Verilog. The design is based on a 3rd-order Moving Average Filter and includes both the FIR filter and the supporting D Flip-Flop (DFF) module.

## Table of Contents

- [Introduction](#introduction)
- [Features](#features)
- [Implementation Details](#implementation-details)
- [Modules](#modules)
  - [FIR_Filter Module](#fir_filter-module)
  - [DFF Module](#dff-module)
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

### Formula

The output of the filter is computed as:

\[
data\_out = (data\_in \times b0) + (x1 \times b1) + (x2 \times b2) + (x3 \times b3)
\]

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

## Usage

1. Clone the repository:
   ```bash
   git clone <repository-url>
   ```

2. Open the Verilog files in a simulator or synthesis tool (e.g., Vivado, ModelSim).

3. Simulate the design using the provided testbench (create your own testbench if not included).

4. Analyze the waveforms and verify the functionality of the FIR filter.

---

## License

This project is licensed under the MIT License. See the LICENSE file for details.

