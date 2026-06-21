# openDog V3 — Assembly Manual

Extracted from James Bruton's [build playlist](https://www.youtube.com/playlist?list=PLpwJoq86vov8uTgd8_WNgBHFpDYemO-OJ) (transcripts in `docs/transcripts/raw/`), cross-referenced with the CAD and code in this repo. Timestamps like *(V2 @ 4:35)* point back to a video + time so you can watch the exact step.

> **Video map:** V1 = Cycloidal drive & test leg · V2 = Mechanical build #1 · V3 = Electronics #2 · V4 = Battlefield turret (optional) · V5 = How I made it walk · V6 = Climbs over obstacles (dynamic stability).

---

## ⭐ Read this first — James's own advice (V5 @ 13:08, V6 @ 13:51)

> "This **shouldn't be your first project**. Don't go and blow $2000 on everything."

His recommended on-ramp, which is perfect for a father-son project:
1. Buy **one ODrive, one motor, one encoder**.
2. Build **one or two cycloidal drives**.
3. Build **one leg** (two joints).
4. Get the **code running on that one leg** — the firmware works fine on a single leg because the other three are just mirrored in the kinematics; you comment out the ODrive lines for the legs you don't have.
5. Only *then* decide whether to commit to the full robot.

This manual is ordered to follow that path: **Stages 1–2 are the cheap prove-out. Stages 3+ are the full build.**

---

## Stage 0 — Prerequisites

**Tools:** 3D printer (large bed helps), Dremel with cut-off discs, rubber mallet, silicone grease, super glue + activator, soldering iron, calipers.

**Printing (from README):** all parts in **PLA**. Large parts ~15% infill, 3 perimeters, 0.3 mm layers. Cycloidal internals: 4 perimeters, 30–40% infill. Use the **`openDog V3_internals_toleranced.stp`** cycloidal internals — James re-toleranced them for real printers (V5 @ 13:02).

**Arduino libraries** (Stage 6): `ODriveArduino`, `RF24` + `nRF24L01`, `LiquidCrystal_I2C`, `Ramp`, and `MPU6050` + `I2Cdev` (stability build only).

**Parts:** see `docs/BOM.md` — and note the **ODrive v3.6 / Teensy LC are now obsolete** (substitutes documented there).

---

## Stage 1 — Build one cycloidal drive (the core skill) — *V1, V2 @ 3:57–6:14*

Each of the 12 joints is a **double cycloidal drive** (two discs, 180° out of phase, so it doesn't vibrate). 10:1 reduction. This is the part you must get right — build one and make it run smoothly before anything else.

Per drive you need (see BOM): **32 small bearings** (11 around each disc + 5 centre, ×2 stages... James buys 384 total for 12 drives), **3 cam/cap bearings**, **2 large 85 mm bearings**, ~**11 × 4 mm steel pins** (cut on the printed `jig.stp`, deburr both ends), **nylon washers + 5 mm spacers**, the cam, and fasteners.

Assembly order (V2):
1. Mount the **large bearings** in the top & bottom of the drive casing. Motor screws to the back.
2. Fit the **output shaft** into the bottom bearing (spins independently of the cam).
3. **First stage:** lay in the ring of bearings on their pins — **every bearing sits on a nylon washer** to set its height (V2 @ 4:41). Silicone grease everything.
4. Slot in the **first cycloidal disc**. It should *scrape* past the bearings, not roll — that's normal.
5. Add a **5 mm nylon spacer**, then the second ring of bearings.
6. The middle cam is **offset 180°** for the second disc. Add washer, then the **second disc**.
7. Washer on top, then the **top casing**.
8. **Cam cap:** an M4 socket-cap bolt runs all the way through into the **captive M4 nylon lock-nut** you placed in the cam at the start (it's a very tight fit). A bearing on this cap holds the cam centred while the output rotates the other way.

✅ **Success check:** turns smoothly both directions, is **back-drivable**, and has **minimal backlash** (the correctly-shaped discs are what give low backlash — V2 @ 6:08).

> Bearings vs bushings: James tested nylon bushings — cheaper but noticeably worse. **Use bearings** (V1 @ 11:55, V2 @ 3:07).

---

## Stage 2 — Build one test leg (prove-out) — *V1 @ 7:54–11:48*

A leg = a **shoulder** drive + a **knee** drive, both motors at the top, knee driven by a **1:1 HTD belt** (9 mm wide, 5 mm pitch).

1. Build the leg body with the two drives (V3 builds the drive into the leg casting so there are fewer pieces to bolt).
2. Lower leg has a **pulley printed in**, running on **8 mm ID skate bearings**, driven by the motor pulley.
3. Fit the **belt**, then the **two idlers** (6 mm ID bearings, on 6 mm bolts) to tension it.
4. **Double-brace the pulley end** so the assembly can't flex — this is what stops the **belt skipping** under load (V1 @ 11:26). If the belt later stretches, just print **bigger idlers**.

✅ **Load check (V1 @ 10:52):** at 45°, James held ~18–19 kg before belt skip; nearly straight, ~25 kg. Each leg weighs ~3 kg. Plenty for a <25 kg dog lifting two legs at a time. He ran the motor at **20 A holding** here.

This is also where you first drive a motor with an ODrive + encoder — see Stage 5.

---

## Stage 3 — Full mechanical assembly — *V2*

Now repeat Stage 1 twelve times and assemble the body. **Motors:** 12 × EaglePower LA-8308 (9225 can, 90 KV). **Chop the central pin off each motor** with a Dremel cut-off disc, masking the motor so swarf doesn't get inside (V2 @ 3:57). Mount each into its printed housing; the cam mounts directly on top with a captive nut.

Build groups (V2 @ 6:21–10:03):
- **4 "first" drives** (the hip/shoulder pairs).
- **4 side-to-side drives** — these have a **gap at each end for a pivot point**, making the perpendicular axis that lets the legs move sideways. Supported on **thin-wall bearings** seated in a **printed plug** (so it's replaceable). Use a rubber mallet for the tight fits.
- **4 knee drives** — pulley + cap riding on a bearing already placed in the leg; lower-leg pulley; **1:1 belt**; two idlers; plus a bearing in the upper-leg half that the pulley boss locates into, to stop skew/belt-skip.

**Body:** push the **carbon-fibre tubes** (28 mm OD) into the drive units — very tight fit. Build the **printed stand** with hooks so you can lift the dog in/out and rest it. There are **holes for tie-bars** in case parts creep off the tubes.

**Range-of-motion note (V2 @ 11:08):** if a leg swings out too far it hits the body tube — fine for walking (~50 mm of travel), and the legs **fold up flat for transport**.

Mass so far ~20 kg; ~25 kg with electronics (Spot Mini ≈ 30 kg for reference).

---

## Stage 4 — Electronics — *V3*

Architecture: **6 ODrives** (each drives 2 motors) ← **Teensy 4.1** over **6 hardware serial ports**. Remote talks to the dog over **nRF24L01**.

1. **Encoder prep (AS5047, per board, V3 @ 5:09):** remove the big jumper, hard-jumper to **3.3 V**, and move **R1 → R2** (a 0 Ω resistor — i.e. short it). Without removing the jumper it will not work at all.
2. Wire encoders with **6-core foil-shielded cable**; build **strain reliefs** (glued into a printed block) so nothing pulls on the solder joints. Magnet block sits on the back of each motor, near (not touching) the chip.
3. Mount ODrives in the **printed cages** (hinge down, zip-tied around the carbon-fibre tube) so every connector stays reachable. Keep the **USB port accessible** for config.
4. **Power:** 6S ~4000 mAh LiPo. Distribute with **8 mm bolts** + silicone cables/eyelets to all six ODrives. (Build a cover so you can't drop metal across it.)
5. **Logic board (PermaProto):** Teensy 4.1 + I2C **LCD** (needs a **5 V↔3.3 V level shifter**) + **MPU-6050 IMU** + **nRF24** radio. Wire the 6 serial ports to the ODrives.
6. **Removable electronics:** route everything through a **25-way D-sub** so the whole electronics tray can come out.
7. **E-stop:** latching switch that **resets all ODrives and kills the motors**. Plus two panel **volt-meters** (main + logic battery).

> James runs the motors at **15 A** here (vs 30 A on openDog 2) so they don't overheat — the 10:1 drives give the torque back.

---

## Stage 5 — ODrive config & encoder calibration — *V3 @ 2:19–5:04*, README

This is the fiddly part. Do it on the **test leg first**.

1. **Flash latest firmware:** `odrivetool dfu` (Python tool) on each of the 6 boards.
2. Configure the **AS5047 in absolute (SPI) mode** per the [ODrive encoder docs](https://docs.odriverobotics.com/v/latest/encoders.html).
3. Run **offset calibration once** — with absolute encoders the ODrive then powers straight into closed-loop on every future boot (no per-power-up calibration dance).
4. From the README:
   - In `odrivetool`: `import math`, then set `vel_limit` and `vel_limit_tolerance` to `math.inf` (stops motors disarming in some conditions).
   - The code's **default encoder offsets** (in the variable-declaration block of `openDogV3.ino`) **must be calibrated** to your build so menu mode 3 homes the joints correctly.

> ⚠️ If you use newer ODrives (S1/Pro) instead of v3.6/ODESC, they run fw 0.6.x with a changed protocol — the supplied `.ino` targets the old ASCII protocol and will need rework. See `docs/BOM.md`.

---

## Stage 6 — Firmware & first power-up — *V3 @ 9:47*

Flash `Code/openDogV3/openDogV3.ino` (+ its `.ino` tabs: `kinematics`, `ODriveInit`, `thresholdSticks`) to the Teensy 4.1, and `Code/Remote/Remote.ino` to the remote.

**LCD menu (from README) — the operating sequence:**

| Mode | Action |
|---|---|
| 0 | idle (default at power-up) |
| 1 | motors → closed-loop control |
| 2 | move legs out to just clear the stand stirrups (1–2 mm) |
| 3 | home: shoulder & knee to 45° (the CAD default — this is what the offsets calibrate to) |
| 4 | turn up position / velocity / integrator gains |
| 5 | inverse-kinematics demo (6-axis translate + rotate) |
| 6 | walking |
| 10 | rest feet back on the stirrups |

Remote: scroll with up/down, **Select** to confirm. There's a **motor-enable switch** (must be on) and a **reverse switch** that flips four axes so the dog walks backward (handled on the remote, not in the dog).

**Default-position requirement (V6 @ 1:54):** because the encoders only see 360° and the drives are 10:1, each joint must start within **36°** of correct — that's why you rest the legs in the stand's **stirrups** (now fold-out) at known positions before powering up.

---

## Stage 7 — Walking & tuning — *V5*

- **Kinematics** (`kinematics.ino`): plain high-school trig, split into triangles — leg-length, fore/aft, side-to-side (with the extra hip offset), then pitch/roll/yaw. One function runs 4×/cycle (once per leg; left/right & front/back are mirrored). (V5 @ 0:55–3:24)
- **Gait:** the **Ramp** library interpolates between foot waypoints so the feet draw straight lines and joints arrive together; longer steps get proportionally longer to complete. (V5 @ 4:14)
- **Tuning the virtual spring:** turn up the ODrive **position gain** *differently per joint* (knee / shoulder / hip) so the legs act like a **damped spring** absorbing impact. Steps are deliberately **short and fast** so it all averages out. Overdo the foot travel and the spring no longer matches the forces and it gets unstable. (V5 @ 5:01)
- **Feet** (V5 @ 9:02): cast in the printed mould — **25A Shore platinum-cure silicone**, A:B mixed equal by weight, poured around the rigid insert (no vacuum chamber needed if you pour a thin stream). **Super-glue the insert to the carbon tube** — zip-ties alone let it rotate and the dog "slips on ice". Carbon tube is glued into both the lower leg and the foot insert.

---

## Stage 8 — Dynamic stability (optional, experimental) — *V6*

This is the **`Code/openDogV3_experimental_stability/`** firmware. The base walk uses **hard-coded timers and no balance**; this adds IMU feedback.

- Lengthened the **lower leg** by using the **centre of the ball foot** as the IK point (geometry change, not maths). (V6 @ 1:08)
- Read **MPU-6050** pitch & roll; smooth with a **first-order filter** to stop oscillation. (V6 @ 5:35)
- Make the **translation axes respond to tilt** (lean into a hill — legs behind going up, in front going down), then also mix in the **pitch/roll rotation axes** already in the kinematic model. (V6 @ 8:23)

Result: walks over lumps/obstacles and recovers from kicks within 1–2 steps instead of "reeling around like a drunken sailor."

---

## ⚠️ Known design limitations (straight from James — set expectations)

- **Large sideways offset between hip joint and foot** (the legs must clear the fat cycloidal-drive bodies). This puts big leverage on the hip joint, so the dog **can't walk slowly or balance on two legs** — it relies on stepping *fast*. openDog 2 didn't have this because its belt drives let the joint sit directly above the foot. (V5 @ 8:05, V6 @ 3:00)
- Because of that offset, **force-control experiments don't really pay off** on this design. (V6 @ 13:08)
- A little **backlash** in each gearbox (inherent to cycloidal vs tight belts). (V3 @ 11:31)
- James explicitly **doesn't plan an openDog 4**, but says if he did he'd offset the hip joint with a belt + cycloidal combo to fix the above. (V6 @ 13:33)

---

## Optional — Battlefield "Ranger" turret — *V4*

A separate promo build, **not needed for the dog**: a pan/tilt rack (2 carbon tubes + 2 servos) on top of the chassis, a foam-dart gun, an **Arduino Mega + nRF24 + servo shield**, a relay, and a **Jetson Nano** doing machine-vision target tracking / follow-me. Skip unless you want a project extension later.
