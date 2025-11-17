---
title: Slicers & Macros
nav_order: 6
parent: Software & Configuration
---
<!-- Use the page layout at TOC.md:  https://github.com/sdylewski/StealthChanger/blob/main/docs/TOC.md -->

# Slicers & Macros

After [installing](Installation.md), [configuring](Configuration.md), and [calibrating](ToolCalibration.md) klipper-toolchanger-easy, you need to set up your slicer to work with the toolchanger system.

## Print Start Macro

klipper-toolchanger-easy provides a `PRINT_START` macro that must be used. It initializes the toolchanger, performs homing and QGL, and handles tool selection and heating. Your slicer should call it with the following parameters:

**Required Parameters:**
- `TOOL` - The initial tool number to use (e.g., `0`, `1`, `2`)
- `TOOL_TEMP` - The temperature for the initial tool
- `BED_TEMP` - The bed temperature

**Optional Parameters (for multi-tool prints):**
- `T0_TEMP`, `T1_TEMP`, `T2_TEMP`, etc. - Pre-heat temperatures for tools that will be used in the print

**Example:**
```gcode
PRINT_START TOOL=0 TOOL_TEMP=220 BED_TEMP=60 T0_TEMP=220 T1_TEMP=230
```

**What the macro does:**
1. Initializes the toolchanger (`INITIALIZE_TOOLCHANGER`)
2. Stops tool probe crash detection
3. Heats the bed to `BED_TEMP`
4. Homes all axes (`G28`)
5. Selects T0
6. Runs Quad Gantry Level (`QUAD_GANTRY_LEVEL`)
7. Re-homes Z after QGL
8. Selects the specified `TOOL` and heats it to `TOOL_TEMP`
9. Pre-heats other tools if `T0_TEMP`, `T1_TEMP`, etc. are provided
10. Restarts tool probe crash detection

**Important:** Do not override the `PRINT_START` macro. If you need custom startup behavior, modify the macro in `stealthchanger/macros.cfg` or use the provided hooks.

**Note:** The `_PARK_ON_COOLING_PAD` macro in `PRINT_START` is not needed for StealthChanger and can be commented out or removed if present.

**Examples:** 
- [Minimal PRINT_START Example](Examples/PRINT_START_Simple.md) - Basic implementation
- [Advanced PRINT_START Example](Examples/PRINT_START_Advanced.md) - Enhanced features with smart QGL selection and tool detection

## Overview

1. [Slicer Software](#slicer-software) - Supported slicers for multi-tool printing
2. [Slicer GCODEs](#slicer-gcodes) - Required start G-code and tool change G-code for each slicer

## Slicer Software

| Name | Multitool | Notes |
|:------:|:------:|------|
| Cura | <span style="color: green;">✓</span> | As of 5.8, select new printer, select DraftShift Design, select the size from the voron list, allows up to 8 extruders |
| OrcaSlicer | <span style="color: green;">✓</span> | As of 2.2 |
| PrusaSlicer | <span style="color: green;">✓</span> | |
| Simplify3D | <span style="color: silver;">?</span> | |
| Slic3r | <span style="color: silver;">?</span> | |
| SuperSlicer | <span style="color: green;">✓</span> | See [2197](https://github.com/supermerill/SuperSlicer/issues/2197) |

## Slicer GCODEs

### Prusa

Printer -> Custom G-code -> **Start G-code** - all must be a single line
```
PRINT_START TOOL_TEMP={first_layer_temperature[initial_tool]} {if is_extruder_used[0]}T0_TEMP={first_layer_temperature[0]}{endif} {if is_extruder_used[1]}T1_TEMP={first_layer_temperature[1]}{endif} {if is_extruder_used[2]}T2_TEMP={first_layer_temperature[2]}{endif} {if is_extruder_used[3]}T3_TEMP={first_layer_temperature[3]}{endif} {if is_extruder_used[4]}T4_TEMP={first_layer_temperature[4]}{endif} {if is_extruder_used[5]}T5_TEMP={first_layer_temperature[5]}{endif}  BED_TEMP=[first_layer_bed_temperature] TOOL=[initial_tool]
```

Printer -> Custom G-code -> **Tool change G-code**
```
M104 S{temperature[next_extruder]} T[next_extruder] ; set new tool temperature so it can start heating while changing
```

**NOTE**: add this as a second line if you use prime tower
```
G1 X{wipe_tower_x} Y{wipe_tower_y} F{travel_speed*60} ; Move to wipe tower before tool change
```

### Orca

Printer settings -> Machine G-code -> **Machine start G-code** - all must be a single line
```
PRINT_START TOOL_TEMP={first_layer_temperature[initial_tool]} {if is_extruder_used[0]}T0_TEMP={first_layer_temperature[0]}{endif} {if is_extruder_used[1]}T1_TEMP={first_layer_temperature[1]}{endif} {if is_extruder_used[2]}T2_TEMP={first_layer_temperature[2]}{endif} {if is_extruder_used[3]}T3_TEMP={first_layer_temperature[3]}{endif} {if is_extruder_used[4]}T4_TEMP={first_layer_temperature[4]}{endif} {if is_extruder_used[5]}T5_TEMP={first_layer_temperature[5]}{endif}  BED_TEMP=[first_layer_bed_temperature] TOOL=[initial_tool]
```

Printer settings -> Machine G-code -> **Change Filament G-code**
```
;Leave blank
```

### Cura

Machine Settings -> Printer -> **Start G-code** - all must be a single line

**For single tool prints:**
```
PRINT_START TOOL_TEMP={material_print_temperature_layer_0} BED_TEMP={material_bed_temperature_layer_0} TOOL={initial_extruder_nr}
```

**For multi-tool prints (Cura 5.8+):**
```
PRINT_START TOOL_TEMP={material_print_temperature_layer_0} T{initial_extruder_nr}_TEMP={material_print_temperature_layer_0} {if material_print_temperature_layer_0[1]!=0}T1_TEMP={material_print_temperature_layer_0[1]}{endif} {if material_print_temperature_layer_0[2]!=0}T2_TEMP={material_print_temperature_layer_0[2]}{endif} {if material_print_temperature_layer_0[3]!=0}T3_TEMP={material_print_temperature_layer_0[3]}{endif} {if material_print_temperature_layer_0[4]!=0}T4_TEMP={material_print_temperature_layer_0[4]}{endif} {if material_print_temperature_layer_0[5]!=0}T5_TEMP={material_print_temperature_layer_0[5]}{endif} BED_TEMP={material_bed_temperature_layer_0} TOOL={initial_extruder_nr}
```

Machine Settings -> Printer ->  Tool n -> **Extruder Start G-code**
```
;Leave blank
```

Machine Settings -> Printer ->  Tool n -> **Pre tool change G-code** - 5.10+
```
M104 S{material_print_temperature} T{extruder_nr}
```

---

**Optional Next Steps:**
- [LEDs](LEDs.md) → Configure color LEDs and lighting effects

## FAQ

**Quick Links:**
- [PrusaSlicer wipe tower position error](#prusaslicer-wipe-tower-position-error-after-upgrading-to-290)
- [OrcaSlicer sets pressure advance to zero](#orcaslicer-sets-pressure-advance-to-zero-with-prime-tower)
- [My tool waits a long time to heat up](#my-tool-waits-a-long-time-to-heat-up-before-continuing)
- [How do I decrease the prime amount in OrcaSlicer?](#how-do-i-decrease-the-prime-amount-in-orcaslicer)
- [Can I use ooze prevention and pre-heating?](#can-i-use-ooze-prevention-and-pre-heating)

---

### PrusaSlicer wipe tower position error after upgrading to 2.9.0
If you use `G1 X{wipe_tower_x} Y{wipe_tower_y} F{travel_speed*60}` in your Tool change G-code, it won't work in PrusaSlicer 2.9.0. They dropped the option to choose your wipe tower position, and the above command will give an error. Either don't use the wipe tower positions, or stick with PrusaSlicer 2.8.1 until it's fixed.

### OrcaSlicer sets pressure advance to zero with prime tower
There is a bug in OrcaSlicer where it sets the pressure advance to zero when using a prime tower. See [OrcaSlicer issue #7594](https://github.com/SoftFever/OrcaSlicer/issues/7594). You can use a post-processing script to remove `SET_PRESSURE_ADVANCE ADVANCE=0` from the g-code, or ensure your pressure advance values are set in your filament settings.

### My tool waits a long time to heat up before continuing
This is likely Klipper waiting for the temperature of the hotend to settle to the target temperature. If your hotend is not well PID tuned, it can sometimes oscillate indefinitely around the target temperature. By default, this margin is quite small (only 0.5°C). This is usually not a problem for single toolhead printers, but with a toolchanger where it heats up the tool every tool call, this can introduce a lot of delay. Ensure your PID values are properly tuned for each tool.

### How do I decrease the prime amount in OrcaSlicer?
Decreasing the Prime volume and width should work, but decreasing width just makes it longer. Adjust the Prime volume setting in your slicer's multi-material settings.

### Can I use ooze prevention and pre-heating?
Yes! Ooze prevention is highly recommended for multi-tool printing. It reduces power draw by keeping inactive tools at a lower temperature, prevents filament from oozing, reduces heat creep, and prevents filament from cooking during long prints.

**OrcaSlicer:**
- Location: Printer Settings → Multi-material → Ooze prevention
- **Temperature variation**: Recommended 30-50°C below print temperature (e.g., if printing at 220°C, set idle temp to 170-190°C)
- **Pre-heat time**: Recommended 15-30 seconds before tool change
- This uses `M104` to pre-heat and `M109` to validate temperature before switching

**PrusaSlicer:**
- Location: Printer Settings → Multi-material → Ooze prevention
- Similar settings available as OrcaSlicer
- Newer versions include an option to turn off tools completely when no longer needed

**Benefits:**
- **Reduced power draw**: Only the active tool runs at full temperature, significantly reducing peak power requirements
- **Prevents oozing**: Lower idle temperature prevents filament from oozing out of inactive nozzles
- **Prevents heat creep**: Keeps filament from getting too hot in the heatbreak
- **Prevents filament degradation**: Avoids cooking filament during long prints

**Note:** Ensure your PID values are properly tuned for each tool, as the temperature will need to stabilize quickly when switching tools. If you experience long wait times during tool changes, you may need to adjust the temperature deadband in your `M109` macro or increase the pre-heat time.
