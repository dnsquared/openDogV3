# openDog V3 — Single-Leg Prove-Out Guide

The cheap, low-risk way in. James says it outright (V5 @ 13:08, V6 @ 13:51): **don't buy everything — prove one slice first.** This guide breaks that into three escalating slices, each ending in a **go/no-go decision** before you spend more. Total to reach the first real "it moves under control" moment is roughly **$210–250**, not $2000 (the controller is most of it).

> **Why this order?** The riskiest part of the whole project isn't printing or bolts — it's getting an ODrive to drive a brushless motor using an absolute encoder. So we kill *that* risk first (Slice A) on a bare motor, before any gearbox exists. Then drivetrain (Slice B). Then the firmware (Slice C).

```
Slice A  ── electrical ──   1 ODrive + 1 motor + 1 encoder, spinning under control   (~$230)
   │  go/no-go: can you configure ODrive + AS5047 and hold position?
Slice B  ── mechanical ──   + 1 more motor/enc + 2 cycloidal drives + belt = a 2-joint test leg
   │  go/no-go: does the drivetrain hold load and back-drive like a spring?
Slice C  ── firmware ───    build a full 3-axis leg, run openDogV3.ino against it
   │  go/no-go: ready to commit to the full robot?
```

---

## Slice A — Electrical bring-up (do this first) 🔌

**Goal:** one brushless motor doing closed-loop position control off an absolute encoder, driven by `odrivetool` on your PC. No gearbox, no printed parts. This proves the single hardest skill.

### Shopping list — Slice A
| Item | Qty | ~Price | Source (2026) | Notes |
|---|---|---|---|---|
| **ODESC 3.6 dual-drive**, 56 V | 1 (buy 2) | ~$100 | [Sequre](https://sequremall.com/products/odesc-dual-drive) · [AliExpress](https://www.aliexpress.com/item/1005007734783069.html) | **These are your final boards** — Slice A runs on the exact hardware you'll build with. **Buying 2 (= 4 axes) covers Slices A–C**; 4 more for the full dog. ~$100 normal (occasional ~$60 sales); **2–3 week shipping, not stocked on Amazon.** Runs the firmware the repo code targets. *Not* the single-drive V4.2 — see the driver decision log in `BOM.md`. |
| **EaglePower LA-8308 90 KV** motor | 1 | ~$61 | [AliExpress (BDUAV)](https://www.aliexpress.com/item/4000563826414.html) | The motor the dog uses. Buy 1 now; you'll need 12 eventually. |
| **AS5047P** encoder breakout | 1 | ~$10–18 | [Amazon (Rakstore)](https://www.amazon.com/Rakstore-AS5047P-Encoder-Modulation-Magnetic/dp/B09LTYXB5Z) · [MakerBase](https://makerbase3d.com/product/makerbase-as5047p-doggo-odrive-simplefoc-magnetic-spi-abi-encoder-adapter-board-based-on-as5047p-ts_ek_ab/) · [Tindie](https://www.tindie.com/products/smallrobots/as5047p-encoder-board-for-robots-motor-control/) | Get the "ODrive/SimpleFOC Doggo" variant — same chip James used. |
| **Diametric magnet**, ~6 mm | 1 | ~$3 | any (often included w/ encoder) | Must be **diametrically** magnetised, glued on the motor shaft centre, ~1 mm from chip. |
| **Bench PSU**, ~24 V, current-limited, ≥5 A | 1 | varies | (use what you have) | Safer than a LiPo for first power-up — current-limit it. ODrive 56 V board runs fine at 24 V for bench testing. |
| **Brake/regen resistor** | 1 | ~$5 | (often ships with ODESC) | ODrive can over-volt on deceleration without one. Check your ODESC box first. |
| **USB cable** (data) + jumper wires | — | — | — | For `odrivetool`. |

### Steps — Slice A
1. **Prep the AS5047 board** (V3 @ 5:09): remove the big jumper, hard-jumper to **3.3 V**, and move **R1 → R2** (it's a 0 Ω link — i.e. short that position). It will *not* work with the jumper on.
2. Glue the diametric magnet centred on the motor shaft; mount the encoder board ~1 mm above it (a printed bracket helps but tape works for the bench).
3. Wire motor 3 phases → ODrive M0; encoder SPI (CS/CLK/MISO/MOSI/3V3/GND) → ODrive. Connect the brake resistor. Power from the **current-limited** bench PSU.
4. Install ODrive's Python tool on your PC: `pip install odrive`, then run `odrivetool`.
5. **Flash latest firmware:** `odrivetool dfu`.
6. Configure motor + **AS5047 in absolute SPI mode** per the [ODrive encoder docs](https://docs.odriverobotics.com/v/latest/encoders.html) (set pole pairs, current limit ~10–15 A to start, calibration current, etc.).
7. Run **motor calibration**, then **encoder offset calibration** — with an absolute encoder this is a *one-time* step; afterwards it boots straight into closed loop.
8. Command closed-loop position: `odrv0.axis0.controller.input_pos = 1` etc.

✅ **GO if:** the motor snaps to commanded positions, holds position stiffly, and you can feel it fight back when you twist it by hand (that's the "virtual spring"). 

❌ **If you're stuck here**, you've spent ~$120 and learned the ODrive workflow — far better than discovering it after building 12 gearboxes. This is the single most common place beginners stall.

---

## Slice B — Mechanical test leg 🦿

**Goal:** reproduce James's V1 test leg — a **2-joint leg** (shoulder + knee, belt-driven 1:1) on **two cycloidal drives** driven by your one ODESC (it drives 2 motors). Proves the drivetrain holds load and back-drives like a spring.

### Additional shopping list — Slice B (on top of Slice A)
| Item | Qty | Source / spec | Notes |
|---|---|---|---|
| EaglePower LA-8308 90 KV motor | +1 | as above | 2nd joint |
| AS5047P encoder + magnet | +1 | as above | 2nd joint |
| **Cycloidal-drive bearings** — see table below | for **2 drives** | any bearing supplier | Generic; order by standard code. |
| **4 mm steel rod** | ~22 pins × 31 mm | eBay/hardware | Cut on printed `jig.stp`, deburr both ends. |
| **HTD 5M belt**, 9 mm wide, 630 mm | 1 | [BeltingOnline](https://www.beltingonline.com/htd-pitches-c-40_253/5mm-htd-timing-belts-p-4575.html) | The knee belt. |
| **Carbon-fibre tube**, 28 mm OD, ≥1 mm wall | ~1 m | [eBay](https://www.ebay.co.uk/itm/333609712747) | Lower leg + body stub. |
| **Nylon washers** (4 mm ID) + **5 mm spacers** | ~64 + ~32 | [Nyfast](https://nyfast.com) | Set bearing heights / stack spacing. |
| **Fasteners** | per drive | see `BOM.ods` | Key ones: M4×40 socket cap (cam) + M4 nyloc per drive; M4 output bolts/nuts/washers. |
| Filament (**PLA**) | — | — | Internals: use `openDog V3_internals_toleranced.stp`, 4 perimeters, 30–40% infill. |

#### Bearing codes (orderable from any supplier — sizes mapped to standard designations)
| Role | Size (OD×ID×W mm) | Standard code | Per cycloidal drive | For 2 drives | + per test-leg knee |
|---|---|---|---|---|---|
| Cycloidal internals | 12×4×4 | **604ZZ** | 32 | 64 | — |
| Cam / cap | 32×20×7 | **6804ZZ** | 3 | 6 | — |
| Top & bottom | 85×65×10 | **6813ZZ / 61813** | 2 | 4 | — |
| Shoulder end support (thin-wall) | 37×30×4 | **61706ZZ / 6706** | — | — | 1–2 |
| Knee joint / lower-leg ("skate") | 22×8×7 | **608ZZ** | — | — | ~4 |
| Belt idlers | 19×6×6 | **626ZZ** | — | — | 4 |

> Buying small bearings in bulk (e.g. 604ZZ ×100) is far cheaper per unit and you'll need 384 of them for the full dog anyway — but for the test leg you only *need* ~70.

### Steps — Slice B
Follow `ASSEMBLY.md` **Stage 1** (build each cycloidal drive — discs, bearings on nylon washers, 5 mm spacer, second disc, cam cap into captive nyloc) then **Stage 2** (assemble the 2-joint test leg: lower-leg pulley on 608 bearings, 1:1 belt, two 626 idlers to tension, **double-brace the pulley end so the belt can't skip**). Mount your 2 motors/encoders and drive both axes from the one ODESC.

✅ **GO if:** the leg moves smoothly, back-drives like a damped spring, and holds load (James got ~18–19 kg at 45°, ~25 kg near-straight, at 20 A — V1 @ 10:52). Listen for a *clattery* drive = a bearing/spacer/disc issue to fix now.

---

## Slice C — Run the firmware on a full leg 💻

**Goal:** build one **complete 3-axis leg** (hip side-to-side + shoulder fore-aft + knee = 3 drives, **2 ODrives**) and run the actual `openDogV3.ino`. James confirms the code runs on a partial robot because the four legs are mirrored in the kinematics — *"just comment out the lines for the o drives that aren't there"* (V6 @ 14:00).

### Additional parts — Slice C (headline items; full detail in `BOM.md`)
- **+1 ODESC 3.6** (3 axes need 2 dual-drive boards), **+1 motor + encoder** (3rd axis), **+1 more cycloidal drive** + the side-to-side pivot parts (thin-wall 61706 bearings in a printed plug).
- **Teensy 4.1** (~$32–40, [SparkFun](https://www.sparkfun.com/teensy-4-1.html) / [PJRC](https://www.pjrc.com/store/teensy41.html)) — the dog's brain.
- **Remote:** **Teensy 4.0** (~$24, [SparkFun](https://www.sparkfun.com/teensy-4-0.html)) — substitute for the discontinued Teensy LC; verify pin mapping in `Remote.ino`. Plus 2× **nRF24L01** radios, 2× 3-axis joysticks, an I2C LCD + 5 V↔3.3 V level shifter.

### Steps — Slice C
1. Flash `Code/openDogV3/*.ino` to the Teensy 4.1; flash `Code/Remote/Remote.ino` to the remote.
2. In `openDogV3.ino`, comment out the ODrive objects/commands for the axes you haven't built (keep the two for your leg).
3. **Calibrate the default encoder offsets** in the variable-declaration block so menu **mode 3** homes your joints to 45° (the CAD default).
4. Set `vel_limit`/`vel_limit_tolerance = math.inf` in `odrivetool` (README).
5. Walk the LCD menu: `1` closed-loop → `3` home to 45° → `4` raise gains → `5` IK demo. Watch your one leg track the kinematics.

✅ **GO / final decision:** if one leg tracks the IK demo cleanly, you understand every subsystem — printing, the drive, ODrive config, wiring, and the firmware. *Now* it's rational to buy the other 10 motors, 4 ODrives, and 300-odd bearings and build the whole dog (`ASSEMBLY.md` Stages 3+).

---

## Rough cost ladder
| Reach | Adds | Cumulative |
|---|---|---|
| Slice A | ODESC ($100) + 1 motor + 1 enc + magnet + resistor + jumpers (PSU owned) | **~$210–250** |
| Slice B | +1 motor/enc, ~70 bearings, belt, rod, tube, PLA, washers/spacers | **~$400–470** |
| Slice C | +1 ODESC, +1 motor/enc, Teensy 4.1 + remote (Teensy 4.0) + 2 radios + LCD | **~$700–800** |
| Full dog | the remaining ~9 motors, **4 ODESC**, ~300 bearings, fasteners, batteries, etc. | ~$2–3k |

*(Prices are ballpark mid-2026, USD, ex-shipping. The **ODESC dual-drive at ~$100/board is the single biggest line** — 6× ≈ $600 — and the one item not on Amazon. Bulk bearings are where you save vs the original BOM's per-unit links.)*
