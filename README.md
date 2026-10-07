# Power-Optimized IEEE-754 Single-Precision Floating-Point Adder

A 32-bit IEEE-754 single-precision floating-point adder designed to reduce unnecessary switching activity and cell area using a dual-path FAR/CLOSE architecture and explicit operand isolation.

## Overview

Floating-point addition requires exponent comparison, mantissa alignment, addition or subtraction, leading-zero detection, normalization, rounding, and result packing. A conventional single-path implementation keeps large alignment and normalization logic active even when the input operands do not require those operations.

This design separates the datapath into FAR and CLOSE cases based on exponent difference and effective subtraction. Logic in the inactive path is explicitly isolated to reduce switching activity.

## Architecture

### FAR Path

Used when the exponent difference is greater than one.

- Aligns the smaller mantissa using an extended alignment shifter.
- Performs addition or subtraction on extended mantissas.
- Requires at most a 1-bit normalization adjustment.
- Avoids a full normalization barrel shifter.

### CLOSE Path

Used for effective subtraction when the exponent difference is at most one.

- Requires at most a 1-bit alignment shift.
- Uses a parallel leading-zero detector for cancellation cases.
- Normalizes the result according to the detected leading-zero count.

### Early Exception Bypass

Zero, infinity, and NaN-related cases are identified before the main arithmetic datapath. These cases can bypass unnecessary datapath activity and directly produce the appropriate IEEE-754 result.

### Explicit Operand Isolation

The inactive FAR or CLOSE datapath receives clamped inputs so that internal nodes do not continue switching when that path is not selected.

## IEEE-754 Single Precision

The design operates on the 32-bit single-precision format:

- 1 sign bit
- 8 exponent bits
- 23 fraction bits
- Implicit leading significand bit for normalized operands
- Round-to-Nearest-Even handling using guard, round, and sticky information

## Verification

Functional verification and switching-activity analysis were performed using Cadence Xcelium and SimVision.

**Test set:**

- 1,000 randomized 32-bit input vectors
- 4 directed corner cases
- Total: **1,004 vectors**
- Functional errors observed: **0**

Directed cases included:

- `0 + 1`
- `1 + 0`
- `+∞ + 1`
- `1 + (-1)`

A VCD activity dump was used for power analysis so that switching estimates were based on simulated activity rather than a default statistical activity assumption.

## Synthesis and STA

The designs were synthesized and analyzed using Cadence Genus.

| Parameter | Value |
|---|---|
| Tool | Cadence Genus 21.14-s082_1 |
| Technology | GPDK 45 nm |
| PVT | 1.1 V, 0°C |
| Configuration | balanced_tree |
| Optimization | area-power balance |
| Clock | 200 MHz |
| Clock period | 5.0 ns |
| Input/output delay | 0.5 ns |

32-bit DFF register banks were used at the A, B, and RESULT interfaces to establish register-to-register timing paths for static timing analysis.

## Results

### Area

| Metric | Baseline | Improved | Change |
|---|---:|---:|---:|
| Cell count | 1,077 | 894 | -17.0% |
| Cell area | 1978.8 µm² | 1682.3 µm² | **-15.0%** |
| Wrapper total area | 2569.8 µm² | 2273.3 µm² | -11.5% |

### Power

| Metric | Baseline | Improved | Change |
|---|---:|---:|---:|
| Switching power | 95.02 µW | 84.65 µW | **-10.9%** |
| Leakage power | 1.063 µW | 0.941 µW | -11.5% |
| Internal power | 233.08 µW | 242.84 µW | +4.2% |
| Total power | 329.16 µW | 328.43 µW | -0.2% |

The main power improvement is in switching power, which reflects the reduced activity in the inactive datapath through operand isolation.

### Timing

| Metric | Baseline | Improved |
|---|---:|---:|
| Critical path delay | 3218 ps | 3336 ps |
| Setup slack | +1669 ps | **+1550 ps** |
| Timing closure | MET | MET |

The improved design has a small timing penalty from the final path-selection multiplexer, while retaining positive setup slack at 200 MHz.

## Key Project Results

- **10.9% switching-power reduction**
- **15.0% cell-area reduction**
- **+1550 ps setup slack**
- **1,004 verification vectors**
- **0 functional errors in the tested vectors**
- **200 MHz timing closure**

## Repository Structure

```
Power-Optimized-IEEE754-FP-Adder/
├── README.md
├── src/
│   ├── fp_adder_baseline.v
│   └── fp_dual_path_adder.v
├── testbench/
│   ├── tb_fp_adder_baseline.v
│   └── tb_fp_adder_core.v
├── synthesis/
│   ├── baseline/
│   │   └── scripts/
│   │       ├── run_synth.tcl
│   │       └── constraints.sdc
│   └── improved/
│       └── scripts/
│           ├── run_synth.tcl
│           └── constraints.sdc
└── docs/
    └── README.md
```

## Simulation

Cadence Xcelium can be used to simulate the baseline and improved designs:

```bash
xrun testbench/tb_fp_adder_baseline.v src/fp_adder_baseline.v -access +rwc -gui

xrun testbench/tb_fp_adder_core.v src/fp_dual_path_adder.v -access +rwc -gui
```

## Synthesis

Cadence Genus scripts and SDC constraints are provided under the `synthesis/` directory.

```bash
cd synthesis/baseline/scripts
genus -f run_synth.tcl

cd ../../improved/scripts
genus -f run_synth.tcl
```

## Publication

The work was accepted and presented at **IEEE SPAC-AID 2026**, IEEE Madhya Pradesh Section conference, 11–12 September 2026.

## Tools

**Verilog HDL · Cadence Xcelium · SimVision · Genus · Static Timing Analysis · GPDK 45 nm**

## Note

Proprietary Cadence libraries, foundry PDK files, generated databases, and other restricted design files are not included in this repository.
