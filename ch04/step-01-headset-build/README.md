# Chapter 4, Step 01: Build the unicast server headset

## Purpose

Build the `nrf_audio` application as a unicast server (headset) for the nRF5340 Audio DK and prove the role from the built configuration, not from the build command we intended to run. This step covers the build half of the Chapter 4 lab. First sound is a bench expectation in the lesson, not a result claimed here.

## Commands

Run from the SDK root at `~/ncs-course`, in a toolchain terminal in VS Code:

```bash
cat nrf/applications/nrf_audio/unicast_server/overlay-unicast_server.conf
west build -p always --sysbuild -b nrf5340_audio_dk/nrf5340/cpuapp -d build-unicast_server nrf/applications/nrf_audio -- -DEXTRA_CONF_FILE=unicast_server/overlay-unicast_server.conf
grep -E "CONFIG_AUDIO_DEV=|CONFIG_TRANSPORT_CIS|CONFIG_TRANSPORT_BIS" build-unicast_server/nrf_audio/zephyr/.config
grep -E "CONFIG_SW_CODEC_LC3=|CONFIG_NRF_AUDIO_CS47L63_DRIVER=" build-unicast_server/nrf_audio/zephyr/.config
grep -E "CONFIG_STREAM_BIDIRECTIONAL=|CONFIG_AUDIO_DECODE_CHANNELS_MAX=|CONFIG_AUDIO_MIN_PRES_DLY_US=|CONFIG_AUDIO_MAX_PRES_DLY_US=" build-unicast_server/nrf_audio/zephyr/.config
```

## Expected result

The build completes and the configuration searches return these set lines:

- `CONFIG_AUDIO_DEV=1` (headset; the value 2 would mean gateway)
- `CONFIG_TRANSPORT_CIS=y`, with BIS present only as a not set line
- `CONFIG_SW_CODEC_LC3=y` and `CONFIG_NRF_AUDIO_CS47L63_DRIVER=y`
- `CONFIG_STREAM_BIDIRECTIONAL=y`, `CONFIG_AUDIO_DECODE_CHANNELS_MAX=2`, `CONFIG_AUDIO_MIN_PRES_DLY_US=5000`, and `CONFIG_AUDIO_MAX_PRES_DLY_US=60000`

Save the build command and those lines with your lab notes. Flashing and first sound follow the desk expectation procedure in Lesson 4, to be observed on the hardware pass.
