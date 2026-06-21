# openDog V3 — Build Log

Running progress + pick-up point. **When you sit back down, start here**, then ask "what's next?"

New doc reading order for the project: `PROJECT.md` → `PROVE-OUT.md` → `ASSEMBLY.md`, with `BOM.md` as the parts reference and this log as the status tracker.

---

## 👉 NEXT ACTION
**Split the CAD into printable parts for one drive + one leg.**

The repo ships **STEP files, not print-ready STLs**, and the main one (`CAD/openDog V3.zip` → `openDog V3.stp`) is the *whole assembly merged together*. So before anything prints:

1. Some parts are **already standalone** — use these directly:
   - `CAD/openDog V3_internals_toleranced.stp` — cycloidal drive internals (use the toleranced version)
   - `CAD/jig.stp` — jig for cutting the 4 mm steel pins
   - `CAD/openDogV3_jig_knee.stp` — knee assembly jig
   - `CAD/bone.stp` — leg bone
2. **Extract from the full assembly** (`openDog V3.stp`): the drive casings/housings, leg bodies, pulleys, idlers — isolate the individual components.
3. Import into a **STEP-capable slicer** (PrusaSlicer / OrcaSlicer / Bambu Studio all read STEP), or convert to STL via FreeCAD/Fusion first.
4. Lay out **one cycloidal drive's worth + one leg's worth** of parts only (not all 12 / all 4 yet).

**Print settings:** PLA. Large parts ~15% infill / 3 perimeters / 0.3 mm layers. **Cycloidal internals: 4 perimeters / 30–40% infill.**

Goal of this step: get a single drive printed so we can assemble + verify tolerances (Stage 1 of `ASSEMBLY.md`) before committing to mass-printing.

---

## Where we are
**Phase:** pre-build / prove-out prep. Nothing assembled yet. Waiting on parts + first prints.

**The plan** is the 3-slice prove-out in `PROVE-OUT.md`: Slice A (electrical bring-up, 1 axis) → Slice B (2-joint test leg) → Slice C (full 3-axis leg running the firmware) → then commit to the full dog.

**Sequence during the shipping wait:**
1. Prep PC toolchain (Arduino IDE + Teensyduino, libraries, `odrivetool`) — *not done, deferred by choice*
2. Split STEP → printable parts ← **next action**
3. Print one drive's parts
4. Assemble that drive by hand when bearings/rod/hardware arrive → verify it spins smoothly (first prove-out win, no motor needed)
5. Slice A electrical bring-up once motor + ODESC arrive

## Ordered / on the way
- **Amazon (placed ~Jun 21):** EaglePower 8308 **KV90** motor (ETA Jul 8) · AS5047P encoder · 6.35 mm diametric magnet (20pc) · USB-C→micro-USB *data* cable · ELEGOO jumper kit · 50 W 2 Ω brake resistor · bearings for one drive (604ZZ, 6804ZZ ×5, 6813ZZ ×2) · drive mechanical hardware (4 mm steel rod, M4 nylon washers, 5 mm spacers, M4×40 cap bolt + nyloc, 2.9 mm case screws)
- **Sequre:** 2× ODESC 3.6 dual-drive (~2–3 week shipping) — 4 axes, covers Slices A–C
- **Have on hand:** bench PSU (~24 V, current-limited), 3D printer

## Decisions made
- **Driver: ODESC 3.6 dual-drive** (drop-in for obsolete ODrive v3.6, zero code rework). 2 now, 4 more for full build. Single-drive V4.2 / genuine ODrive S1 / SimpleFOC all evaluated and set aside — full reasoning in `BOM.md` driver decision log.
- **Strategy:** vertical-slice prove-out before committing the ~$2k of motors + drivers.
- **Docs live in `docs/`** (separate from upstream `README.md` to avoid merge conflicts).

## Done so far
- Audited the fork — fully in sync with James Bruton's upstream, nothing missing.
- Built `PROJECT.md`, `ASSEMBLY.md` (manual from the 6 build videos), `BOM.md` (2026 re-check), `PROVE-OUT.md`, transcripts in `docs/transcripts/`.
- Settled the motor-driver question; placed the Slice A + one-drive order.

---

## Dated entries
- **2026-06-21** — Project kickoff. Repo audited, docs created, driver decision settled (ODESC 3.6 dual-drive), Slice A + one-drive parts ordered. Next: split STEP files for printing.
