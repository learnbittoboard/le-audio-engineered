# Chapter 2, Step 01: Build all four roles

## Purpose

Build every role image of the nrf_audio application (unicast client, unicast server, broadcast source, broadcast sink) into separate build directories, and keep the logs. This is the reference build set the whole course compares against.

## Commands

Activate the toolchain environment, then run from the SDK root at `~/ncs-course`:

```bash
export PATH="$HOME/.local/bin:$HOME/ncs-course/tools:$PATH"
eval "$(nrfutil toolchain-manager env --ncs-version v3.4.1 --as-script)"

west build -p always --sysbuild -b nrf5340_audio_dk/nrf5340/cpuapp \
  -d build-unicast_server nrf/applications/nrf_audio -- \
  -DEXTRA_CONF_FILE=unicast_server/overlay-unicast_server.conf

west build -p always --sysbuild -b nrf5340_audio_dk/nrf5340/cpuapp \
  -d build-unicast_client nrf/applications/nrf_audio -- \
  -DEXTRA_CONF_FILE=unicast_client/overlay-unicast_client.conf \
  -DCONFIG_STREAM_BIDIRECTIONAL=y

west build -p always --sysbuild -b nrf5340_audio_dk/nrf5340/cpuapp \
  -d build-broadcast_source nrf/applications/nrf_audio -- \
  -DEXTRA_CONF_FILE=broadcast_source/overlay-broadcast_source.conf

west build -p always --sysbuild -b nrf5340_audio_dk/nrf5340/cpuapp \
  -d build-broadcast_sink nrf/applications/nrf_audio -- \
  -DEXTRA_CONF_FILE=broadcast_sink/overlay-broadcast_sink.conf
```

## Expected result

Each build ends cleanly with two memory reports: the ipc_radio network core image first, then the nrf_audio application core image. Each build directory contains a `domains.yaml` listing both domains and the flash order (ipc_radio, then nrf_audio), plus per image `zephyr/zephyr.elf` and `zephyr/zephyr.hex` outputs. This sysbuild configuration produces no merged hex file. Reference numbers from the course's verification builds are tabulated in Lesson 2, section 2.7.
