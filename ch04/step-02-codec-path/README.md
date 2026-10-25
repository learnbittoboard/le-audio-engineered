# Chapter 4, Step 02: Walk the codec path in the source

## Purpose

Trace one received frame through the headset code in order, so the decode path is a set of named files we have opened, not a diagram we have only looked at. This step also holds the stretch exercise: an explicit bidirectional configuration diff using the walkie-talkie symbol.

## Commands

Run from the SDK root at `~/ncs-course`:

```bash
grep -n "le_audio_rx_data_handler" nrf/applications/nrf_audio/src/audio/le_audio_rx.c
grep -n "sw_codec_decode" nrf/applications/nrf_audio/src/audio/audio_datapath.c nrf/applications/nrf_audio/src/audio/sw_codec_select.c
grep -n "Drft comp state\|Pres comp state" nrf/applications/nrf_audio/src/audio/audio_datapath.c
grep -n "hw_codec_volume\|pdm_mic" nrf/applications/nrf_audio/src/modules/hw_codec.c
```

For the stretch diff, keep the Step 01 build as the baseline, build a second directory with the walkie-talkie symbol added as a build flag, and compare the two built configurations:

```bash
cp build-unicast_server/nrf_audio/zephyr/.config baseline-headset.config
west build -p always --sysbuild -b nrf5340_audio_dk/nrf5340/cpuapp -d build-unicast_server-wt nrf/applications/nrf_audio -- -DEXTRA_CONF_FILE=unicast_server/overlay-unicast_server.conf -DCONFIG_WALKIE_TALKIE_DEMO=y
grep "CONFIG_WALKIE_TALKIE_DEMO" build-unicast_server-wt/nrf_audio/zephyr/.config
diff baseline-headset.config build-unicast_server-wt/nrf_audio/zephyr/.config
```

## Expected result

The searches locate, in receive order: `le_audio_rx_data_handler` in `le_audio_rx.c`, the `sw_codec_decode` call inside `audio_datapath_stream_out` in `audio_datapath.c`, the LC3 decoder checks in `sw_codec_select.c`, and the volume thread plus PDM microphone configuration in `hw_codec.c`. Our notes state, in one sentence per file, what enters and what leaves.

For the stretch, the diff is saved, the changed symbols are grouped under stream direction, audio source selection, presentation delay minimum, and encode channel limits, and the write up explains why the baseline already contained `CONFIG_STREAM_BIDIRECTIONAL=y` before anything was changed.
