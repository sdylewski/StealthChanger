---
title: Stealthburner
nav_order: 3
parent: Toolheads
---
<!-- Use the page layout at TOC.md:  https://github.com/sdylewski/StealthChanger/blob/main/docs/TOC.md -->
# Stealthburner

<img src="../media/Toolheads/Stealthburner.png" width=200>

## References

* [Stealthburner reference page](https://github.com/VoronDesign/Voron-Stealthburner)

## Backplate

**Note:** heatsets on the SB version in from the opposite side as the Voron instructions, slightly longer screws on the CW2 attachment may be required

* [Stealthburner Backplate](https://github.com/sdylewski/StealthChanger/blob/main/STLs/Backplates/StealthBurner.stl)
* OR - [Stealthburner magnetic backplate](https://www.printables.com/model/1346474-stealthchanger-stealthburner-backplate-v11-magnet/files)
* OR - [Stealthburner screw-in pins backplate](https://www.printables.com/model/1358108-stealtchanger-stealthburner-backplate-with-screwed)
* OR - [Stealthburner screw-in pins and magnetic backplate](https://www.printables.com/model/1384948-stealthchanger-stealthburner-backplate-v11-magnet)
* [Backplate assembly instructions](https://github.com/sdylewski/StealthChanger/blob/main/Manual/Stealthchanger_Assembly_Guide.pdf)
* Requires supports. [Printing instructions](../Building/Printing.md)

Having magnets that keep the toolhead in place in the docks and nicely aligned with the shuttle is recommended for reliable dropoff/pickup. These go in the bottom front screw holes of the StealthBurner cowl.

### Backplate BOM for All Tools

- [4] m3 heat inserts
- [3] Ø4x12mm ssRod (dowel pin) with a rounded end **(if it has female threads on the back it will be listed as an M3, but make sure the pin is 4mm OD, that is what is important. We recommend the threaded pins for better fixing)**
- [1] 6x3mm magnet (N52 Highly recommended to counter the pull from the umbilicals. Note that most magnets not from a reputable source may say N52 but aren't actually. Recommend one of the two links below.)
- [2] m3x6 or m3x8 FHCS (Flat head countersunk screw, MUST BE MAGNETIC. no stainless, as per TAP)
- [1] [OptoTap](https://s.click.aliexpress.com/e/_DEGsGTV) (only the sensor PCB is required)
- [1] m3x8 BHCS (optional in v1.1)

### BOM Additions for Stealthburner

- [Recommended for DraftShift Dock] Several 5x2 or 5x3 N52 magnets to hold toolhead to dock

## PCB Mounting Options

### Official PCB Mounts (Draftshift Design)

*StealthBurner uses integrated PCB mounting in the toolhead design. SB2040 and SB2209 boards mount directly to the toolhead. For EBB36, see User Mods below.*



### User Mods

| PCB | Extruder | Description | Link |
|-----|----------|-------------|------|
| EBB36 | CW2 | EBB36 v1.2 CW2 mount with PUG, includes PCB cover | [EBB36 v1.2 CW2 mount with PUG](https://www.printables.com/model/1126741-ebb36-v12-cw2-mount-with-pug) |

## Toolhead Options & Mods

* [Stealthburner numbered cowls](https://github.com/sdylewski/StealthChanger/tree/main/UserMods/Dumplap/Stealthburner%20Number%20Cowls)
* [SC Barf LED for Stealthburner](https://github.com/sdylewski/StealthChanger/tree/main/UserMods/N3MI-DG/StealthBurner_SC_Barf) (note that SC Barf needs RGB nozzle LEDs, not RGBW for SW compatability)
* [FilamAtrix](https://github.com/thunderkeys/FilamATrix) for runout sensor, cutter and ECAS fitting

## Umbilical

If using the recommended N3MI umbilical plate, you probably want to terminate your umbilical at the toolhead using some sort of cable anchor at the toolhead:

* [Stealthburner cable bridge mod](https://www.printables.com/model/608471-stealthburner-cable-bridge-mod/files) (EBB SB2240/2209)
* [Stealthburner Umbilical SB2040 CAN Bus cable anchor](https://www.printables.com/model/407336-voron-stealthburner-umbilical-sb2040-can-bus-cable/files)
* [EBB36 v1.2 CW2 mount with PUG](https://www.printables.com/model/1126741-ebb36-v12-cw2-mount-with-pug) mount with PUG instead of PG7, also has PCB cover that uses existing mounting holes of the CW2
  
## Dock

| Dock Width | Stubby Dock Compatible |
|------------|------------------------|
| 76mm | ❌ No |

### Maximum Toolheads per Printer Size

| Printer Size | Max Toolheads |
|--------------|---------------|
| Voron 250 | 4 |
| Voron 300 | 4 |
| Voron 350 | 5 |

*Calculation assumes 5mm spacing between docks. See [Docks](../Docks.md) for details.*

### Official Dock Types (Draftshift Design)

| Dock Type | Description | Link |
|-----------|-------------|------|
| Standard Dock | Full-height dock with back and base | [Stealthburner Back and Base](https://github.com/DraftShift/ModularDock/tree/main/STLs/Stealthburner) |

**Note:** Stealthburner is not compatible with stubby docks due to toolhead geometry.

### User Mods

| Dock Type | Size | Description | Link |
|-----------|------|-------------|------|
| Magnetic Dock | 76mm | Magnetic backplate-compatible dock | [Stealthburner magnetic dock](https://www.printables.com/model/1346474-stealthchanger-stealthburner-backplate-v11-magnet/files) |
| Happy Crab Docks | 76mm | Minimal docks (no verticals, crossbar attachment only) | [Happy Crab docks](https://www.printables.com/model/994635-stealthchanger-stealthburner-minimal-docks-aka-hap) |
| One Piece Docks | 76mm | Single-piece dock design (includes UHF variant) | [One piece docks](https://www.printables.com/model/1063297-one-piece-docks-remix-of-crabby-docks-with-uhf-var) |
| Screw Docks | 76mm | Screw-based docks for hooking toolheads | [Screw docks](https://www.printables.com/model/911717-stealthchanger-screw-docks) |

**Additional Resources:**
* Common components for [your dock type](../Docks.md)
* [Modular dock assembly guide](https://github.com/DraftShift/ModularDock/blob/main/Manual/ModularDock_Assembly_Guide.pdf)


## Tips & Suggestions
* Add multiple magnets so the dock screws don't get caught on the cowl.  




