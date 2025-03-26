# Custom-design-of-a-Tensor-Slice-for-acceleration-ML-on-FPGA
Custom design and evaluation of a Tensor Block for accelerating ML and DL processes on FPGA

## Overview
- Objective: Design and evaluate a custom tensor slice for accelerating ML and DL on FPGA's
- Technology Node: 14nm
- Design Flow: RTL to GDSII using Synopsys EDA Tools (inclduing DFT and FV )
- Target Application: Matrix Computation (GEMM) , ML applications 

## Introduction
FPGAs have evolved to accelerate deep learning (DL) by integrating specialized hardware, such as AI processing engines, low-precision arithmetic (8-bit fixed-point, FP16), and shadow multipliers. A recent advancement in this domain is **Tensor Slices**, which enhance FPGA compute density by embedding **systolic arrays** for efficient tensor operations. These slices support multiple precision formats and can be dynamically reconfigured into independent multipliers and MAC units. A local crossbar at the inputs reduces routing congestion, improving performance. Building on this concept, I aim to **custom design a Tensor Slice**, optimizing its architecture for higher efficiency, flexibility, and seamless FPGA integration while maintaining fine-grained programmability for other applications.


## Tensor Slice
## Block Diagram

 <p align="center">
  <img width="1200" height="500" src="/Images/Screenshot 2025-03-13 122353.png">
</p>

- **Tensor Slice vs. DSP Slice** – A **Tensor Slice** is to deep learning what a **DSP Slice** is to digital signal processing, optimized for machine learning workloads.  

- **Supported Operations** – It accelerates **matrix-matrix and matrix-vector multiplications**, along with **element-wise (Eltwise) addition, subtraction, and multiplication**, crucial for fully connected, convolutional, and recurrent layers.  

- **Eltwise Operations** – Used in **normalization, residual connections, weight updates, and dropout layers**, enhancing efficiency in deep learning computations.  

- **Core Architecture** – Consists of a **2D array of 16 processing elements (PEs)**, each integrating a **multiplier and an adder**, supporting MAC operations.  

- **Control Logic** – Manages **input, output, and mode selection** for dynamic operation configuration.  

- **Data Handling** – **Input logic** aligns data for **systolic computation**, **output logic** selects and shifts processed data, and **muxing logic** configures different operational modes.

## Processing Element (PE)
## Block Diagram

 <p align="center">
  <img width="1200" height="500" src="/Images/Screenshot 2025-03-13 122353.png">
</p>

 <p align="center">
  <img width="1200" height="500" src="/Images/Screenshot 2025-03-13 122353.png">
</p>

Functionality	Equation in Design	Control Signals
Multiply (MULT)	out = r2_s1 * r3_s1	m5 = 0, m4 = 0
Addition (ADD)	out = r2_s1 + r3_s1	m5 = 0, m4 = 1
Subtraction (SUB)	out = r2_s1 - r3_s1	m5 = 0, m4 = 1, sub_enable = 1
Multiply-Accumulate (MAC)	out = (m5 ? out : r1_s3) + (m4 ? m2_out : r2_s3)	m5 = 1, m4 = 1
Multiply-Add (MADD)	out = (r2_s1 * r3_s1) + side_in1	m5 = 0, m4 = 1, m6 = 1
Multiply-Subtract (MSUB)	out = (r2_s1 * r3_s1) - side_in1	m5 = 0, m4 = 1, m6 = 1, sub_enable = 1
Systolic Vector Dot Product	S_i = S_{i-1} + (r2_s1 * r3_s1)	m5 = 1, m4 = 1, systolic_mode = 1
Systolic FIR Mode	out = out + (side_in1 * r1_s1)	m5 = 1, m4 = 0, fir_mode = 1
Vector One Mode	side_out1 = r1_s1 * side_in1	m1 = 1, m7 = 0

- **Comprehensive Operations** – The Processing Element (PE) supports MAC, MULT, ADD/SUB, MADD/MSUB, DOT PRODUCT, and PE chaining, enabling efficient vector computations.

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

Define Tensor Slice role in AI acceleration (MACs, matrix ops).

Determine PE grid size (e.g., 4×4 systolic array).

Establish precision handling (FP32 core with reuse for BF16, FP16, INT16).

- 2️⃣ Processing Element (PE) & Compute Core Design

Design PE with MAC, DOT, MADD, ADD/SUB support.

Implement FP32 compute core with fixed-width operand reuse.

Optimize PE chaining for matrix-vector and matrix-matrix ops.

- 3️⃣ Tensor Slice Control & Dataflow Optimization

Design input/output logic for efficient operand routing.

Implement local crossbar for flexible interconnect.

Optimize pipelining & systolic data movement for high throughput.

- 4️⃣ Scalability & System Integration

Ensure Tensor Slice tiling for larger tensor computations.

Balance latency, throughput, and power efficiency.

Integrate into larger AI accelerator architectures with minimal overhead. 🚀

## Current Progress - Initial RTL design and iteration of PE completed 

## Processing Element (PE)
### RTL Design and Functional Verification

 <p align="center">
  <img width="1200" height="500" src="/Images/Screenshot 2025-03-13 122353.png">
</p>

 <p align="center">
  <img width="1200" height="500" src="/Images/Screenshot 2025-03-13 122353.png">
</p>

### Synthesis and Gate Level Simulation (GLS)

 <p align="center">
  <img width="1200" height="500" src="/Images/Screenshot 2025-03-13 122353.png">
</p>

 <p align="center">
  <img width="1200" height="500" src="/Images/Screenshot 2025-03-13 122353.png">
</p>


















