# Step 02: Set the pair identity (SIRK)

Chapter 3 of LE Audio, Engineered. Stack: NCS v3.4.1, Zephyr 4.4.2, GCC 14.3.0. Board target: nrf5340_audio_dk/nrf5340/cpuapp.

## Purpose

Choose the coordinated set identity for the pair, record it as a build input for the headset build in Chapter 4, and prove the difference between a build flag that was delivered and one that took effect. The SIRK lives on the set member (the headset, the unicast server). The gateway is the set coordinator and learns the SIRK over the air through set discovery, so this step configures no gateway behaviour. Its product is a correct, recorded identity and a verified understanding of where the flag lands.

## Pair record

Fill this in and keep it with the Chapter 1 inventory worksheet.

| Field | Value |
|---|---|
| Pair SIRK (exactly 16 characters, not NRF5340_TWS_DEMO) | |
| Character count, counted by hand | |
| Board A serial number (gateway) | |
| Board B serial number (headset) | |
| Flag staged for the Chapter 4 server build | -DCONFIG_BT_SET_IDENTITY_RESOLVING_KEY with our SIRK as the value |

## Commands

1. Read the vendor tooling first. In the SDK tree, open `nrf/applications/nrf_audio/tools/buildprog/buildprog.py` and find the SIRK validation: it rejects the default value and any length other than 16, and it passes the key to the headset build as a `CONFIG_BT_SET_IDENTITY_RESOLVING_KEY` build flag.

2. Rebuild the gateway with the identity flag added, into a separate directory. The value below is a 16 character placeholder shape; substitute the pair SIRK from the record above in a real run:

```bash
cd ~/ncs-course

west build -p always --sysbuild -b nrf5340_audio_dk/nrf5340/cpuapp -d build-unicast_client_sirk nrf/applications/nrf_audio -- -DEXTRA_CONF_FILE=unicast_client/overlay-unicast_client.conf -DCONFIG_BT_SET_IDENTITY_RESOLVING_KEY=\"PAIRKEY_EXAMPLE1\"

grep "CONFIG_BT_SET_IDENTITY_RESOLVING_KEY" build-unicast_client_sirk/nrf_audio/CMakeCache.txt
grep "CONFIG_BT_SET_IDENTITY_RESOLVING_KEY" build-unicast_client_sirk/nrf_audio/zephyr/.config
```

3. Do not flash this experimental build. Board A keeps the clean step 01 gateway image.

## Expected result

The build exits with status 0. The first grep finds the flag value recorded in `CMakeCache.txt`: the flag reached the build. The second grep finds no matching line in the gateway `.config`: the symbol `CONFIG_BT_SET_IDENTITY_RESOLVING_KEY` is defined only where the unicast server is enabled, so on the coordinator role it has nothing to attach to. Delivered is not the same as effective, and the `.config` grep is how we tell the difference.

In Chapter 4 the same flag, carrying the recorded pair SIRK, is applied to the unicast server build. The verification there is the mirror image of this one: the symbol must appear in the server build's `.config` with our value, and the headset log must not carry the default SIRK warning line family.
