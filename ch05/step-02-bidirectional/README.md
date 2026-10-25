# Chapter 5, Step 02: Bidirectional annotation and the QoS rejection trace

## Purpose

Extend the Step 01 worksheet to a bidirectional bring up, then work the stretch exercise: trace a server QoS rejection through the ASE state machine in writing, using the response code names from the Zephyr header.

## Commands

Run from the SDK root at `~/ncs-course`:

```bash
grep -n "audio_system_encoder_start\|audio_system_encoder_stop" nrf/applications/nrf_audio/unicast_server/main.c
grep -n "STREAM_BIDIRECTIONAL\|WALKIE_TALKIE_DEMO" nrf/applications/nrf_audio/src/audio/Kconfig
grep -n "BT_BAP_ASCS_RSP_CODE" zephyr/include/zephyr/bluetooth/audio/bap.h
grep -n "Group PD sink" nrf/applications/nrf_audio/src/bluetooth/bt_stream/unicast/unicast_client.c
```

## Expected result

The worksheet extension names `audio_system_encoder_start` and `audio_system_encoder_stop` as they appear in the unicast server `main.c`, assigns the source ASE its own pass through Codec Configured, QoS Configured, Enabling, and Streaming, and identifies the client family that shows the group presentation delay considering both directions.

The written failure trace states the ASE state at every step, never places the ASE in Enabling or Streaming after the QoS rejection, leaves the ASE in Codec Configured after that rejection, names the ASCS response code families for unsupported, rejected, and invalid configurations, and ends with the reference application escalation: the warning family `No valid configurations found, will disconnect` in the server main code, followed by disconnection, identified as application policy rather than Bluetooth behaviour.
