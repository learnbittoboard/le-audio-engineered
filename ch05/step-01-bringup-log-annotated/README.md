# Chapter 5, Step 01: Annotate a bring up against the sequence

## Purpose

Build the bring up worksheet used in Lesson 5 and reused on the bench: real log format strings and ASE state names, extracted from the SDK source and sorted into the stages of the CIS establishment sequence diagram. This is a reading exercise. No line in the worksheet is a captured transcript.

## Commands

Run from the SDK root at `~/ncs-course`:

```bash
grep -n "BT_BAP_EP_STATE" zephyr/include/zephyr/bluetooth/audio/bap.h
grep -n "LOG_DBG\|LOG_INF\|LOG_WRN\|LOG_ERR" nrf/applications/nrf_audio/src/bluetooth/bt_stream/unicast/unicast_server.c
grep -n "LOG_INF" nrf/applications/nrf_audio/src/bluetooth/bt_stream/unicast/unicast_client.c
grep -n "Presentation delay\|No valid configurations" nrf/applications/nrf_audio/unicast_server/main.c
grep -n "Drft comp state\|Pres comp state" nrf/applications/nrf_audio/src/audio/audio_datapath.c
grep -n "ISO RX SDUs\|BLE ISO RX overrun" nrf/applications/nrf_audio/src/audio/le_audio_rx.c
```

## Expected result

A worksheet in which:

- The seven ASE states are listed with values 0x00 to 0x06 in order: Idle, Codec Configured, QoS Configured, Enabling, Streaming, Disabling, Releasing.
- The server families `ASE Codec Config stream %p`, `QoS: stream %p qos %p`, `Stream %p started`, `Stream %p disabled`, and `Stream %p released` appear with placeholders intact.
- The client families `Created unicast group`, `%s %s stream configured`, `Group PD sink: %u, source PD: %u`, and `Stream QoS PD: %d, prev group PD: %d, new PD %d` appear.
- The main and datapath families `Presentation delay %d us is set by initiator`, `No valid configurations found, will disconnect`, `Drft comp state: %s`, and `Pres comp state: %s` appear, with the drift spelling copied exactly as the source spells it.
- Every family is annotated with its sequence stage, its board (gateway or headset), its module, and the ASE state expected when it appears, and the streaming stage also carries the receive health families `ISO RX SDUs: Loc: %u Total: %llu Bad: %llu` and `BLE ISO RX overrun: Num: %d`.

Matching these families in real bench logs, in stage order, is a desk expectation for the hardware pass.
