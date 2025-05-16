# Custom-design-of-a-Tensor-Slice-for-acceleration-ML-on-FPGA

📊 Progress: 🟩🟩🟩⬜⬜ (60%)  **(WORK PROGRESS AND RESULTS AT THE END OF THE DOCUMENTATION)**

## Overview
- Objective: Design and evaluate a novel tensor slice for accelerating ML and DL on FPGA's
- Technology Node: 32nm
- Design Flow: RTL to GDSII using Synopsys EDA Tools (inclduing DFT and STA )
- Target Application: Matrix Multiplication - Addition/Subtraction - Transpose , CNN and Transformer Applications 

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

## Key Features

- **Tensor Slice vs. DSP Slice** – A **Tensor Slice** is to deep learning what a **DSP Slice** is to digital signal processing, optimized for machine learning workloads.  

- **Supported Operations** – It accelerates **matrix-matrix and matrix-vector multiplications**, along with **element-wise (Eltwise) addition, subtraction, and multiplication**, crucial for fully connected, convolutional, and recurrent layers.  

- **Eltwise Operations** – Used in **normalization, residual connections, weight updates, and dropout layers**, enhancing efficiency in deep learning computations.  

- **Core Architecture** – Consists of a **2D array of 16 processing elements (PEs)**, each integrating a **multiplier and an adder**, supporting MAC operations.  

- **Control Logic** – Manages **input, output, and mode selection** for dynamic operation configuration.  

- **Data Handling** – **Input logic** aligns data for **systolic computation**, **output logic** selects and shifts processed data, and **muxing logic** configures different operational modes.


## I/O and Operation Overview

**Input and Output Ports**

| Signal           | Width  | Description                                | Signal         | Width | Description                      |
|------------------|--------|--------------------------------------------|----------------|-------|----------------------------------|
| `clk`            | 1      | System clock                               | `counter`      | 4     | Operation or cycle counter       |
| `done`           | 1      | Done signal (possibly from controller)     | `ready_transpose` | 1   | Indicates transpose is ready     |
| `rst`            | 1      | Active-high reset                          | `r1`           | 256   | Row 1 output result              |
| `op`             | 1      | Operation control signal                   | `r2`           | 256   | Row 2 output result              |
| `op2`            | 1      | Secondary operation control signal         | `r3`           | 256   | Row 3 output result              |
| `a_data`         | 128    | Input data for matrix A                    | `r4`           | 256   | Row 4 output result              |
| `b_data`         | 128    | Input data for matrix B                    | `out_transpose1` | 256 | Transposed output row 1          |
|                  |        |                                            | `out_transpose2` | 256 | Transposed output row 2          |
|                  |        |                                            | `out_transpose3` | 256 | Transposed output row 3          |
|                  |        |                                            | `out_transpose4` | 256 | Transposed output row 4          |

**Operation Table**

| `op` | `op2` | Operation                                 | Description                                                                 |
|------|-------|-------------------------------------------|-----------------------------------------------------------------------------|
| 0    | 0     | Matrix Multiplication                     | Performs matrix multiplication between `a_data` and `b_data`                |
| 1    | 0     | Matrix Addition/Subtraction               | Performs element-wise addition of `a_data` and `b_data`                     |
| x    | 1     | Matrix Transpose of Input A               | Transposes the matrix provided in `a_data` (column-wise input assumed)      |

## Methodology 

- **Tool Flow (Synopsys EDA Tools)**
- 1️⃣ RTL Design & Simulation/Verification – Verilog/SystemVerilog, Xilinx Vivado, VCS, Verdi
- 2️⃣ Synthesis & DFT Insertion – Design Compiler NXT , TestMAX
- 3️⃣ Static Timing Analysis (STA) – PrimeTime (PT)
- 4️⃣ Place & Route (PnR) – IC Compiler II (ICC2) 
- 5️⃣ Power Analysis & Signoff – PrimeTime-PX (PTPX)
- 6️⃣ Final GDSII Generation – ICC2, Calibre (DRC/LVS)

## Current Progress 

## 4x4 Systolic Array (can be extended to 8x8 , 16x16 as per requirement)

## RTL Design and Functional Verification

- **Schematic**
 <p align="center">
  <img width="1200" height="500" src="/Images/SYSTOLIC ARRAY SCHEMATIC.png">
</p>

- **IP Block**
<p align="center">
  <img width="1200" height="500" src="/Images/IP MID.png">
</p>


### Simulation

- **Inputs**
 <p align="center">
  <img width="1200" height="500" src="/Images/INPUTS.png">
</p>

- **Matrix-Matrix Multiplication**
 <p align="center">
  <img width="2000" height="300" src="/Images/MULT.png">
</p>

- **Matrix-Vector Multiplication**
 <p align="center">
  <img width="2000" height="300" src="/Images/MAT-VEC.png">
</p>

- **Matrix Addition**
 <p align="center">
  <img width="2000" height="300" src="/Images/ADD.png">
</p>

- **Matrix Transpose**
 <p align="center">
  <img width="2000" height="300" src="/Images/TRANS.png">
</p>




---------------------------------------------------------------------------------------------------------------------------------------------------------




















