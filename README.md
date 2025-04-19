# Custom-design-of-a-Tensor-Slice-for-acceleration-ML-on-FPGA

📊 Progress: 🟩⬜⬜⬜⬜ (20%)  **(WORK PROGRESS AND RESULTS AT THE END OF THE DOCUMENTATION)**

## Overview
- Objective: Design and evaluate a novel tensor slice for accelerating ML and DL on FPGA's
- Technology Node: 14nm
- Design Flow: RTL to GDSII using Synopsys EDA Tools (inclduing DFT and STA )
- Target Application: Matrix Computation (GEMM) , ML and DL applications 

## Introduction
FPGAs have evolved to accelerate deep learning (DL) by integrating specialized hardware, such as AI processing engines, low-precision arithmetic (8-bit fixed-point, FP16), and shadow multipliers. A recent advancement in this domain is **Tensor Slices**, which enhance FPGA compute density by embedding **systolic arrays** for efficient tensor operations. These slices support multiple precision formats and can be dynamically reconfigured into independent multipliers and MAC units. A local crossbar at the inputs reduces routing congestion, improving performance. Building on this concept, I aim to **custom design a Tensor Slice**, optimizing its architecture for higher efficiency, flexibility, and seamless FPGA integration while maintaining fine-grained programmability for other applications.


## Tensor Slice
## Block Diagram

 <p align="center">
  <img width="1200" height="500" src="/Images/BLOCK.png">
</p>

### Overview

- **Tensor Slice vs. DSP Slice** – A **Tensor Slice** is to deep learning what a **DSP Slice** is to digital signal processing, optimized for machine learning workloads.  

- **Supported Operations** – It accelerates **matrix-matrix and matrix-vector multiplications**, along with **element-wise (Eltwise) addition, subtraction, and multiplication**, crucial for fully connected, convolutional, and recurrent layers.  

- **Eltwise Operations** – Used in **normalization, residual connections, weight updates, and dropout layers**, enhancing efficiency in deep learning computations.  

- **Core Architecture** – Consists of a **2D array of 16 processing elements (PEs)**, each integrating a **multiplier and an adder**, supporting MAC operations.  

- **Control Logic** – Manages **input, output, and mode selection** for dynamic operation configuration.  

- **Data Handling** – **Input logic** aligns data for **systolic computation**, **output logic** selects and shifts processed data, and **muxing logic** configures different operational modes.

## Processing Element (PE) 
## Block Diagram

 <p align="center">
  <img width="1400" height="500" src="/Images/MAC.png">
</p>

**I/O Overview**

| Signal         | Width  | Description                           | Signal      | Width | Description                  |
|---------------|--------|---------------------------------------|------------|-------|------------------------------|
| `in`         | 96     | Operand input (A, B, bias)           | `ch_out`   | 32    | Chained output to another PE |
| `clk`        | 1      | System clock                         | `out`      | 32    | Final computed result        |
| `rst`        | 1      | Active-high reset                    | `op_sel`   | 1     | Operation select (MUL, ADD)  |
| `chain_in`   | 1      | Enables input chaining               | `m2`       | 2     | Multiplexer control          |
| `ch_in_ax_fwd` | 1    | Forwarding control for AX            | `chain_out`| 2     | Chaining output selection    |
| `ax_ay_fwd`  | 1      | Forwarding control for AY            | `ch_in`    | 32    | Chained input from PE        |
| `acc`        | 1      | Accumulation enable                  |            |       |                              |

**Operation Overview**

 Operation            | OPSEL | OP_SEL | ACC | AX_AY_FWD | CH_IN_AX_FWD | M2    | CHAIN_IN |
|----------------------|------ |------- |---- |---------- |------------- |------ |--------- |
| Multiply (MULT)      | 0     | 1      | 0   | 1         | 1            | 2'b10 | 0       |
| Addition (ADD)       | 1     | 1      | 0   | 1         | 1            | 2'b10 | 0       |
| Subtraction (SUB)    | 1     | 1      | 0   | 1         | 1            | 2'b10 | 0       |
| Multiply-Accumulate (MAC) | 0 | 1      | 1   | 1         | 1            | 2'b10 | 0       |
| Multiply-Add (MADD)  | 0     | 1      | 0   | 1         | 1            | 2'b01 | 0       |
| Multiply-Subtract (MSUB) | 1 | 1      | 0   | 1         | 1            | 2'b01 | 0       |

| CHAIN_IN | CH_IN_AX_FWD | CHAIN_OUT | Description                           |
|--------- |------------ |---------- |-------------------------------------- |
| 1        | 0          | 00        | Chained Multiply (MULT)               |
| 1        | 0          | 01        | Chained Addition/Subtraction (ADD/SUB) |
| 1        | 0          | 10        | Chained Multiply-Accumulate (MAC)     |

### Overview

- **Comprehensive Operations** – The Processing Element (PE) supports MAC, MULT, ADD/SUB, MADD/MSUB and PE chaining, enabling efficient chianed computations.

- **Dedicated FP32 Fixed Unit** – Unlike virtual logic and hardware sharing, the PE contains a fixed FP32 unit with dedicated adders and multipliers for all supported formats.

- **Unified Arithmetic Design** – The FP32 multiplier (24-bit mantissa MUL + 8-bit CLA for exponent) and corresponding adders are reused for BF16, INT16, and FP16 by appending zeros to match fixed-width processing.

- **Precision-Adaptive Processing** – Input data is zero-extended to FP32 width for computation, but the final result maintains the same precision format as the input (BF16, INT16, or FP16).

- **No Logical PE Mapping** – Unlike architectures with logical PE scaling (e.g., 16-bit → 1 PE, 8-bit → 4 PEs), each physical PE operates independently without virtual partitioning.

- **Efficient Multi-Format Computation** – This design ensures seamless format adaptation while maintaining high compute efficiency for mixed-precision deep learning workloads.

## Methodology 

- **Tool Flow (Synopsys EDA Tools)**
- 1️⃣ RTL Design & Simulation/Verification – Verilog/SystemVerilog, Xilinx Vivado, VCS, Verdi
- 2️⃣ Synthesis & DFT Insertion – Design Compiler NXT , TestMAX
- 3️⃣ Static Timing Analysis (STA) – PrimeTime (PT)
- 4️⃣ Place & Route (PnR) – IC Compiler II (ICC2) 
- 5️⃣ Power Analysis & Signoff – PrimeTime-PX (PTPX)
- 6️⃣ Final GDSII Generation – ICC2, Calibre (DRC/LVS)
  
- **Architecture Design Flow**

- 1️⃣ Tensor Slice Architecture Definition

Determine PE grid size (e.g., 4×4 systolic array).

Establish precision handling (FP32 core with reuse for BF16, FP16, INT16).

- 2️⃣ Processing Element (PE) & Compute Core Design

Implement FP32 compute core with fixed-width operand reuse.

- 3️⃣ Tensor Slice Control & Dataflow Optimization

Design input/output logic for efficient operand routing.

- 4️⃣ Scalability & System Integration

Ensure Tensor Slice tiling for larger tensor computations.


## Current Progress - Initial RTL design and iteration of PE completed 

## Processing Element (PE) - Floating Point
### RTL Design and Functional Verification

- **SCHEMATIC**
 <p align="center">
  <img width="1200" height="500" src="/Images/SCHEMATIC.png">
</p>

- **MULT AND ADD/SUB**
 <p align="center">
  <img width="1200" height="500" src="/Images/MULT , ADD ( OPSEL = 0 AND OP_SEL = 1 , ACC = 0 , AX_AY_FWD = 1 , CH_IN_AX_FWD = 1 , M2 = 2'b01 , CHAIN_IN = 0 ).png">
</p>

- **MULT-ADD(MADD)/MULT-SUB(MSUB)**
 <p align="center">
  <img width="1200" height="500" src="/Images/MULT - ADD ( OPSEL = 0 AND OP_SEL = 1 , ACC = 0, AX_AY_FWD = 1 , CH_IN_AX_FWD = 1 , M2 = 2'b10 , CHAIN_IN = 0 ).png">
</p>

- **MULT AND ACC (MAC)**
 <p align="center">
  <img width="1200" height="500" src="/Images/MULT AND ACC ( OPSEL = 0 AND OP_SEL = 1 , ACC = 1, AX_AY_FWD = 1 , CH_IN_AX_FWD = 1 , M2 = 2'b10 , CHAIN_IN = 0 ).png">
</p>

### Synthesis and Gate Level Simulation (GLS)

- **BLOCK DIAGRAM**
 <p align="center">
  <img width="1200" height="500" src="/Images/PU BLOCK.png">
</p>

- **PROCESSING ELEMENT NETLIST**
 <p align="center">
  <img width="1200" height="500" src="/Images/PU.png">
</p>

- **CONTROL UNIT NETLIST**
 <p align="center">
  <img width="1200" height="500" src="/Images/CU.png">
</p>


- **GLS (TENTATIVELY DONE IN XCELIUM)**
 <p align="center">
  <img width="1200" height="500" src="/Images/GLS.png">
</p>

- **AREA REPORT**
<p align="center">
  <img width="1200" height="500" src="/Images/AREA.png">
</p>

- **TIMING REPORT**
- The maximum delay of the Processing Element is just 2.45 ns 
 <p align="center">
  <img width="1200" height="500" src="/Images/TIMING.png">
</p>

- **POWER REPORT**
 <p align="center">
  <img width="1200" height="500" src="/Images/POWER.png">
</p>

## Processing Element (PE) - Fixed Point 
### RTL Design and Functional Verification

- **SCHEMATIC**
 <p align="center">
  <img width="1200" height="500" src="/Images/SYSTOLIC ARRAY SCHEMATIC.png">
</p>

- **MULT AND ADD/SUB**
 <p align="center">
  <img width="1200" height="500" src="/Images/INPUTS ZOOM.png">
</p>

- **MULT-ADD(MADD)/MULT-SUB(MSUB)**
 <p align="center">
  <img width="1200" height="500" src="/Images/OUTPUT ZOOM.png">
</p>

********** WORK UNDER PROGRESS ( CURRENTLY FIGURING OUT HOW TO DEVELOP SYSTOLIC ARRAY ARCHITECTURE USING THE PE ************

---------------------------------------------------------------------------------------------------------------------------------------------------------




















