# Chapter 9, Step 01: Build the broadcast sink

## Purpose

Build the headset board role from the nRF Audio reference application as a broadcast sink that scans on its own, syncs to periodic advertising, reads the BASE, selects BIS streams by location, and receives audio. This is the receiving half of the Chapter 8 source pair.

Role: `broadcast_sink` is the headset (receiver). `CONFIG_AUDIO_DEV=1`. Transport: BIS. The sink syncs on its own in this step; no phone or Broadcast Assistant is used.

## Commands

Run from `~/ncs-course` with the pinned toolchain environment loaded (`nrfutil toolchain-manager env --ncs-version v3.4.1 --as-script`):

```bash
west build -p always --sysbuild -b nrf5340_audio_dk/nrf5340/cpuapp -d build-broadcast_sink nrf/applications/nrf_audio -- -DEXTRA_CONF_FILE=broadcast_sink/overlay-broadcast_sink.conf
grep -E "CONFIG_TRANSPORT_BIS|CONFIG_TRANSPORT_CIS|CONFIG_AUDIO_DEV|CONFIG_BT_BAP_BROADCAST_SINK|CONFIG_BT_ISO_SYNC_RECEIVER|CONFIG_BT_OBSERVER" build-broadcast_sink/nrf_audio/zephyr/.config
```

## Expected result

The build completes without error. The `.config` search shows `CONFIG_TRANSPORT_BIS=y`, `CONFIG_AUDIO_DEV=1`, `CONFIG_BT_BAP_BROADCAST_SINK=y`, `CONFIG_BT_ISO_SYNC_RECEIVER=y`, and `CONFIG_BT_OBSERVER=y`, with no `CONFIG_TRANSPORT_CIS=y` selected, and the default decode channel maximum of one. The resolved broadcast name must match the Chapter 8 source build, and encryption must agree with the source step in use (public for the base pair).

Sync and listening are a desk expectation for the hardware pass, with the Chapter 8 source running: flash the headset board only, then annotate the actual log stage by stage (found, PA synced, BASE and presentation delay, encryption line, BIS bitfield, stream started) and record the listening result honestly.
