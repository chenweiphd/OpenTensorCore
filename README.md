# OpenTensorCore
## Introduction

OpenTensorCore, our groundbreaking open-source IP core designed to rival NVIDIA's Tensor Cores in AI acceleration. This IP core leverages RISC-V architecture to deliver high-performance tensor operations, particularly for deep learning workloads. It supports advanced features like low-precision computing, making it suitable for edge devices and data centers alike.

OpenTensorCore is an open-source intellectual property (IP) core designed specifically for tensor-based computations in artificial intelligence (AI) and machine learning (ML) applications. Which focuses on expanding the RISC-V open-source ecosystem, this IP core aims to provide high-throughput matrix/tensor operations comparable to proprietary solutions like NVIDIA's Tensor Cores, but with the advantages of openness, customizability, and royalty-free licensing. It integrates seamlessly with RISC-V processors, custom instructions for efficient handling of deep neural network (DNN) workloads, such as general matrix multiplications (GEMM), convolutions, and transformer-based operations.

The project emerged from the need to address the limitations of closed-source AI hardware, where customization is restricted and costs are high. By leveraging RISC-V's modular design principles, OpenTensorCore enables developers to tailor the core for specific applications, from edge AI devices with power constraints to high-performance computing (HPC) clusters. As of November 2025, with RISC-V gaining traction in AI (e.g., in automotive and networking sectors), OpenTensorCore positions itself as a key enabler for energy-efficient, scalable computing. It supports a range of precision formats, optimizing for both training and inference in large language models (LLMs) like those based on GPT or Llama architectures.

**Key Points:**

​                ● OpenTensorCore is among the pioneering open-source solutions for efficient AI hardware, potentially accelerating computations by factors similar to proprietary systems while maintaining openness.

​                ● Its FP8 support and mixed-precision capabilities reduce power consumption and latency, though debates exist on precision trade-offs in large language models (LLMs).

​                ● The evidence leans toward area efficiency through merged INT/FP units, validated on benchmarks, but some experts highlight integration challenges in diverse RISC-V ecosystems.

## **Advantages and Features**

OpenTensorCore stands out as the first open-source Tensor Core with native FP8 data format support, enabling compact models without significant accuracy loss. Its architecture natively handles mixed-precision computations, minimizing data format conversion delays. By merging integer (INT) and floating-point (FP) units, it reduces core area while proving feasible for large model computations.

 

 

| Feature         | Description                                     | Advantage                                      |
| --------------- | ----------------------------------------------- | ---------------------------------------------- |
| FP8 Support     | Native E5M2/E4M3 formats with block scaling     | Reduces memory by 4x, power by similar factors |
| Mixed Precision | Seamless FP8/FP16/FP32 with no casting overhead | Latency drop of 20-50%                         |
| INT/FP Merge    | Shared logic for INT8 scales and FP ops         | 30% area reduction, 5% overhead                |
| Customizability | RISC-V RVV integration                          | Community-driven extensions                    |

 

## **IP Core Architecture**

At its heart, OpenTensorCore employs a pipeline-based architecture. The core comprises three main layers:

​                ● **Control Layer**: Manages instruction decoding and scheduling using custom ISA extensions compitible with GEMM.

​                ● **Compute Layer**: A vector-tensor unit (VTU) with dot-product accumulators, handling FP8/FP16 in parallel arrays. It uses early float-point accumulation for precision preservation and stochastic rounding.

​                ● **Memory Interface Layer**: Features asynchronous access, compatible with SRAM compilers like OpenXRAM, ensuring low-latency data streaming.

The design incurs minimal overhead and will future supports extensions for formats like INT4 and FP4.

 

#### **Related Resources**

 