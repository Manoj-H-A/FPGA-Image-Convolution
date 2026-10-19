# FPGA Acceleration of 2D Image Convolution

**A pipelined FPGA convolution engine using hierarchical Vedic multiplication for low-latency edge processing.**

Sutra-dhar explores hardware acceleration of spatial image convolution on a **Xilinx Artix-7 FPGA (Arty A7-100T, XC7A100T)**. The design combines Urdhva Tiryagbhyam (Vedic) multiplication with multi-operand accumulation structures to reduce arithmetic delay while preserving bit-exact results.

> **Project status:** Academic / hackathon prototype. Update the build instructions and attach the final Vivado reports before treating the numbers below as reproducible release results.

## Overview

Software-based spatial convolution can be limited by sequential multiply-accumulate operations and memory-access overhead. Sutra-dhar moves the compute-intensive datapath into FPGA logic and explores parallel arithmetic structures for a pipelined convolution engine.

### Key features

- **Vedic multiplication:** Uses the Urdhva Tiryagbhyam principle to generate partial products.
- **Fast accumulation:** Evaluates carry-save accumulation and parallel-prefix addition (including Brent–Kung-style reduction).
- **Pipelined datapath:** A parallel 3×3 multiply-accumulate (MAC) datapath targets higher-throughput convolution.
- **Zero-operand isolation:** Bypasses unnecessary arithmetic activity for zero operands, targeting lower switching activity without changing the numerical result.
- **Camera integration:** The broader project integrates an OV7670 camera front-end with FPGA-side image processing.
- **Golden-model verification:** Compares hardware outputs against a Python reference model to check numerical correctness.

## Architecture

```text
Image input / test image
        |
        v
Input pixels and kernel values
        |
        v
Parallel Vedic multiplier lanes
(Urdhva Tiryagbhyam)
        |
        v
Partial-product / multi-operand reduction
(CSA tree)
        |
        v
Final carry-propagate adder
(Brent–Kung / other evaluated variants)
        |
        v
Pipelined convolution output
        |
        v
Python golden-model comparison
```

The repository should document the exact final configuration used for each published result. The presentation evaluates multiple multiplier/adder combinations, so results from different variants should not be compared as if they came from the same build.

## Hardware and tools

| Item | Details |
|---|---|
| FPGA platform | Arty A7-100T |
| FPGA device | Xilinx Artix-7 XC7A100T |
| RTL | Verilog |
| FPGA toolchain | Xilinx Vivado |
| Reference / verification | Python golden model |
| Image sensor integration | OV7670 camera module (project integration) |

## Reported implementation results

The project presentation reports the following implementation figures. Confirm that each figure is from the same final RTL revision and Vivado run before using them as a formal benchmark.

| Metric | Reported value |
|---|---:|
| LUT utilization | 2.01% |
| LUTs used | 1,275 |
| Flip-flops used | 389 |
| Critical-path delay reported for the final project | 5.725 ns |

The presentation also includes a separate architecture comparison with different delay values for several Vedic multiplier and adder variants. Those figures are configuration-specific and should be read from the corresponding synthesis/implementation reports.

**Reproducibility note:** FPGA timing, resource usage, and power depend on the selected RTL variant, constraints, Vivado version, and implementation settings. Add the final `timing_summary`, `utilization`, and `power` reports (or screenshots) to the repository to make the measurements independently verifiable. Do not infer operating frequency from a delay number without checking the clock constraints and timing report.

## Verification

The design uses a Python golden model as the reference for output checking.

Recommended checks to include with the repository:

1. Run the RTL simulation on a small, known image and kernel.
2. Compare each output pixel against the Python reference.
3. Test boundary cases, including zero-valued inputs and maximum supported pixel/kernel values.
4. Record the exact pixel width, coefficient width, accumulator width, signedness, rounding, and overflow behavior.
5. Save simulation logs and the Vivado timing/utilization reports for the same source revision.

## Repository structure

Adjust this tree to match the files you publish:

```text
Sutra-dhar/
├── rtl/                 # Verilog modules
├── sim/                 # Testbenches and simulation files
├── python/              # Golden model and verification scripts
├── constraints/         # FPGA pin and timing constraints
├── reports/             # Vivado timing, utilization and power reports
├── images/              # Architecture diagrams and demo screenshots
└── README.md
```

## Getting started

The exact commands depend on the source files and Vivado project included in the repository.

1. Clone the repository:
   ```bash
   git clone <YOUR_REPOSITORY_URL>
   cd Sutra-dhar
   ```
2. Open the Vivado project (`.xpr`) if you publish one, or create a project targeting the **Arty A7-100T / XC7A100T** device.
3. Add the RTL sources, testbench files, and the correct XDC constraints.
4. Run RTL simulation and compare results with the Python golden model.
5. Run synthesis and implementation, then review timing and utilization reports.
6. For camera-based testing, connect the OV7670 module according to the published pin constraints and wiring documentation.

> Replace the placeholder repository URL and update these steps to match the files actually included in your GitHub repository. Do not commit generated Vivado run directories unless you intentionally want to publish them.

## Demo

Add a short demo video or GIF showing the camera/input path and processed output, plus screenshots of:
- the hardware setup,
- the simulation or Python comparison,
- the Vivado timing summary,
- the utilization report.

## Team

**Team Sutra-dhar**  
Track 1 — FPGA  
KLE Technological University, Hubballi

- Keerti Kallur
- Manoj H. A.
- Rutvik Malali

## Reference

The project presentation references the following paper for comparison:

- DOI: [10.1109/COMPSAC61105.2024.00071](https://doi.org/10.1109/COMPSAC61105.2024.00071)

## Acknowledgements

Developed as an academic FPGA / Edge AI project at KLE Technological University, Hubballi.
