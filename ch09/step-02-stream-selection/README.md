# Chapter 9, Step 02: Stream selection and stereo sink variant

## Purpose

Rebuild the Chapter 9 broadcast sink with the stereo enable lines prescribed in the sink overlay comments, so one sink device can select both BIS streams of the default stereo broadcast, decode two channels, and present stereo itself. Use it to study the BASE walk: codec and frequency checks per subgroup, location matching per BIS, and the selection bitfield as the visible result.

Extra configuration for this build (the three lines the overlay prescribes for stereo): concurrent received streams maximum of two, decode channels maximum of two, and the minimum presentation delay symbol at the overlay prescribed value. Keep the step 01 build intact for comparison.

## Commands

Run from `~/ncs-course` with the pinned toolchain environment loaded. Place the three stereo lines in the extra configuration file used for this build, then:

```bash
west build -p always --sysbuild -b nrf5340_audio_dk/nrf5340/cpuapp -d build-broadcast_sink_stereo nrf/applications/nrf_audio -- -DEXTRA_CONF_FILE=broadcast_sink/overlay-broadcast_sink.conf
grep -E "CONFIG_BT_AUDIO_CONCURRENT_RX_STREAMS_MAX|CONFIG_AUDIO_DECODE_CHANNELS_MAX|CONFIG_LC3_DEC_CHAN_MAX|CONFIG_AUDIO_MIN_PRES_DLY_US" build-broadcast_sink_stereo/nrf_audio/zephyr/.config
```

## Expected result

The build completes without error, in a directory separate from step 01. The `.config` search shows the concurrent streams and decode channel maxima at two for the stereo build, while the step 01 `.config` retains its defaults. Predictions to record before any bench run: a left or right located default sink selects one BIS; this stereo build can select both, and its syncing log family should show a two bit bitfield.

On the bench (desk expectation), verify the prediction against the pasted log bitfield and the listening result, and recheck device location, BASE allocations, and capacity symbols in that order if the result disagrees.
