# Chapter 8, Step 01: Build the broadcast source (public)

## Purpose

Build the gateway board role from the nRF Audio reference application as a public Auracast broadcast source, and prove the transport and role in the resolved configuration before any flash step. This is the base build for Chapter 8 and the source half of the pair that Chapter 9 receives.

Role: `broadcast_source` is the gateway (transmitter). `CONFIG_AUDIO_DEV=2`. Transport: BIS.

## Commands

Run from `~/ncs-course` in a toolchain terminal in VS Code:

```bash
west build -p always --sysbuild -b nrf5340_audio_dk/nrf5340/cpuapp -d build-broadcast_source nrf/applications/nrf_audio -- -DEXTRA_CONF_FILE=broadcast_source/overlay-broadcast_source.conf
grep -E "CONFIG_TRANSPORT_BIS|CONFIG_TRANSPORT_CIS|CONFIG_AUDIO_DEV|CONFIG_BT_BAP_BROADCAST_SOURCE|CONFIG_BT_ISO_BROADCASTER|CONFIG_BT_CAP_INITIATOR" build-broadcast_source/nrf_audio/zephyr/.config
```

## Expected result

The build completes without error. The `.config` search shows `CONFIG_TRANSPORT_BIS=y`, `CONFIG_AUDIO_DEV=2`, `CONFIG_BT_BAP_BROADCAST_SOURCE=y`, `CONFIG_BT_ISO_BROADCASTER=y`, and `CONFIG_BT_CAP_INITIATOR=y`, with no `CONFIG_TRANSPORT_CIS=y` selected, the broadcast name at its default `NRF5340_BROADCASTER`, Auracast enabled, one Broadcast ID type enabled (FICR by default), and the encrypted broadcast symbol not set (public broadcast).

Flashing and advertising are a desk expectation for the hardware pass: flash the gateway board only, then look for log lines in the families for a created broadcast source naming the broadcast, a started source reporting index, ID, subgroups, and not encrypted, and advertising successfully started. Do not treat those families as results until an actual bench log is recorded.
