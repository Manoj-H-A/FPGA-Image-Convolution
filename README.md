# 🚀 Sutra-dhar: Hardware Acceleration of 2D Image Convolution using Hierarchical Vedic Multiplication

![Platform](https://img.shields.io/badge/Platform-Xilinx%20Artix--7%20XC7A100T-blue)
![Language](https://img.shields.io/badge/Language-Verilog%20HDL-orange)
![Tool](https://img.shields.io/badge/EDA-Vivado%20%7C%20Python-yellow)
![Domain](https://img.shields.io/badge/Domain-Edge%20AI%20%7C%20FPGA%20Acceleration-brightgreen)

## 📌 Overview
**Sutra-dhar** is a highly parallelized 2D Convolution Core implemented on the Xilinx Artix-7 FPGA. Standard software-based spatial convolution on embedded CPUs introduces significant latency due to sequential loop execution (\(O(N^2 \cdot K^2)\)). 

To overcome this, this project leverages **Urdhva Tiryagbhyam (Vedic) multiplication** alongside a **Carry-Save Adder (CSA) tree** and a **Brent-Kung prefix adder**. The architecture is fully pipelined to maximize arithmetic throughput, reduce multi-operand accumulation delay, and prove exact bit-level equivalence against a Python Golden Model.

---

## ✨ Key Innovations
1. **9-Way Parallel Vedic Multiplication:** Executes 9 mixed-signed multiplications (8-bit Unsigned Pixel × 8-bit Signed Weight) simultaneously.
2. **CSA 9→2 Carry-Save Compression:** Uses a 4-layer tree (9→6→4→3→2) to postpone carry propagation, drastically reducing the critical path.
3. **Pipelined Multiplier → CSA → CPA Datapath:** A 3-stage pipeline (R1, R2, R3) that allows continuous data flow.

---

## 🏗️ System Architecture

The design supports both a live camera input and UART-based PC input.

### System Level (Level 1)
* **Input:** 8×8 Image via UART RX or OV7670 Camera (RGB565 → Grayscale → 8×8 Point Sampling).
* **Control:** Kernel Selection / Control FSM.
* **Compute:** 3×3 Sliding Convolution MAC Engine.
* **Output:** 20-bit Convolution Result → 8-bit Output via UART TX to Python Visualization.

### MAC Engine Microarchitecture (Level 2)
```text
[8x8 Image] -> [10x10 Zero Padding] -> [3x3 Window]
      |
      v
[9x Vedic 8x8 Multipliers] -> [R1: 9 Product Registers] (Pipeline Stage 1)
      |
      v
[CSA Tree: 9->6->4->3->2] -> [R2: Sum/Carry Registers] (Pipeline Stage 2)
      |
      v
[20-bit Brent-Kung Adder] -> [R3: 20-bit Output Register] (Pipeline Stage 3)
