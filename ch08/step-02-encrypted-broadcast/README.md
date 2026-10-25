# Chapter 8, Step 02: Encrypted broadcast source variant

## Purpose

Rebuild the Chapter 8 broadcast source with broadcast encryption enabled, so the pair can exercise the Broadcast Code concept. The source encrypts its BIS streams with the configured key string; a sink needs the matching key to produce audio. The matching sink configuration is exercised in Chapter 9.

Symbols: `CONFIG_BT_AUDIO_BROADCAST_ENCRYPTED=y` and `CONFIG_BT_AUDIO_BROADCAST_ENCRYPTION_KEY` set to a course key shared with the sink build. Keep the step 01 public build intact for comparison.

## Commands

Run from `~/ncs-course` with the pinned toolchain environment loaded. Place the two symbols above in the extra configuration file used for this build, then:

```bash
west build -p always --sysbuild -b nrf5340_audio_dk/nrf5340/cpuapp -d build-broadcast_source_encrypted nrf/applications/nrf_audio -- -DEXTRA_CONF_FILE=broadcast_source/overlay-broadcast_source.conf
grep -E "CONFIG_BT_AUDIO_BROADCAST_ENCRYPTED|CONFIG_BT_AUDIO_BROADCAST_ENCRYPTION_KEY" build-broadcast_source_encrypted/nrf_audio/zephyr/.config
```

## Expected result

The build completes without error, in a directory separate from step 01. The `.config` search shows the encrypted symbol set to `y` with the chosen key, while the step 01 `.config` still shows encryption not set. Name, Broadcast ID type, and the BASE description are unchanged unless deliberately altered.

On the bench (desk expectation), an encrypted source reports its started family as encrypted, and a sink without the matching code should find the broadcast but not produce audio. Do not claim encrypted audio was heard from this build alone.
