# PicoSoC FFT Accelerator: RTL to Layout

A memory-mapped FFT accelerator for a PicoRV32 SoC, taken from RTL through synthesis, place-and-route and sign-off in a 45 nm flow. The high-performance design computes the first FFT chunk in **6.5 µs, down from 61.0 µs** in the reference design, with timing and DRC clean.

Group project for ET4351 Digital VLSI Systems on Chip (TU Delft, 2025–2026, group 4). Tools: Questa, Cadence Genus and Innovus.

## Results (post-layout, high-performance design)

| Metric | Baseline | This design |
|---|---|---|
| Latency of first chunk | 61.00 µs | **6.512 µs** (407 cycles) |
| Clock frequency | 12 MHz | 62.5 MHz |
| Post-layout area | — | 224 955 µm² (core 594.6 × 594.6 µm) |
| Setup / hold WNS | — | 0.756 ns / 0 ns |
| DRC, connectivity, antenna | — | clean |

Raw tool reports are in [`reports/`](reports/).

## What changed from the baseline

- **Dual-bank memory**: real and imaginary parts in separate banks, so both butterfly operands are read in one cycle (61.0 → 33.9 µs).
- **Shorter FSM**: 13 states down to 8, with only 5 in the butterfly loop.
- **Trivial-twiddle bypass**: rotations by 0°, 90°, 180° and 270° skip the multipliers.
- **Higher clock**: critical paths broken up, low-Vt cells, area-driven synthesis to relieve routing congestion, and an ECO pass for hold.

`git diff baseline main -- src` shows the RTL changes.

## Layout

| Path | Contents |
|---|---|
| tag `baseline` | Reference design handed out at the start of the project |
| `src/` | RTL, constraints and testbench |
| `firmware/` | C firmware that drives the accelerator from the PicoRV32 |
| `synth/`, `pnr/` | Genus and Innovus scripts |
| `sim_behav/`, `sim_struct/`, `sim_phys/` | RTL, post-synthesis and post-layout simulation |
| `sw/verify.py` | Checks simulation output against the software FFT |

## Run

`./run_all.sh` runs the whole flow: firmware, RTL simulation, synthesis, place-and-route and post-layout simulation. It expects the course server environment (`setup.sh`).

Not included, because they cannot be redistributed: the standard-cell and SRAM libraries (including `saed32sram.v`), the course audio samples, and the generated layout databases.

## Credits

The reference design and flow scripts come from the ET4351 course staff. PicoRV32 and PicoSoC are by Claire Wolf (ISC license). The accelerator and implementation work were done by the 11 members of group 4.
