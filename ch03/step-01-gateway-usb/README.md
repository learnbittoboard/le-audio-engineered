# Step 01: Gateway build with USB audio in

Chapter 3 of LE Audio, Engineered. Stack: NCS v3.4.1, Zephyr 4.4.2, GCC 14.3.0. Board target: nrf5340_audio_dk/nrf5340/cpuapp. Role: unicast_client, gateway, CONFIG_AUDIO_DEV of 2, CONFIG_TRANSPORT_CIS, CONFIG_AUDIO_SOURCE_USB.

## Purpose

Build the unicast client (gateway) firmware for Board A from the nRF Audio reference application, verify the role from the built configuration instead of trusting the command line, and walk the flash and first boot checks. The lesson explains the USB audio path and each verification; this file is the exact procedure.

## Commands

Run from the SDK root, `~/ncs-course`, with only Board A connected for the flash step:

```bash
cd ~/ncs-course
eval "$(nrfutil toolchain-manager env --ncs-version v3.4.1 --as-script)"

west build -p always --sysbuild -b nrf5340_audio_dk/nrf5340/cpuapp -d build-unicast_client nrf/applications/nrf_audio -- -DEXTRA_CONF_FILE=unicast_client/overlay-unicast_client.conf

grep -E "^CONFIG_AUDIO_DEV=|^CONFIG_TRANSPORT_CIS=|^CONFIG_AUDIO_SOURCE_USB=|^CONFIG_BT_CSIP_SET_COORDINATOR=|^CONFIG_BT_DEVICE_NAME=" build-unicast_client/nrf_audio/zephyr/.config

grep -E "^CONFIG_AUDIO_SAMPLE_RATE_HZ=|^CONFIG_AUDIO_FRAME_DURATION_US=" build-unicast_client/nrf_audio/zephyr/.config

cat build-unicast_client/domains.yaml
ls build-unicast_client/nrf_audio/zephyr/zephyr.hex build-unicast_client/ipc_radio/zephyr/zephyr.hex

nrfutil device list
west flash -d build-unicast_client
```

## Expected result

The build exits with status 0. The first grep shows `CONFIG_AUDIO_DEV=2`, `CONFIG_TRANSPORT_CIS=y`, `CONFIG_AUDIO_SOURCE_USB=y`, `CONFIG_BT_CSIP_SET_COORDINATOR=y`, and `CONFIG_BT_DEVICE_NAME="NRF_AUDIO"`. The second grep shows `CONFIG_AUDIO_SAMPLE_RATE_HZ=48000` and `CONFIG_AUDIO_FRAME_DURATION_US=10000`. The `domains.yaml` file lists the `nrf_audio` and `ipc_radio` domains with flash order `ipc_radio` then `nrf_audio`, and both per domain `zephyr.hex` files exist.

Flash and boot behaviour on real hardware is a desk expectation to record in your own bench notes: `west flash` programs both domains in the recorded order; after reset, LED3 blinks green, the RGB shows solid green for gateway, the serial log carries lines in the `audio_usb` family stating USB is initialised as unidirectional (headphones only) and ready for the USB host, and the computer's sound settings list an output device named nRF5340 USB Audio. Connection and streaming log lines are not expected until the headset exists in Chapter 4.
