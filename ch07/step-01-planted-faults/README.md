# Chapter 7, Step 01: Planted faults

## Purpose

Diagnose three planted faults from written log excerpts alone, using the method in Chapter 7, section 7.1: shape first, first unhappy module second, starved, flooded, or misaligned third, then one fix and one rebuild. The excerpts in the lesson are composed from real log format strings read in the SDK source. They are lines in the family of what the modules print, not captured transcripts, and any counter values in them are teaching values, not measurements.

The three faults, with their full excerpts, diagnoses, fixes, and rebuild steps, are in Chapter 7, sections 7.8.1 to 7.8.3:

1. Fault 1, no stream at all: `server_store` reports no supported sample rate, the client cannot create a unicast group, and the receiver modules are silent.
2. Fault 2, a stream that drops: `bt_le_audio_tx` reports that the encoded data size does not match the SDU size, and the receiver datapath starves downstream.
3. Fault 3, a stream that drifts: the transmit path reports data provided too slow and too fast on a synchronous USB feed, drift compensation cycles instead of settling, and the USB and datapath counters climb slowly and steadily.

## Commands

Every fix in this step is proven with the same pair of builds, run from `~/ncs-course` with the toolchain environment loaded (`nrfutil toolchain-manager env --ncs-version v3.4.1 --as-script`). The gateway is `unicast_client` (`CONFIG_AUDIO_DEV` 2) and the headset is `unicast_server` (`CONFIG_AUDIO_DEV` 1), both on `nrf5340_audio_dk/nrf5340/cpuapp` with CIS transport:

```bash
west build -p always --sysbuild -b nrf5340_audio_dk/nrf5340/cpuapp -d build-unicast_client nrf/applications/nrf_audio -- -DEXTRA_CONF_FILE=unicast_client/overlay-unicast_client.conf
west build -p always --sysbuild -b nrf5340_audio_dk/nrf5340/cpuapp -d build-unicast_server nrf/applications/nrf_audio -- -DEXTRA_CONF_FILE=unicast_server/overlay-unicast_server.conf
```

Fault specific configuration checks:

```bash
# Fault 1: the pair must agree on one sample rate
grep "CONFIG_AUDIO_SAMPLE_RATE_HZ" build-unicast_client/nrf_audio/zephyr/.config build-unicast_server/nrf_audio/zephyr/.config

# Fault 2: no lone hand edited bitrate on one side
grep -E "CONFIG_LC3_BITRATE|CONFIG_BT_AUDIO_BITRATE" build-unicast_client/nrf_audio/zephyr/.config build-unicast_server/nrf_audio/zephyr/.config

# Fault 3: the gateway source change is a configuration change
grep "CONFIG_AUDIO_SOURCE" build-unicast_client/nrf_audio/zephyr/.config
```

Overlay edits behind those checks, each described in full in the lesson: Fault 1 removes leftover sample rate lines from both role overlays and rebuilds at 48000 for the USB gateway. Fault 2 removes the hand edited bitrate line from the client overlay and rebuilds both roles. Fault 3 adds `CONFIG_AUDIO_SOURCE_I2S=y` to the client overlay for the isolation test and rebuilds both roles.

## Expected result

Fault 1: both builds finish and `CONFIG_AUDIO_SAMPLE_RATE_HZ` is 48000 in both built `.config` files. Fault 2: both builds finish, no bitrate value appears as a lone hand edit on one side, and the lab record shows the octets per frame arithmetic for the reverted bitrate at a 10000 microsecond frame duration. Fault 3: the gateway build finishes with `CONFIG_AUDIO_SOURCE_I2S=y` and the USB source symbol not set in its built `.config`, and the lab record explains why a clean I2S test would clear the firmware and convict the host path.

What any of these streams sounds like after the fix is a desk expectation for the hardware pass. This step proves diagnoses on paper and configuration resolution at build level.
