# Chapter 6, Step 02: Sample rate and frame duration

## Purpose

Change the system sample rate to 24 kHz and the frame duration to 7.5 ms on both roles, move the gateway from the USB source to the I2S source so the sample rate is a legal one, rebuild, and diff the built configuration. The second purpose is just as important: discover, from `server_store.c`, that the wired in unicast presets carry their own codec frame duration, so a system frame duration change does not by itself produce a coherent 7.5 ms stream. The full discussion is in Chapter 6, sections 6.2, 6.5, and 6.8.2 of the lesson.

## Before we start

Start from the Step 01 end state with its overlay line removed, so `git -C nrf diff` on the two role overlays is empty. Work from `~/ncs-course` with the toolchain environment loaded:

```bash
cd ~/ncs-course
nrfutil toolchain-manager env --ncs-version v3.4.1 --as-script > /tmp/ncs-env.sh
source /tmp/ncs-env.sh
```

Role map: `unicast_client` is the gateway (transmitter, `CONFIG_AUDIO_DEV` 2). `unicast_server` is the headset (receiver, `CONFIG_AUDIO_DEV` 1).

## Commands

1. Append these lines to the client overlay, `nrf/applications/nrf_audio/unicast_client/overlay-unicast_client.conf`:

```text
CONFIG_AUDIO_SOURCE_I2S=y
CONFIG_AUDIO_SAMPLE_RATE_24000_HZ=y
CONFIG_AUDIO_FRAME_DURATION_7_5_MS=y
```

Append only these two lines to the server overlay, `nrf/applications/nrf_audio/unicast_server/overlay-unicast_server.conf`, because that overlay already sets `CONFIG_AUDIO_SOURCE_I2S=y`:

```text
CONFIG_AUDIO_SAMPLE_RATE_24000_HZ=y
CONFIG_AUDIO_FRAME_DURATION_7_5_MS=y
```

The source line exists because the sample rate Kconfig help says 24 kHz is currently only valid for I2S and line in, and USB input is 48 kHz only. Check the edit with `git -C nrf diff`: three added lines in the client overlay, two in the server overlay.

2. Build both roles:

```bash
west build -p always --sysbuild -b nrf5340_audio_dk/nrf5340/cpuapp -d build-unicast_client nrf/applications/nrf_audio -- -DEXTRA_CONF_FILE=unicast_client/overlay-unicast_client.conf
west build -p always --sysbuild -b nrf5340_audio_dk/nrf5340/cpuapp -d build-unicast_server nrf/applications/nrf_audio -- -DEXTRA_CONF_FILE=unicast_server/overlay-unicast_server.conf
```

3. Extract and read the resolved symbols:

```bash
grep -E "CONFIG_AUDIO_SOURCE|CONFIG_AUDIO_SAMPLE_RATE|CONFIG_AUDIO_FRAME_DURATION|CONFIG_BT_BAP_UNICAST" build-unicast_client/nrf_audio/zephyr/.config
grep -E "CONFIG_AUDIO_SOURCE|CONFIG_AUDIO_SAMPLE_RATE|CONFIG_AUDIO_FRAME_DURATION|CONFIG_BT_BAP_UNICAST" build-unicast_server/nrf_audio/zephyr/.config
```

4. Open `nrf/applications/nrf_audio/src/bluetooth/bt_stream/unicast/server_store.c`, find the three static presets and `sample_rate_check`, and compare the 24_2_1 codec values in `zephyr/include/zephyr/bluetooth/audio/bap_lc3_preset.h` with the system frame duration we just built.

## Expected result

Both builds finish. The client built configuration contains `CONFIG_AUDIO_SOURCE_I2S=y` with the USB source symbol not set, `CONFIG_AUDIO_SAMPLE_RATE_HZ=24000`, `CONFIG_AUDIO_FRAME_DURATION_7_5_MS=y`, and `CONFIG_AUDIO_FRAME_DURATION_US=7500`. The server built configuration contains the same sample rate and frame duration resolutions.

The source comparison shows the layering gap this step exists to teach: at 24 kHz, `sample_rate_check` selects the 24_2_1 preset, whose codec configuration uses a 10 ms frame duration, a 60 octet frame, an SDU interval of 10000 microseconds, RTN 2, transport latency 10 ms, and presentation delay 40000 microseconds. The lab record states where each duration lives, which code uses each one, and closes with a keep, revert, or promote recommendation and a trade statement. The recommended outcome is to revert the overlay lines and rebuild the baseline pair.

Whether a stream between these builds plays, stutters, or refuses to start is a desk expectation for the hardware pass. This step proves build level resolution and a source level fact, and nothing about sound.
