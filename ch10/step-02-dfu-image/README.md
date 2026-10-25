# Step 02: DFU image build

Lesson 10 of LE Audio, Engineered. Stack: NCS v3.4.1, Zephyr 4.4.2, GCC 14.3.0. Board target: nrf5340_audio_dk/nrf5340/cpuapp.

## Purpose

Rebuild one role, the unicast server headset, as a release build with MCUboot enabled at sysbuild level, and verify that the build structure gains a bootloader domain and produces update artifacts. The default build of this application has no MCUboot; this step adds it deliberately, in a separate build directory, so the plain release pair from step 01 stays untouched. This step proves the update image structure at build level only. No delivery transport for this application is verified here, and an actual transfer to hardware is a desk expectation for the author's hardware pass.

## Commands

Run from ~/ncs-course in a toolchain terminal in VS Code:

```bash
west build -p always --sysbuild -b nrf5340_audio_dk/nrf5340/cpuapp -d build-unicast_server-release-dfu nrf/applications/nrf_audio -- -DEXTRA_CONF_FILE=unicast_server/overlay-unicast_server.conf -DFILE_SUFFIX=release -DSB_CONFIG_BOOTLOADER_MCUBOOT=y

cat build-unicast_server-release-dfu/domains.yaml
ls build-unicast_server-release-dfu

grep -E "CONFIG_AUDIO_DEV=|CONFIG_BT_BAP_UNICAST_SERVER|CONFIG_DEBUG" build-unicast_server-release-dfu/nrf_audio/zephyr/.config

ls build-unicast_server-release-dfu/*.zip build-unicast_server-release-dfu/nrf_audio/zephyr/zephyr.signed.bin build-unicast_server-release-dfu/nrf_audio/zephyr/app_update.bin 2>/dev/null
find build-unicast_server-release-dfu -maxdepth 2 -name "*update*" -o -maxdepth 2 -name "dfu*.zip"
```

## Expected result

The build completes without error in its own directory, build-unicast_server-release-dfu. The domains listing includes a bootloader domain alongside nrf_audio and ipc_radio, and the build tree contains MCUboot related output absent from the plain release build. The application .config still shows CONFIG_AUDIO_DEV=1, the unicast server role, and the release form of the debug family. At least one packaged update artifact is present in the build output; record its exact name and location from the listing in the lab notes rather than assuming it from this README.
