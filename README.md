# Hardware Acceleration of 2D Image Convolution using Hierarchical Vedic Multiplication

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
```

int this also used sutr dhar chaneg that

### Mixed-Signed Vedic Multiplier

- **Pixel (A):** Unsigned 8-bit (0 to 255)
- **Weight (B):** Signed 8-bit (-128 to +127)
- **Sparsity-Aware Design:** Zero-detection and operand isolation disable internal switching when inputs are zero, reducing dynamic power.
- **Hierarchical Design:** Built using `2×2→4×4→8×8` Vedic sub-modules.
- .

---

## 📊 Hardware Results & Comparisons

### 1. Implementation Comparison

| **Implementation**                 | **Area (LUTs)** | **Delay (ns)** | **ADP (Area-Delay Product)** |
| ---------------------------------- | --------------- | -------------- | ---------------------------- |
| Baseline/DSP                       | 0               | 22.756         | —                            |
| Conv-Vedic-KS                      | 1,755           | 25.293         | 44,389                       |
| Conv-Vedic-RCA                     | 1,223           | 22.682         | 27,740                       |
| **Conv-Vedic-CSA_BK (Our Design)** | **1,176**       | **17.002**     | **19,994**                   |
| VEDIC_CSA-KS                       | 1,214           | 16.808         | 20,404                       |

### 2. Reference Paper Comparison

| **Parameter**           | **Reference Paper (VM)** | **Our Design**       | **Comparison**            |
| ----------------------- | ------------------------ | -------------------- | ------------------------- |
| **LUT Utilization**     | 79 LUTs                  | **50 LUTs**          | **36.7% fewer LUTs**      |
| **Critical Path Delay** | 6.56 ns                  | **5.725 ns**         | **12.7% lower delay**     |
| **Operating Frequency** | 150 MHz                  | **100 MHz**          | Target constraint         |
| **Power**               | 224.72 mW                | **109 mW**           | **Low power utilization** |
| **FPGA / Technology**   | Zynq UltraScale XCZU2CG  | **Artix-7 XC7A100T** | Different technologies    |

### 3. Hardware Resource Utilization (Artix-7 XC7A100T)

| **Resource**         | **Used**  | **Utilization**       |
| -------------------- | --------- | --------------------- |
| **LUTs**             | **1,275** | **2.01%** (of 63,400) |
| **Flip-Flops (FFs)** | **389**   | —                     |
| **LUTRAM**           | **66**    | —                     |

### 4. Power Analysis (Vivado)

| **Metric**              | **With Sparse Optimization** | **Without Sparse Optimization** |
| ----------------------- | ---------------------------- | ------------------------------- |
| **Total On-Chip Power** | **0.121 W**                  | **0.109 W**                     |
| **Dynamic Power**       | 0.024 W (20%)                | 0.012 W (11%)                   |
| **Device Static**       | 0.097 W (80%)                | 0.097 W (89%)                   |
| **Junction Temp**       | 25.5 °C                      | 25.5 °C                         |

---

## 🎛️ Runtime Configurable Kernels

The MAC engine supports runtime kernel selection via physical switches (`SW[1:0]`):

- `00` = **Sobel X** (Vertical Edge Detection)
- `01` = **Sobel Y** (Horizontal Edge Detection)
- `10` = **Blur** (Smoothing)
- `11` = **Sharpen** (Edge Enhancement)

---

## 🧪 Software Verification & Test Results

The design is verified by streaming image tiles to the FPGA over UART, processing, and comparing the results bit-by-bit with a **Python Golden Model** (using NumPy).

- **Test Image Size:** 445 × 466 pixels
- **FPGA Tile Size:** 8 × 8
- **Total Tiles Processed:** **5,850 / 5,850**
- **Result:** Visual and numerical **FUNCTIONAL VERIFICATION: PASS** (Bit-exact match across all kernels).

---

## 🛠️ Technologies Used

- **Hardware:** Xilinx Artix-7 (Arty A7-100T), OV7670 Camera Module
- **RTL Design:** Verilog HDL
- **EDA Tools:** Xilinx Vivado (Synthesis, Implementation, Timing, Power Analysis)
- **Verification:** Python 3 (NumPy, PySerial), Vivado Simulator
- **Protocols:** UART (115200 baud), SCCB (I2C for Camera)
