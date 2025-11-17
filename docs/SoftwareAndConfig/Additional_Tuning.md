---
title: Additional Tuning
nav_order: 9
parent: Software & Configuration
---
<!-- Use the page layout at TOC.md:  https://github.com/sdylewski/StealthChanger/blob/main/docs/TOC.md -->

# Additional Tuning

Use this after your first successful print to refine quality and reliability. Nothing here is strictly required, but each item can reduce artifacts and increase consistency.

## Hotend PID tuning
  - `PID_CALIBRATE HEATER=extruder TARGET=220` then `SAVE_CONFIG`.
  - (add notes on how to do this for all extruders)

## Mesh and Z-offset touch-ups (first layer)
- Print a big single-layer square or a first-layer test.
- If some areas are squished or sparse in the far corners from the umbilical, it may be pulling enough to affect the first layer. Adjust the umbilical and try again.

## Extrusion calibration (rotation_distance / steps)
- Mark filament and command a 100 mm extrude at print temp.
- Measure actual movement. Update extruder `rotation_distance` (Klipper) using the ratio: new = old × (commanded / measured).
- Do a short flow cube to validate; fine-tune slicer flow if necessary.

## Pressure advance
- Print a PA test or use Klipper's tower: `TUNING_TOWER COMMAND=SET_PRESSURE_ADVANCE PARAMETER=ADVANCE START=0 FACTOR=0.005`.
- Pick the segment with most consistent walls and neat corners. Set that value in your config and `SAVE_CONFIG`.
- Save this in your _SLICER_ (examples)

## Ooze reduction
Slicer settings to reduce ooze: 
Orcaslicer settings:

## Resonance/ringing and acceleration sanity check
- If you see ringing/ghosting on walls, reduce accel or re-run input shaper calibration.
- Klipper: `SHAPER_CALIBRATE` (if you have an accelerometer) and apply recommended values, then `SAVE_CONFIG`.


---

## FAQ (post-first-print fine-tuning)
- **Temperatures overshoot or oscillate**
  - Re-run hotend/bed PID at your common temps and `SAVE_CONFIG`.
- **First layer shifts near certain corners**
  - Likely cable/umbilical tug. Add slack, reclock strain relief, and re-test per the umbilical section.
- **Ringing/ghosting on walls**
  - Lower accel/junction limits or re-run input shaper calibration. Check shuttle to backplate spacing.
- **Stringing during tool changes**
  - Install nozzle blocker. Adjust retraction.

