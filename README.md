# Custom-design-of-a-Tensor-Slice-for-acceleration-ML-on-FPGA

📊 Progress: 🟩🟩🟩⬜⬜ (60%)  **(WORK PROGRESS AND RESULTS AT THE END OF THE DOCUMENTATION)**

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

 <p align="center">
  <img width="500" height="500" src="/Images/SYSTOLIC ARRAY FLOW.png">
</p>

### Overview

- **Tensor Slice vs. DSP Slice** – A **Tensor Slice** is to deep learning what a **DSP Slice** is to digital signal processing, optimized for machine learning workloads.  

- **Supported Operations** – It accelerates **matrix-matrix and matrix-vector multiplications**, along with **element-wise (Eltwise) addition, subtraction, and multiplication**, crucial for fully connected, convolutional, and recurrent layers.  

- **Eltwise Operations** – Used in **normalization, residual connections, weight updates, and dropout layers**, enhancing efficiency in deep learning computations.  

- **Core Architecture** – Consists of a **2D array of 16 processing elements (PEs)**, each integrating a **multiplier and an adder**, supporting MAC operations.  

- **Control Logic** – Manages **input, output, and mode selection** for dynamic operation configuration.  

- **Data Handling** – **Input logic** aligns data for **systolic computation**, **output logic** selects and shifts processed data, and **muxing logic** configures different operational modes. 

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


## Current Progress 

## Processing Element (PE) - Fixed Point 
### RTL Design and Functional Verification

- **SCHEMATIC**
 <p align="center">
  <img width="1200" height="500" src="/Images/SYSTOLIC ARRAY SCHEMATIC.png">
</p>

- **INPUTS**
 <p align="center">
  <img width="1800" height="500" src="/Images/INPUTS.png">
</p>

- **OUTPUTS**
 <p align="center">
  <img width="1800" height="500" src="/Images/OUTPUT.png">
</p>

- **FULL SIMULATION**
 <p align="center">
  <img width="1200" height="500" src="/Images/SIM.png">
</p>


---------------------------------------------------------------------------------------------------------------------------------------------------------




















