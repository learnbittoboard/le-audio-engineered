# Step 01: Pair inventory and out of box test

Chapter 1 of LE Audio, Engineered. Stack: NCS v3.4.1, Zephyr 4.4.2, GCC 14.3.0. Board target: nrf5340_audio_dk/nrf5340/cpuapp.

## Purpose

Prove both nRF5340 Audio DKs are healthy in their factory state, give each board a durable label, and record the inventory every later chapter refers to. There is no build in this step. The full reasoning and the LED sign definitions are in the lesson; this file is the bench procedure and the worksheet.

## Commands and actions

1. Label one board A and one board B with tape and a marker, away from the LEDs.
2. Connect Board A by USB, switch POWER on, and observe the RGB LEDs, the OB/EXT LED, and LED3.
3. Read the board serial number from the terminal:

```bash
nrfutil device list
```

4. Confirm the printed serial number matches the sticker on Board A, and write it in the worksheet below.
5. Power Board A off, disconnect it, and repeat steps 2 to 4 for Board B.
6. Confirm DEBUG ENABLE is on for both boards, find POWER, VOL-, VOL+, PLAY/PAUSE, BTN 4, BTN 5, and RESET by touch on each board, then power both boards off.

## Inventory worksheet

Copy this table into your notes and fill one row per board.

| Board label | SEGGER serial number | RGB observed | OB/EXT observed | LED3 observed | Planned Act 1 role | Notes |
|---|---|---|---|---|---|---|
| A | | | | | Gateway, transmitter | |
| B | | | | | Headset, receiver | |

## Expected result

Each board, in factory state, shows the documented out of box signs: RGB solid yellow (factory firmware, not yet programmed as gateway or headset), OB/EXT solid green (3.3 V available, on board hardware codec selected), and LED3 blinking green (application core running). Both serial numbers appear in the `nrfutil device list` output and are recorded against the board labels and the Act 1 role assignments, Board A as gateway and Board B as headset.

A board that does not show all three signs, or never appears in `nrfutil device list`, is a hardware or connection problem to resolve before Chapter 3. Try another USB cable and port first, and confirm the POWER switch is on.
