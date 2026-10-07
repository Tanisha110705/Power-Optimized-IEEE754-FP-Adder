# Power-Optimized IEEE-754 Single-Precision Floating-Point Adder

A power- and area-optimized 32-bit IEEE-754 single-precision floating-point adder using a dual-path FAR/CLOSE architecture and explicit operand isolation.

## Project Overview

This project implements and evaluates an IEEE-754 compliant floating-point adder with two main architectural improvements:

- **Dual-path FAR/CLOSE architecture** for operands with different exponent separations.
- **Explicit operand isolation** to reduce unnecessary switching activity in inactive datapaths.

The design was simulated using Cadence Xcelium/SimVision and evaluated through Cadence Genus synthesis and static timing analysis.

## Key Results

| Metric | Result |
|---|---:|
| Dynamic power reduction | **11%** |
| Cell area reduction | **15%** |
| Setup slack | **+1550 ps** |
| Verification vectors | **1,004** |
| Functional errors | **0** |
| Technology | **GPDK 45 nm** |

The verification set contains **1,000 random vectors** and **4 corner cases**.

## Architecture

The adder uses two datapath cases:

- **FAR path** for operands with a large exponent difference.
- **CLOSE path** for operands with closely aligned exponents.

Explicit operand isolation limits unnecessary switching in inactive logic.

The datapath covers IEEE-754 single-precision floating-point addition, including operand handling, alignment, significand operation, normalization, rounding, and result packing.

## Verification

Simulation used:

- Cadence Xcelium
- Cadence SimVision

A total of **1,004 test vectors** were evaluated:

- 1,000 random vectors
- 4 corner cases:
  - 0 + 1
  - 1 + 0
  - infinity + 1
  - 1 + (-1)

No functional errors were observed for the tested vectors.

## Synthesis and Timing

Synthesis used:

- Cadence Genus **21.14-s082_1**
- GPDK **45 nm**
- PVT: **1.1 V, 0°C**
- Balanced-tree configuration
- Area-power balance effort

The implementation uses 32-bit DFF registers for A, B, and RESULT, enabling register-to-register timing analysis.

Real VCD activity was used for switching activity analysis rather than relying on the default activity assumption.

## Team Project and Documentation

This repository contains the shared team implementation for the project. The original team documentation, paper, and project report are available in the public repository maintained by teammate [Ashit Raj](https://github.com/ashitraj634/Power-Optimized-IEEE754-fp-Adder).

## Repository Structure

```
Power-Optimized-IEEE754-FP-Adder/
├── README.md
├── src/
│   └── README.md
├── testbench/
│   └── README.md
├── synthesis/
│   └── README.md
└── docs/
    └── README.md
```

## Publication

The work was accepted and presented at **IEEE SPAC-AID 2026**, IEEE Madhya Pradesh Section conference, 11–12 September 2026.

## Tools

**Cadence Xcelium · SimVision · Genus · Static Timing Analysis · GPDK 45 nm**

## Note

Proprietary Cadence libraries, foundry PDK files, generated databases, and restricted design files are not included.
