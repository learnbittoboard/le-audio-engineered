# Chapter 12, Step 01: Sniffer setup

## Purpose

Build the witness: program a Nordic board (nRF52840 DK or nRF52840 Dongle) with the nRF Sniffer for Bluetooth LE firmware, install the Wireshark extcap plugin, and create the course Wireshark profile from Lesson 12, section 12.3.

## Commands

There is no build in this step. The procedure is:

1. Download the nRF Sniffer for Bluetooth LE package from Nordic and program the sniffer firmware onto the dongle or DK, following Nordic's instructions for your board. Record the board and the sniffer firmware version in your lab notes.
2. Copy the package's extcap plugin into your Wireshark installation's extcap folder (the package documents the path per operating system) and restart Wireshark.
3. In Wireshark, create a new profile named for this course. Set the columns: relative time, source, destination, protocol, length, info.
4. Add the starter display filters from Lesson 12, section 12.3, each saved under a name that states the question it answers (advertising picture, one device, ISO rhythm, control landmarks).

## Expected result

The nRF Sniffer interface appears in Wireshark's capture interface list (provable at a desk, no radios required). The course profile exists with the five columns and the named starter filters saved inside it. Your lab notes record the sniffer board, its firmware version, and the plugin version. On air use of the setup is a bench step; Lesson 12 labels it a desk expectation throughout.
