# LE Audio, Engineered: labs

Step by step lab companion for **LE Audio, Engineered**, the Bit to Board project course that builds an Auracast pair: two nRF5340 Audio DKs that first form a private LE Audio unicast link (Act 1), then flip into an Auracast broadcast (Act 2).

Clone this repo:

```bash
git clone https://github.com/learnbittoboard/le-audio-engineered.git
# or over SSH:
git clone git@github.com:learnbittoboard/le-audio-engineered.git
```

Every chapter of the course has a directory here. Every step inside it is a small, complete unit: what it is for, the exact commands, and the expected result. The lessons explain; these pages do.

## Environment

- nRF Connect SDK **v3.4.1** (Zephyr 4.4.2), toolchain via the nRF Connect for VS Code extension (pinned v3.4.1 bundle; commands run in its toolchain terminal in VS Code)
- Board target: `nrf5340_audio_dk/nrf5340/cpuapp`
- Reference application: `nrf/applications/nrf_audio` in the SDK tree
- Hardware: **two** nRF5340 Audio DKs (PCA10121). Two boards is a requirement of the project, not an upgrade: a link and a broadcast both need a talker and a listener.
- Read along works: every step states its expected result in the text, so the steps can be studied with no hardware at all. Flashing and listening steps are labelled as bench steps in the lessons.

## Layout

| Directory | Chapter |
|---|---|
| `ch01/` | Why audio needed a second Bluetooth: pair inventory |
| `ch02/` | The reference application, taken apart: build all four roles, config checkpoints |
| `ch03/` | The transmitter: USB audio in, set identity |
| `ch04/` | The receiver: headset build, the codec path |
| `ch05/` | CIS under the microscope: bring up annotation, bidirectional |
| `ch06/` | Tuning the stream: preset swap, frame duration |
| `ch07/` | When it stutters: planted faults, log forensics |
| `ch08/` | Act 2, the Auracast source: broadcast source, encrypted broadcast |
| `ch09/` | The Auracast sink: broadcast sink, stream selection |
| `ch10/` | One pair, two personalities: release pair, DFU image |
| `ch11/` | Capstone: the ship the pair checklist |
| `ch12/` | Reading the air: sniffer setup, read the pair |
| `ch13/` | Case File: the sync that died on a schedule (the case worksheet) |

## The one habit

Every build step in this repo ends the same way: read the resolved configuration in the build's `nrf_audio/zephyr/.config` and prove the build is the role it claims to be. Directory names are claims. Resolved symbols are proof.
