# Five-Stage RISC-V Processor

## Project Overview

A VHDL processor project comparing single-cycle and five-stage pipelined implementations. The design explores instruction overlap, data forwarding, hazard handling, and the FPGA resource and clock-frequency cost of pipelining.

## Main Features

- IF, ID, EX, MEM, and WB stages with interstage registers.
- Forwarding from EX/MEM and MEM/WB, prioritizing the newer result and excluding register `x0`.
- Load-use stalling and control-flow flushing.
- Branch and jump resolution in the MEM stage.
- Separate 8 KiB instruction and 8 KiB data memories.
- Stage-level instruction/PC visibility and cycle, stall, and flush counters.

## Architecture

The pipelined variant distributes the datapath across five stages. A hazard unit controls stalls, forwarding supplies recent operands, and flushing discards instructions after a control-flow redirect. Multiplication is split across EX and MEM using partial products from the lower 16 operand bits.

![Pipelined processor RTL](https://github.com/omarwatt/Five-Stage-RISC-V-Processor/blob/main/DOC/images/pipeline-rtl.png?raw=true)
*The report’s RTL view shows the processor blocks and interstage register banks.*

## Tools and Technologies

VHDL, Intel Quartus Prime 25.1, ModelSim/Questa, Intel FPGA memory primitives, and Cyclone V. The report also uses RARS as a software reference for program behavior.

## Simulation and Results

The report contains representative simulation and hardware-debug captures. The supplied testbenches provide directed stimulus rather than a complete automated ISA regression.

Archived results from `DOC/pre5.pdf`:

| Metric | Single-cycle | Pipelined |
| --- | ---: | ---: |
| Fitter ALMs | 1,448 | 1,705 |
| Fitter registers | 1,314 | 2,024 |
| MCLK Fmax | 34.99 MHz | 98.72 MHz |

The pipelined design raises reported maximum clock frequency while increasing resources. These figures do not establish application execution-time speedup.

![Pipeline simulation](https://github.com/omarwatt/Five-Stage-RISC-V-Processor/blob/main/DOC/images/pipeline-waveform.png?raw=true)
*The IF, ID, EX, MEM, and WB program counters expose pipeline progression; STCNT and FHCNT show accumulated stalls and flushes.*

![Pipeline timing result](https://github.com/omarwatt/Five-Stage-RISC-V-Processor/blob/main/DOC/images/pipeline-timing.png?raw=true)
*Quartus reports an MCLK Fmax of 98.72 MHz for the archived implementation.*

![Register-file critical path](https://github.com/omarwatt/Five-Stage-RISC-V-Processor/blob/main/DOC/images/register-file-critical-path.png?raw=true)
*The technology-map view traces memwb_regwrite_q through the IDECODE decoder to RF_q, locating the reported register-file write timing path.*

![Pipelined data-memory readback](https://github.com/omarwatt/Five-Stage-RISC-V-Processor/blob/main/DOC/images/dtcm-results.png?raw=true)
*The archived DTCM readback shows the benchmark’s stored result pattern. The report compares it with the single-cycle result and software reference.*

## Repository Structure

- `DUT/RV32IM_sc/`: single-cycle processor sources.
- `DUT/RV32IM_pipeline/`: pipelined processor sources.
- `TB/`: testbenches for the two variants.
- `DOC/`: project report.
- `docs/images/`: five selected design and results figures.
- `QUARTUS/`: archived implementation/debug artifacts.

## How to Run

1. Create a separate simulator project or work library for each variant: their entity and package names overlap.
2. Enable VHDL-2008 and configure Intel's `altera_mf` simulation library.
3. Set the selected variant's `G_MODELSIM` constant to `1`.
4. Supply matching instruction/data initialization images and replace the absolute paths in the memory sources.
5. Compile the selected variant's packages and DUT files in dependency order, then its testbench. Elaborate and inspect stage signals, register writes, and memory results.

## Limitations and Attribution

Course project by **Omar Wattad**, based on the supplied course framework. Preserve the original source headers and course attribution.
