# Custom-design-of-a-Tensor-Slice-for-acceleration-ML-on-FPGA
Custom design and evaluation of a Tensor Block for accelerating ML and DL processes on FPGA

## Introduction

FPGAs have evolved to accelerate deep learning (DL) by integrating specialized hardware, such as AI processing engines, low-precision arithmetic (8-bit fixed-point, FP16), and shadow multipliers. A recent advancement in this domain is **Tensor Slices**, which enhance FPGA compute density by embedding **systolic arrays** for efficient tensor operations. These slices support multiple precision formats and can be dynamically reconfigured into independent multipliers and MAC units. A local crossbar at the inputs reduces routing congestion, improving performance. Building on this concept, I aim to **custom design a Tensor Slice**, optimizing its architecture for higher efficiency, flexibility, and seamless FPGA integration while maintaining fine-grained programmability for other applications.
