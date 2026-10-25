# Chapter 13, Step 01: The case file worksheet

## Purpose

Work the case of Lesson 13 (the DevZone thread "nRF5340 audio sync lost when adding oscillator CONFIG") the way the chapter teaches: study the original, pause and diagnose from the evidence, then reproduce the fault and its fix at configuration level in the pinned NCS v3.4.1 stack, where both states are readable facts in two `.config` files.

## Before you start

Read the original thread first (about 10 minutes): https://devzone.nordicsemi.com/f/nordic-q-a/113731/nrf5340-audio-sync-lost-when-adding-oscillator-config
The full log lives there; the lesson quotes only the lines the walk turns on. Write your one sentence theory at the bottom of the ticket before opening the lesson's verdict (section 13.5).

## Commands

Run from the SDK root at `~/ncs-course` with the toolchain environment loaded. Baseline first (any existing course broadcast sink build works):

```bash
grep -E 'K32SRC_(SYNTH|XTAL)=' build-broadcast_sink/nrf_audio/zephyr/.config
grep -E 'K32SRC_(SYNTH|XTAL)=' build-broadcast_sink/ipc_radio/zephyr/.config
```

Create `fault-k32src.conf` with the reporter's three lines:

```
CONFIG_CLOCK_CONTROL_NRF_K32SRC_RC=y
CONFIG_CLOCK_CONTROL_NRF_K32SRC_RC_CALIBRATION=y
CONFIG_CLOCK_CONTROL_NRF_K32SRC_SYNTH=y
```

Reproduce the split (application side only):

```bash
west build -p always --sysbuild -b nrf5340_audio_dk/nrf5340/cpuapp \
  -d build-sink_fault nrf/applications/nrf_audio -- \
  -DEXTRA_CONF_FILE="broadcast_sink/overlay-broadcast_sink.conf;<path>/fault-k32src.conf"
grep -E 'K32SRC_(SYNTH|XTAL)=' build-sink_fault/nrf_audio/zephyr/.config
grep -E 'K32SRC_(SYNTH|XTAL)=' build-sink_fault/ipc_radio/zephyr/.config
```

Create `fix-k32src.conf` with the fix's single line (`CONFIG_CLOCK_CONTROL_NRF_K32SRC_SYNTH=y`), then repair (the line delivered to the network core image):

```bash
west build -p always --sysbuild -b nrf5340_audio_dk/nrf5340/cpuapp \
  -d build-sink_fixed nrf/applications/nrf_audio -- \
  -DEXTRA_CONF_FILE="broadcast_sink/overlay-broadcast_sink.conf;<path>/fault-k32src.conf" \
  -Dipc_radio_EXTRA_CONF_FILE=<path>/fix-k32src.conf
grep -E 'K32SRC_(SYNTH|XTAL)=' build-sink_fixed/nrf_audio/zephyr/.config
grep -E 'K32SRC_(SYNTH|XTAL)=' build-sink_fixed/ipc_radio/zephyr/.config
```

## Expected result

Baseline: both images report `CONFIG_CLOCK_CONTROL_NRF_K32SRC_XTAL=y` (agreement, the stock state). Fault build: completes cleanly, and the greps disagree: application `SYNTH=y`, network core `XTAL=y`. That pair of lines is the case's root cause, reproduced. Fixed build: both images report `SYNTH=y` (agreement on the new story). All three states were produced and read in the course's own verification builds on this exact stack. The on air behaviour of the fault build (the metronome sync loss) is the thread's telemetry and, on your bench, a desk expectation to confirm; this worksheet's claims are configuration level and fully provable from the chair.
