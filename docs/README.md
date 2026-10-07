# Project Documentation

This folder contains the technical documentation for the IEEE-754 floating-point adder project.

## Project

**Power-Optimized IEEE-754 Compliant Floating-Point Adder Using Dual-Path Architecture and Explicit Operand Isolation with Static Timing Analysis Validation**

The project compares a conventional single-path IEEE-754 floating-point adder with an optimized dual-path implementation.

## Design Approach

The improved design uses:

1. Early handling of special IEEE-754 cases.
2. FAR/CLOSE path selection based on exponent difference and effective subtraction.
3. A FAR datapath optimized for operands with large exponent separation.
4. A CLOSE datapath optimized for near-equal subtraction and cancellation.
5. Parallel leading-zero detection in the CLOSE path.
6. Explicit operand isolation to stop unnecessary switching in the inactive datapath.
7. A shared Round-to-Nearest-Even stage.

## Verification

Cadence Xcelium and SimVision were used for functional simulation and activity analysis.

The verification set contained 1,000 randomized vectors and four directed cases:

- `0 + 1`
- `1 + 0`
- `+∞ + 1`
- `1 + (-1)`

Total vectors: **1,004**

No functional errors were observed for the tested vectors.

A VCD activity dump was generated for switching-activity analysis. The activity data was used during Genus power analysis instead of relying only on a default statistical switching assumption.

## Synthesis Setup

Cadence Genus 21.14-s082_1 was used with a GPDK 45 nm technology library.

- PVT: 1.1 V, 0°C
- Configuration: balanced_tree
- Optimization effort: area-power balance
- Clock period: 5.0 ns
- Target frequency: 200 MHz
- Input delay: 0.5 ns
- Output delay: 0.5 ns

32-bit DFF register banks were included at the A, B, and RESULT interfaces to create meaningful register-to-register timing paths for STA.

## Results

| Metric | Baseline | Improved |
|---|---:|---:|
| Cell count | 1,077 | 894 |
| Cell area | 1978.8 µm² | 1682.3 µm² |
| Switching power | 95.02 µW | 84.65 µW |
| Leakage power | 1.063 µW | 0.941 µW |
| Internal power | 233.08 µW | 242.84 µW |
| Total power | 329.16 µW | 328.43 µW |
| Critical path delay | 3218 ps | 3336 ps |
| Setup slack | +1669 ps | +1550 ps |

The improved design reduces switching power by 10.9% and cell area by 15.0%. It remains timing-closed at 200 MHz with +1550 ps setup slack.

## Project Files

- `src/` contains the baseline and optimized Verilog implementations.
- `testbench/` contains the functional testbenches.
- `synthesis/` contains Genus scripts and SDC constraints.
- `docs/` contains project documentation.

The conference paper and full project report are not duplicated here because their binary source files were not available for direct transfer into this repository.

## Publication

The work was accepted and presented at **IEEE SPAC-AID 2026**, IEEE Madhya Pradesh Section conference, 11–12 September 2026.
