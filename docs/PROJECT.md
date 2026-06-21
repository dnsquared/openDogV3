# openDog V3 — Build Project

Our working notes for building James Bruton's openDog V3 (a fork of [XRobots/openDogV3](https://github.com/XRobots/openDogV3)).

> This file and everything in `docs/` is **ours** — it won't conflict when we pull upstream updates. The top-level `README.md` is James's original project description; leave it for upstream.

## What's in this repo

### CAD (`/CAD`)
| File | What it is |
|---|---|
| `openDog V3.zip` → `openDog V3.stp` (35 MB) | Full assembly — every part |
| `openDog V3_internals_toleranced.stp` | Cycloidal drive internals, sized for printing |
| `openDog V3_foot.stp` | Silicone foot mould |
| `bone.stp` | Leg bone |
| `Remote.stp` | Handheld controller |
| `jig.stp`, `openDogV3_jig_knee.stp` | Assembly jigs |

### Code (`/Code`) — Arduino / Teensy sketches
- `openDogV3/` — main robot firmware (Teensy 4.1): main loop, kinematics, ODrive init, stick thresholds
- `openDogV3_experimental_stability/` — alternate firmware with IMU-based balancing
- `Remote/` — handheld controller firmware (Teensy LC)

**Libraries the firmware needs** (install in Arduino IDE): `ODriveArduino`, `RF24` + `nRF24L01`, `LiquidCrystal_I2C`, `Ramp`, and `MPU6050` + `I2Cdev` (experimental build only). `SPI`/`Wire` ship with Arduino.

### Docs (`/docs`) — ours
- `PROJECT.md` — this file
- `ASSEMBLY.md` — **assembly manual** extracted from the build videos, ordered as build stages
- `BOM.md` — **re-checked bill of materials** (flags the obsolete ODrive v3.6 / Teensy LC)
- `transcripts/` — raw video transcripts pulled from YouTube (source material for the manual)

### Other
- `BOM.ods` — James's original bill of materials (2022; some supplier links + parts likely outdated — we're re-checking)
- `README.md`, `LICENSE` — upstream

## Build reference videos
The build is documented across the [YouTube playlist](https://www.youtube.com/playlist?list=PLpwJoq86vov8uTgd8_WNgBHFpDYemO-OJ), not in text. See `docs/transcripts/README.md` for the list and our extraction status.

## LCD menu reference (from README)
`0` idle · `1` closed-loop control · `2` clear stirrups · `3` home to 45° · `4` raise gains · `5` IK demo · `6` walk · `10` rest feet on stirrups
