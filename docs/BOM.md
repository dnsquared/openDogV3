# openDog V3 — Bill of Materials (2026 re-check)

Re-checked version of James's original `BOM.ods` (2022). Quantities are from his BOM; the **2026 notes** column flags obsolete parts, dead links, and current alternatives. Verify quantities against the CAD before ordering — treat this as a shopping guide, not gospel.

> ⚠️ **Biggest changes since 2022:** the **ODrive v3.6 is officially NRND** (not recommended for new designs) and the **Teensy LC is effectively discontinued**. See notes below.
>
> 🛒 **Just want to start?** Don't buy this whole list. See **`PROVE-OUT.md`** for the ~$230 single-leg slice with current 2026 sources for only the parts you need first.

## Electronics — the parts that actually went obsolete

| Part | Spec | Qty | 2026 status / recommendation |
|---|---|---|---|
| **Motor driver** | ODrive v3.6 | 6 (dual-axis, 2 motors each) | ⚠️ **ODrive v3.6 is NRND.** Official replacement is the **ODrive S1**, but it's *single-axis* → you'd need **12** boards + a firmware/code rewrite (new boards run ODrive fw 0.6.x with a changed protocol; the existing `.ino` targets the old ASCII protocol). **Pragmatic path:** use an **ODESC 3.6 _dual-drive_** clone — same schematic, same firmware, drop-in for the v3.6, keeps the 6-board count, and works with the existing code. This is what most current openDog-style builders do. **⚠️ Must be _dual-drive_, not single-drive:** the common Amazon **ODESC V4.2 is single-drive** — building the dog on those needs **12 boards**, which exceeds the **Teensy 4.1's 8 hardware serial ports** (the code uses 6, one per dual board) and needs a code rewrite. A single V4.2 is still handy as a **bench rig** for Slice-A testing + calibrating each motor/encoder before assembly (see `PROVE-OUT.md`). Buy the dual-drive 3.6 from [Sequre](https://sequremall.com/products/odesc-dual-drive)/AliExpress, not Amazon. |
| **Microcontroller (remote)** | Teensy LC | 1 | ⚠️ **Discontinued / NRND.** Replace with **Teensy 4.0** (PJRC's recommended successor). Pin-compatible enough for the remote; double-check 3.3 V logic & pin numbers in `Remote.ino`. |
| **Microcontroller (dog)** | Teensy 4.1 | 1 | ✅ Current. Still made (now produced at SparkFun since 2025). |
| **Motor encoders** | AMS AS5047P | 12 | ✅ Current. Used in absolute position mode. Verify ODrive fw still supports your chosen encoder mode if you switch off v3.6. |
| **IMU** | MPU6050 | 1 | ✅ Available (generic). Used by the experimental_stability firmware. |
| **Radio** | nRF24L01 | 2 | ✅ Cheap/generic. Use the **+PA/LNA** version with a stable 3.3 V supply (add a decoupling cap) for range. |
| **Voltage regulator** | 5 V adjustable | 1 | ✅ Generic. Any solid 5 V/3 A buck works. |
| **LCD** | I2C 16×2 (dog) / 20×4 (remote) | 1 + 1 | ✅ Generic. Note code uses addr `0x27` — some modules are `0x3F`. |
| **Connectors** | 25-way D-sub M + F | 1 + 1 | ✅ Generic. |
| **Switches** | latching power, ×2 E-stop, 5× remote toggles, 4× remote momentary | — | ✅ Generic. |
| **Volt meters** | panel volt meters | 2 | ✅ Generic. |
| **Joysticks** | 3-axis | 2 | ✅ Generic hobby joysticks. |
| **Battery (main)** | 6S high-drain LiPo, ~4000 mAh, 60C | 1 | ✅ Must source enough current. Needs a balance charger. |
| **Battery (electronics)** | 2S/3S small | 1 | ✅ Generic. |
| **Encoder cable** | 6-core foil-shielded | ~6 m | ✅ Generic shielded signal cable. |

### Driver decision (Jun 2026) — settled
Going with **ODESC 3.6 dual-drive** clones from Sequre — the drop-in for the obsolete ODrive v3.6 that the repo firmware already targets (zero code rework). **Ordered 2 now** (= 4 axes, enough to carry the whole single-leg prove-out, Slices A–C); 4 more for the full robot. Alternatives evaluated and set aside as too complex/risky for a first iteration: single-drive ODESC V4.2 (12 boards → exceeds Teensy serial ports → CAN rewrite), genuine single-axis ODrive S1 (new firmware + CAN rewrite + ~$2k), and SimpleFOC (you author/tune the drive firmware, and the cheap B-G431B-ESC1 only sustains ~4–8 A vs the dog's 15 A). Revisit single-axis + CAN only if we ever design our own v2.

## Mechanical / drivetrain (specs don't go obsolete — supplier links may be dead)

| Part | Spec | Qty | Notes |
|---|---|---|---|
| Brushless motors | 9225 90 KV | 12 | Turnigy **Multistar 9225 90KV** (HobbyKing/eBay) or **EaglePower 8308 (9225 can) 90KV** (AliExpress, ~$60 ea). Both still findable in 2026; EaglePower is the more reliably stocked. |
| Knee drive belts | HTD 5M pitch, 630 mm | 4 | |
| Carbon fibre tube | 28 mm OD, ≥1 mm wall | 4 × 1 m | Cut to CAD; off-cuts do the legs. Foot tube is glued in. |
| 4 mm steel bar | 4 mm × 31 mm | 132 | Bearing-mount pins around cycloidal drives. |

### Bearings
Sizes mapped to **standard designations** so you can order from any bearing supplier (cheaper in bulk than the original per-unit links).

| Where | Size (OD×ID×W) | Standard code | Qty |
|---|---|---|---|
| Cycloidal drive internals | 12 × 4 × 4 | **604ZZ** | **384** (32/drive) |
| Cam & cap | 32 × 20 × 7 | **6804ZZ** | 36 (3/drive) |
| Top/bottom drive | 85 × 65 × 10 | **6813ZZ / 61813** | 24 (2/drive) |
| Thin-wall (shoulder end) | 37 × 30 × 4 | **61706ZZ** | 8 |
| Knee / lower-leg ("skate") | 22 × 8 × 7 | **608ZZ** | 8 |
| Knee idler | 19 × 6 × 6 | **626ZZ** | 16 |

### Fasteners & nylon
| Part | Spec | Qty |
|---|---|---|
| Motor mount screws | M4 12 mm countersunk | 48 |
| Cycloidal cam bolt | M4 40 mm socket cap | 12 |
| Cycloidal cam locknut | M4 nylon insert | 12 |
| Cycloidal output locknut | M4 nylon insert | 60 |
| Cycloidal output washer | M4 | 60 |
| Cycloidal cam screw | M3 15 mm | 48 |
| Cycloidal output bolt | M4 hex 80 mm (cut to length) | 60 |
| Drive case screws | 2.9 mm × 18 mm c/s pozi | 132 |
| Side-to-side mount screws | 2.9 mm × 18 mm c/s pozi | 48 |
| Leg screws | 2.9 mm × 18 mm c/s pozi | 56 |
| Nylon washers | 4 mm ID | 384 |
| Nylon spacers | 4 mm ID × 5 mm | 192 |

## Consumables (from README, not in BOM.ods)
- **PLA** for all printed parts (large parts ~15% infill, 3 perimeters, 0.3 mm layers; cycloidal internals 4 perimeters, 30–40% infill).
- **25A Shore platinum-cure silicone** + pigment for the moulded feet.

## Open verification items
- [ ] Confirm ODESC 3.6 vs ODrive S1 decision (cost vs. drop-in compatibility) before ordering drivers.
- [ ] Confirm Teensy 4.0 pin mapping works for `Remote.ino`.
- [ ] Cross-check every quantity against the assembled CAD.
- [ ] Re-source dead supplier links (most 2022 eBay/AliExpress listings are gone).
