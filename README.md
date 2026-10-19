# FPGA Acceleration of 2D Image Convolution

A pipelined hardware implementation of 2D image convolution using hierarchical Vedic multiplication, designed for low-latency edge processing on a Xilinx Artix-7 FPGA.

## Overview

Software-based spatial convolution relies on repeated multiply-accumulate operations, which can introduce latency due to sequential computation and memory-access overhead. Our design accelerates this workload by implementing a parallel convolution datapath in FPGA logic.

The architecture explores Urdhva Tiryagbhyam (Vedic) multiplication and optimized accumulation structures to improve arithmetic performance while maintaining correct output values.

## Design Architecture

The datapath consists of the following main stages:

1. **Parallel Vedic multiplication** — Generates partial products using the Urdhva Tiryagbhyam principle.
2. **Carry-save accumulation** — Reduces multiple partial products while limiting carry-propagation overhead.
3. **Final adder stage** — Uses a parallel-prefix adder, including a Brent–Kung-based configuration, in the evaluated architecture.
4. **Pipelined convolution datapath** — Organizes arithmetic stages to support parallel 3×3 multiply-accumulate operations.
5. **Zero-operand isolation** — Bypasses unnecessary arithmetic activity for zero operands, targeting reduced switching activity without changing numerical functionality.

Multiple multiplier and adder configurations were evaluated, including carry-save reduction with Brent–Kung and Kogge–Stone adders, as well as ripple-carry-based accumulation.

## FPGA Platform and Tools

- **FPGA board:** Digilent Arty A7-100T
- **FPGA device:** Xilinx Artix-7 XC7A100T
- **RTL design:** Verilog
- **FPGA design tool:** Xilinx Vivado
- **Verification:** Python golden model
- **Image sensor integration:** OV7670 camera front-end

## Reported Hardware Results

The project presentation reports these implementation figures:

| Metric | Reported result |
|---|---:|
| LUT utilization | 2.01% |
| LUTs used | 1,275 |
| Flip-flops used | 389 |
| Critical-path delay | 5.725 ns |

The presentation also reports results for separate architecture variants. For example, one comparison lists 1,176 LUTs and 17.002 ns delay for a Vedic multiplier with CSA and Brent–Kung accumulation, while another lists 1,214 LUTs and 16.808 ns for a CSA/Kogge–Stone configuration. These are different configurations and should not be treated as measurements from the same build.

## Verification and Testing

A Python golden model is used as the reference for checking the hardware output. The design workflow compares convolution results to verify numerical correctness.

The reported evaluation also considers implementation resources, critical-path delay, and power. These metrics depend on the selected RTL configuration and the corresponding FPGA implementation run.

## Team

**Team Sutra-dhar**  
Track 1 — FPGA  
KLE Technological University, Hubballi

- Keerti Kallur
- Manoj H. A.
- Rutvik Malali

## Reference

The project presentation cites the following research paper for comparison:

[DOI: 10.1109/COMPSAC61105.2024.00071](https://doi.org/10.1109/COMPSAC61105.2024.00071)
