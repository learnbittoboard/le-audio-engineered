# Step 01: Release pair

Lesson 10 of LE Audio, Engineered. Stack: NCS v3.4.1, Zephyr 4.4.2, GCC 14.3.0. Board target: nrf5340_audio_dk/nrf5340/cpuapp.

## Purpose

Build release images for both unicast roles of the pair, gateway and headset, using FILE_SUFFIX=release, and verify role, transport, and flavour from the built .config files rather than from the commands we typed. Role map: unicast_client is the gateway (CONFIG_AUDIO_DEV=2), unicast_server is the headset (CONFIG_AUDIO_DEV=1). Transport for both: CIS.

## Commands

Run from ~/ncs-course in a toolchain terminal in VS Code:

```bash
west build -p always --sysbuild -b nrf5340_audio_dk/nrf5340/cpuapp -d build-unicast_client-release nrf/applications/nrf_audio -- -DEXTRA_CONF_FILE=unicast_client/overlay-unicast_client.conf -DFILE_SUFFIX=release

west build -p always --sysbuild -b nrf5340_audio_dk/nrf5340/cpuapp -d build-unicast_server-release nrf/applications/nrf_audio -- -DEXTRA_CONF_FILE=unicast_server/overlay-unicast_server.conf -DFILE_SUFFIX=release

grep -E "CONFIG_AUDIO_DEV=|CONFIG_TRANSPORT_CIS|CONFIG_TRANSPORT_BIS|CONFIG_DEBUG|CONFIG_ASSERT|CONFIG_SERIAL|CONFIG_CONSOLE|CONFIG_BT_BAP_UNICAST_CLIENT" build-unicast_client-release/nrf_audio/zephyr/.config

grep -E "CONFIG_AUDIO_DEV=|CONFIG_TRANSPORT_CIS|CONFIG_TRANSPORT_BIS|CONFIG_DEBUG|CONFIG_ASSERT|CONFIG_SERIAL|CONFIG_CONSOLE|CONFIG_BT_BAP_UNICAST_SERVER|CONFIG_BT_CSIP_SET_MEMBER" build-unicast_server-release/nrf_audio/zephyr/.config

grep -E "CONFIG_DEBUG|CONFIG_SERIAL|CONFIG_CONSOLE|CONFIG_BT_CTLR_CONN_ISO_STREAMS" build-unicast_server-release/ipc_radio/zephyr/.config

ls build-unicast_client-release/nrf_audio/zephyr/zephyr.elf build-unicast_client-release/ipc_radio/zephyr/zephyr.elf build-unicast_server-release/nrf_audio/zephyr/zephyr.elf build-unicast_server-release/ipc_radio/zephyr/zephyr.elf
```

## Expected result

Both builds complete without error. Each build directory contains a domains.yaml listing the nrf_audio and ipc_radio domains. The gateway .config shows CONFIG_AUDIO_DEV=2 with the unicast client role and CIS selected; the headset .config shows CONFIG_AUDIO_DEV=1 with the unicast server role, CSIP set member support, and CIS selected. Both show the debug family in its off form. The network core .config of the headset build shows its release form with CONFIG_BT_CTLR_CONN_ISO_STREAMS=2. All four zephyr.elf paths list with no missing file errors. Hearing audio from a flashed release pair is a desk expectation for the author's hardware pass, not a result of this build step.
