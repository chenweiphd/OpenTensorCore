# OpenTensorCore
![image-20251124190550818](https://raw.githubusercontent.com/chenweiphd/typopic/master/image-20251124190550818.png)

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

![image-20251124190838829](https://raw.githubusercontent.com/chenweiphd/typopic/master/image-20251124190838829.png)

 

| **分类**         | **模块**                                            | **描述**                                                     |
| ---------------- | --------------------------------------------------- | ------------------------------------------------------------ |
| **顶层**         | tensor_core.v                                       | 张量核心顶层设计模块                                         |
|                  | tensor_core_exev.v                                  | 张量核心顶层设计模块(最顶层)，多线程调度和结果汇总           |
| **格式转换**     | to_fp8_con.v                                        | FP8格式转换器（顶层接口）                                    |
|                  | to_fp8_core.v                                       | FP8格式转换核心逻辑模块(最后是转化为FP9)                     |
|                  | fp22_to_fp8_con.v                                   | FP22格式到FP8格式的专用转换器                                |
| **张量计算**     | tc_dot_product.v                                    | 点积计算单元（核心计算模块）                                 |
|                  | tc_mul_pipe.v                                       | 乘法流水线模块，3级流水线，集成fmul_s1/2/3                   |
|                  | tc_add_pipe.v                                       | 加法流水线模块，2级流水线                                    |
|                  | naivemultiplier.v                                   | 基础尾数乘法器（组合逻辑）                                   |
| **浮点运算单元** | fadd_s1.v                                           | 浮点加法器第一阶段，接收tc_mul的累加中间值，操作数对齐和特殊值处理 |
|                  | fadd_s2.v                                           | 浮点加法器第二阶段，尾数相加和结果规格化                     |
|                  | fmul_s1.v                                           | 浮点乘法器第一阶段，接收来自tc_mul_pipe的输入，指数处理和尾数预处理 |
|                  | fmul_s2.v                                           | 浮点乘法器第二阶段，调用naivemultiplier.v生成部分积，进行压缩处理 |
|                  | fmul_s3.v                                           | 浮点乘法器第三阶段，最终结果规格化和舍入，输出到tc_mul_pipe的结果总线 |
| **辅助计算**     | [cf_math_pkg.sv](http://cf_math_pkg.sv)             | 数学函数和常数定义包                                         |
|                  | [norm_div_sqrt_mvp.sv](http://norm_div_sqrt_mvp.sv) | 支持乘法的归一化/除法/平方根计算模块                         |
| **存储单元**     | singleport_SRAM.v                                   | 单端口SRAM存储器模型                                         |
| **配置与控制**   | config_registers.v                                  | 配置寄存器模块                                               |
| **项目支撑**     | [define.sv](http://define.sv)                       | 全局宏定义文件                                               |
|                  | filelist.f                                          | 项目文件列表（用于编译/仿真）                                |
|                  | run.tcl                                             | 自动化运行脚本                                               |
| **日志文件**     | command.log                                         | 命令执行历史日志                                             |
|                  | scl.log                                             | 脚本运行日志                                                 |
| **其他文件**     | default.svf                                         | 默认配置文件                                                 |
|                  | qrd_mvp.pvk                                         | 未知                                                         |



#### **Related Resources**

|                                                              |                                                              |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [OpenRVGPUCourese](https://github.com/chenweiphd/OpenRVGPUCourse) | OpenRVGPUCourse aims to achieve entry into RISC-V GPU architecture in the shortest path while exploring the application potential of technologies such as 2.5D/3D/3.5D Chiplet and open-source EDA in open-source GPUs |
| [Ventus](https://github.com/THU-DSP-LAB/ventus-gpgpu)        | GPGPU processor supporting RISCV-V extension, developed with Chisel HDL. |
| [Vortex](https://github.com/vortexgpgpu/vortex)              | Vortex is a full-stack open-source RISC-V GPGPU. Vortex supports multiple **backend drivers**, including our C++ simulator (simx), an RTL simulator, and physical Xilinx and Altera FPGAs-- all controlled by a single driver script |
|                                                              |                                                              |
|                                                              |                                                              |
|                                                              |                                                              |
|                                                              |                                                              |
|                                                              |                                                              |



![image](https://raw.githubusercontent.com/chenweiphd/typopic/master/502929102-39c5d16e-9783-49a6-b9ed-25f4c363db97.png)