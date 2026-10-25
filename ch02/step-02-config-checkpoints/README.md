# Chapter 2, Step 02: Config checkpoints

## Purpose

Prove from each build's resolved configuration that the build really is the role its directory name claims. A directory name is a claim; the built `.config` is the proof.

## Commands

Run from the SDK root at `~/ncs-course`, once per build directory:

```bash
grep -E '^CONFIG_AUDIO_DEV=|^CONFIG_TRANSPORT_CIS=|^CONFIG_TRANSPORT_BIS=|^CONFIG_SW_CODEC_LC3=' \
  build-unicast_server/nrf_audio/zephyr/.config
grep -E '^CONFIG_AUDIO_DEV=|^CONFIG_TRANSPORT_CIS=|^CONFIG_TRANSPORT_BIS=|^CONFIG_SW_CODEC_LC3=' \
  build-unicast_client/nrf_audio/zephyr/.config
grep -E '^CONFIG_AUDIO_DEV=|^CONFIG_TRANSPORT_CIS=|^CONFIG_TRANSPORT_BIS=|^CONFIG_SW_CODEC_LC3=' \
  build-broadcast_source/nrf_audio/zephyr/.config
grep -E '^CONFIG_AUDIO_DEV=|^CONFIG_TRANSPORT_CIS=|^CONFIG_TRANSPORT_BIS=|^CONFIG_SW_CODEC_LC3=' \
  build-broadcast_sink/nrf_audio/zephyr/.config
```

## Expected result

- unicast_server: `CONFIG_AUDIO_DEV=1`, `CONFIG_TRANSPORT_CIS=y`, `CONFIG_SW_CODEC_LC3=y`.
- unicast_client: `CONFIG_AUDIO_DEV=2`, `CONFIG_TRANSPORT_CIS=y`, `CONFIG_SW_CODEC_LC3=y`.
- broadcast_source: `CONFIG_AUDIO_DEV=2`, `CONFIG_TRANSPORT_BIS=y`, no CIS transport line, `CONFIG_SW_CODEC_LC3=y`.
- broadcast_sink: `CONFIG_AUDIO_DEV=1`, `CONFIG_TRANSPORT_BIS=y`, no CIS transport line, `CONFIG_SW_CODEC_LC3=y`.

Record the four rows as a table in your lab notes (build directory, device number, transport, codec). This worksheet is the template for the config checkpoint that ends every later lab in the course.
