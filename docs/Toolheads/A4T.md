---
title: A4T
nav_order: 4
parent: Toolheads
---

# A4T

<img src="../media/Toolheads/A4t.png" width=200>

## References

* [A4T Reference Page](https://github.com/Armchair-Heavy-Industries/A4T)

## Backplate

* [A4T Backplate](https://github.com/sdylewski/StealthChanger/blob/main/STLs/Backplates/A4T.stl)
* [Backplate assembly instructions](https://github.com/sdylewski/StealthChanger/blob/main/Manual/Stealthchanger_Assembly_Guide.pdf)
* Requires supports. [Printing instructions](../Building/Printing.md)

### Backplate BOM for All Tools

- [4] m3 heat inserts
- [3] Ø4x12mm ssRod (dowel pin) with a rounded end **(if it has female threads on the back it will be listed as an M3, but make sure the pin is 4mm OD, that is what is important. We recommend the threaded pins for better fixing)**
- [1] 6x3mm magnet (N52 Highly recommended to counter the pull from the umbilicals. Note that most magnets not from a reputable source may say N52 but aren't actually. Recommend one of the two links below.)
- [2] m3x6 or m3x8 FHCS (Flat head countersunk screw, MUST BE MAGNETIC. no stainless, as per TAP)
- [1] [OptoTap](https://s.click.aliexpress.com/e/_DEGsGTV) (only the sensor PCB is required)
- [1] m3x8 BHCS (optional in v1.1)

### BOM Additions for A4T

- [2] m3x45 SHCS **magnetic** (backplate lower mounting screws)
- [2] m3x12 BHCS / SHCS (backplate upper mounting screws)
- [2] m3x6 FHCS **magnetic** (preload screws. m3x8 will be too long)

## PCB Mounting Options

### Official PCB Mounts (Draftshift Design)

*No official PCB mounts from Draftshift Design. See User Mods below.*

### User Mods

* TBD

## Toolhead Options & Mods

* [Fan splitter](https://github.com/DraftShift/StealthChanger/tree/main/UserMods/dudewithan02/A4T-Xol-Fan-Splitter-PCB)

## StealthChanger Requirements

* Requires Shorter Z joints like <a href="https://github.com/VoronDesign/VoronUsers/tree/main/printer_mods/hartk1213/Voron2.4_GE5C">Ge5C z-joints</a> so you don't bottom out your carriage when homing.
* Requires new smaller front idlers like the <a href="https://github.com/clee/VoronBFI">BFI</a> or <a href="https://github.com/DraftShift/StealthChanger/tree/main/UserMods/BT123/MiniBFI%20%2B%20MicroBFI">Mini BFI</a> so it doesn't collide with the idlers.

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
| Standard Dock | Full-height dock with back and base | [A4T Back and Base](https://github.com/DraftShift/ModularDock/tree/main/STLs/A4T) |

**Note:** A4T is not compatible with stubby docks due to toolhead geometry.

### User Mods

| Dock Type | Size | Description | Link |
|-----------|------|-------------|------|
| Tall Magnetic Dock | 76mm | Tall magnetic dock variant | [A4T tall magnetic dock](https://www.printables.com/model/1381192-stealthchanger-a4t-magnetic-dock) |
| Short Magnetic Docks | 76mm | Shorter magnetic dock variant | [A4T short magnetic docks](https://github.com/DraftShift/ModularDock/blob/main/UserMods/dudewithan02/A4T-Magnet-Docks/README.md) |

**Additional Resources:**
* Common components for [your dock type](../Docks.md)
* [Modular dock assembly guide](https://github.com/DraftShift/ModularDock/blob/main/Manual/ModularDock_Assembly_Guide.pdf)



## Tips & Suggestions
* TBD

