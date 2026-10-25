# Step 01: Capstone checklist

Lesson 11 of LE Audio, Engineered. Stack: NCS v3.4.1, Zephyr 4.4.2, GCC 14.3.0. Board target: nrf5340_audio_dk/nrf5340/cpuapp.

## Purpose

Run the full bring up protocol cold and produce the product card. Protocol order: gateway, headset, unicast link, broadcast source, broadcast sink, switch back to unicast. One flavour, debug or release, for the whole run, recorded at the top of the capstone notes. Role map: unicast_client and broadcast_source are the gateway (CONFIG_AUDIO_DEV=2); unicast_server and broadcast_sink are the headset (CONFIG_AUDIO_DEV=1).

## Commands

Builds, only if a build needs to be remade. Run from ~/ncs-course in a toolchain terminal in VS Code. Add -DFILE_SUFFIX=release to every command for a release flavour capstone.

```bash
west build -p always --sysbuild -b nrf5340_audio_dk/nrf5340/cpuapp -d build-unicast_client nrf/applications/nrf_audio -- -DEXTRA_CONF_FILE=unicast_client/overlay-unicast_client.conf

west build -p always --sysbuild -b nrf5340_audio_dk/nrf5340/cpuapp -d build-unicast_server nrf/applications/nrf_audio -- -DEXTRA_CONF_FILE=unicast_server/overlay-unicast_server.conf

west build -p always --sysbuild -b nrf5340_audio_dk/nrf5340/cpuapp -d build-broadcast_source nrf/applications/nrf_audio -- -DEXTRA_CONF_FILE=broadcast_source/overlay-broadcast_source.conf

west build -p always --sysbuild -b nrf5340_audio_dk/nrf5340/cpuapp -d build-broadcast_sink nrf/applications/nrf_audio -- -DEXTRA_CONF_FILE=broadcast_sink/overlay-broadcast_sink.conf
```

Checklist verification commands used during the run:

```bash
grep -E "CONFIG_AUDIO_DEV=|CONFIG_TRANSPORT_CIS|CONFIG_TRANSPORT_BIS|CONFIG_BT_BAP_UNICAST_CLIENT" build-unicast_client/nrf_audio/zephyr/.config
grep -E "CONFIG_AUDIO_DEV=|CONFIG_TRANSPORT_CIS|CONFIG_TRANSPORT_BIS|CONFIG_BT_BAP_UNICAST_SERVER|CONFIG_BT_CSIP_SET_MEMBER" build-unicast_server/nrf_audio/zephyr/.config
```

Flashing and power up follow the lesson checklist stage by stage: pair log line before each flash, one board at a time, gateway before headset in both personalities, listening pass in the fixed order presence, stability, control, quality impressions. If the unicast link does not re form after the switch back, suspect stored bonding state first and, if the evidence supports it, clear bonding by holding BTN5 on the affected device during startup, then retry once.

## Expected result

The run closes the loop: unicast link verified, broadcast source and sink verified, switch back to a working unicast link, or a precise evidence based failure record for the stage that did not pass. Two artifacts exist at the end: capstone notes with a verdict and evidence for every stage, and a completed product card whose firmware, configuration, and identity entries match the pair log and the built .config evidence. All hardware and audio observations are recorded as observations from this run. In the course text, the on hardware behaviour is a desk expectation for the author's hardware pass.
