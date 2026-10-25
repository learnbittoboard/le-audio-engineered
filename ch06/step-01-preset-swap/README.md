# Chapter 6, Step 01: Preset swap

## Purpose

Move the unicast pair from the default configurable BAP configuration to the explicit named preset `CONFIG_BT_BAP_UNICAST_48_4_1`, on both roles, and prove the move in the built `.config` files rather than trusting the overlay edits. This step stays at 48 kHz and a 10 ms frame duration, inside the tested paths. The full reasoning, the trade framework, and the trade statement we write at the end are in Chapter 6, sections 6.3, 6.6, and 6.8.1 of the lesson.

## Before we start

Work from `~/ncs-course` in a toolchain terminal in VS Code:

```bash
cd ~/ncs-course
```

Role map: `unicast_client` is the gateway (transmitter, `CONFIG_AUDIO_DEV` 2). `unicast_server` is the headset (receiver, `CONFIG_AUDIO_DEV` 1). Transport is CIS in both builds.

## Commands

1. Build the baseline pair, before editing anything:

```bash
west build -p always --sysbuild -b nrf5340_audio_dk/nrf5340/cpuapp -d build-unicast_client nrf/applications/nrf_audio -- -DEXTRA_CONF_FILE=unicast_client/overlay-unicast_client.conf
west build -p always --sysbuild -b nrf5340_audio_dk/nrf5340/cpuapp -d build-unicast_server nrf/applications/nrf_audio -- -DEXTRA_CONF_FILE=unicast_server/overlay-unicast_server.conf
```

2. Save the baseline tuning symbols:

```bash
grep -E "CONFIG_BT_BAP_UNICAST|CONFIG_BT_AUDIO_BITRATE|CONFIG_AUDIO_SAMPLE_RATE_HZ|CONFIG_AUDIO_FRAME_DURATION_US|CONFIG_AUDIO_MIN_PRES_DLY_US" build-unicast_client/nrf_audio/zephyr/.config > /tmp/ch06-baseline-client.txt
grep -E "CONFIG_BT_BAP_UNICAST|CONFIG_BT_AUDIO_BITRATE|CONFIG_AUDIO_SAMPLE_RATE_HZ|CONFIG_AUDIO_FRAME_DURATION_US|CONFIG_AUDIO_MIN_PRES_DLY_US" build-unicast_server/nrf_audio/zephyr/.config > /tmp/ch06-baseline-server.txt
```

3. Append one line to each role overlay in our working copy of the application, `nrf/applications/nrf_audio/unicast_client/overlay-unicast_client.conf` and `nrf/applications/nrf_audio/unicast_server/overlay-unicast_server.conf`:

```text
CONFIG_BT_BAP_UNICAST_48_4_1=y
```

Check the edit with `git -C nrf diff`. It must show exactly one added line in each overlay and nothing else.

4. Rebuild both roles with the two build commands from step 1.

5. Extract the same symbols again and diff:

```bash
grep -E "CONFIG_BT_BAP_UNICAST|CONFIG_BT_AUDIO_BITRATE|CONFIG_AUDIO_SAMPLE_RATE_HZ|CONFIG_AUDIO_FRAME_DURATION_US|CONFIG_AUDIO_MIN_PRES_DLY_US" build-unicast_client/nrf_audio/zephyr/.config > /tmp/ch06-step01-client.txt
diff /tmp/ch06-baseline-client.txt /tmp/ch06-step01-client.txt
```

Repeat the extract and diff for the server build.

## Expected result

Both builds finish without a Kconfig error. The baseline extract shows `CONFIG_BT_BAP_UNICAST_CONFIGURABLE=y`, `CONFIG_AUDIO_SAMPLE_RATE_HZ=48000`, and `CONFIG_AUDIO_FRAME_DURATION_US=10000`. After the change, the diff for each role shows the configurable symbol no longer set and `CONFIG_BT_BAP_UNICAST_48_4_1=y` present, with the sample rate and frame duration lines unchanged. Every other changed line in the diff gets one written sentence of explanation in the lab record, and the record closes with a trade statement naming a product, a spent currency, and a saved currency.

How the stream sounds after this step is a desk expectation for the hardware pass, not a result of this step. From the preset values in the source, the negotiated configuration is expected to match the 48_4_1 bundle the application already favours at 48 kHz, so the audible change may be small or none.
