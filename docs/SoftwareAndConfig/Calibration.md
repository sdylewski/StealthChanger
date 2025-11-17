---
title: Tool Calibration
nav_order: 3
parent: Software & Configuration
---
<!-- Use the page layout at TOC.md:  https://github.com/sdylewski/StealthChanger/blob/main/docs/TOC.md -->

# Tool Calibration

A multi-tool printer requires calibration. This page explains what to calibrate, and describes some of the useful tools and techniques.

## Calibration Overview

StealthChanger requires several types of calibration:

1. **[Probe Z Offset](#probe-z-offset-tool_probe-offsets)** - Sets where Z=0 is for each tool when homing
2. **[G-code Offsets](#g-code-offsets)** - Aligns nozzle positions between tools (X, Y, Z)
3. **[Tool Offset Calibration](#tool-offset-calibration)** - Measure and set X, Y, and Z offsets between tools
4. **[Dock Calibration](DockCalibration.md)** - Sets where each tool docks when not in use

<div style="background-color: #fff3cd; border-left: 4px solid #ffc107; padding: 12px; margin: 12px 0;">

**⚠️ WARNING - Dock Calibration:** When setting dock locations for T1, T2, or any non-T0 tool, you **MUST** home with T0 first, then **manually** switch to the tool you're calibrating. **DO NOT** run `G28` or home with Tn (non-T0 tools) during dock calibration. Homing with Tn applies that tool's offsets, which will make your dock positions incorrect. Always home with T0, then manually place or pick up Tn using T0 before calibrating its dock position.

</div>

# Preparing to calibrate

Make sure you [set the preload
screws](../Shuttle.md#backplate-preload) on each backplate
before calibrating, since this affects the Z position of each [tool](../Toolheads/Toolheads.md). If you find
your Z calibration is drifting, it’s likely that one of the preload screws has
come loose.


**Before you start calibrating you should "break in" each tool probe.  Heat-soak
your machine and run a couple `PROBE_ACCURACY SAMPLES=100` per tool.**

## Probe Z Offset (`[tool_probe]` Offsets)

A Voron 2.4-style printer needs a bed probe in order to perform quad gantry leveling (QGL). The default on StealthChanger is to use the OptoTap board on each tool as the bed probe.

### How It Works

With this probing method, the tool will move downward until the nozzle contacts the bed. As the gantry continues to lower, the tool will slide upwards on the shuttle until the OptoTap board no longer registers the presence of the shuttle, at which point the probe is considered triggered. This is conceptually the same as [Voron Tap](https://github.com/VoronDesign/Voron-Tap), with the StealthChanger pins and bushings taking the place of the Tap linear rail.

### Understanding Probe Z Offset

- **Applied only when homing** with that specific tool
- **X and Y probe offsets are zero** - because the nozzle is the probe
- **Z offset** is specified by `z_offset` in `[tool_probe Tn]` (where Tn = T0, T1, etc.)
- **Always a negative number** - the trigger point is "below" the bed
- **Each tool has its own offset** - due to variation in assembly, preload screws, and OptoTap board

A more negative probe Z offset means less squish; a less negative (closer to zero) value means more squish, as the tool will not rise as far from the trigger point.

**Important:** If you always home Z with your primary tool (typically T0), then other tools' probe Z offsets do **not** affect print height. Only the primary tool's probe Z offset determines where Z=0 is set, and then `gcode_z_offset` values align the other tools' nozzles to match. Non-primary tools' probe Z offsets only need to be "close enough" for any initial Z homing that might occur before picking up the primary tool.

**Why do I need probe Z offset for Tn?** Even though non-primary tools' probe Z offsets don't affect print height, you still need to set them. This is because tools are often left on the printer at the end of a print. When you restart the printer, it needs to home with whatever tool is currently on the shuttle before it can safely switch to T0. With the tool's X, Y, and Z offsets configured, the printer can calculate where the dock locations are relative to T0's position, allowing it to properly park the current tool and pick up T0 for the final QGL and Z homing.

### Z_offset Calibration Procedure

1. Manually put a tool on the [shuttle](../Shuttle.md) and run `INITIALIZE_TOOLCHANGER`
2. Warm your nozzle to 150C or clean it well.
3. Run `G28` to home all axes
4. Run `QUAD_GANTRY_LEVEL` to level the gantry
5. Run `G28` again to re-home after QGL
6. Do a [Manual Paper Test](https://www.klipper3d.org/Bed_Level.html#the-paper-test) as normal (like a single toolhead printer) and adjust the Z
7. Copy the offset value and save it to `z_offset` in `[tool_probe Tn]` of the tool config file (`stealthchanger/tools/Tn.cfg`)
8. Repeat from step 1 for all tools
9. Run `FIRMWARE_RESTART` to apply changes

**Note:** It is typical to set up the print start macro such that T0 is always used for the final QGL and Z homing before printing. Then it is only necessary to fine-tune the probe Z offset on T0. Other tools need only be close enough for an initial Z homing before picking up T0.

**Tip:** Follow [Ellis's method](https://ellis3dp.com/Print-Tuning-Guide/articles/first_layer_squish.html) or the [paper test](https://www.klipper3d.org/Bed_Level.html#the-paper-test) to get the right amount of first-layer squish.

## G-code Offsets (T0 - Tn offsets)

G-code offsets align nozzle positions between tools so that parts of a model printed with different tools will line up correctly. This is necessary even with "identical" tools because of tolerances in parts and assembly.

### Understanding G-code Offsets

- **Applied when changing tools** during printing
- **Relative to your primary tool** (the one you always home with, typically T0)
- **Primary tool:** `gcode_*_offset = 0` (it's the reference)
- **Other tools:** Set to match the primary tool's position
- **Specified by:** `gcode_x_offset`, `gcode_y_offset`, and `gcode_z_offset` in `[tool Tn]`
- **Stable from print to print** - but may need adjustment if you rebuild a tool or have a crash

By convention, these offsets are set to 0 for T0. The offsets for other tools specify the distance that tool needs to move to place its nozzle in exactly the same position that T0's would be. This can be either positive or negative on each axis. 

**Note:** For Z offsets specifically, `gcode_z_offset` is independent of the tool's probe Z offset. If you always home Z with T0, then T0's probe Z offset sets Z=0, and T2's `gcode_z_offset` aligns T2's nozzle to match T0's position. T2's probe Z offset doesn't affect print height in this case - it's only used if T2 ever homes Z.

### Setup Process

**Single Tool:**
- Set `[tool_probe]` offsets - you're done
- Can home directly with that tool

**Multiple Tools:**
1. Choose a primary tool (typically T0)
2. Set primary tool's `[tool_probe]` offsets correctly
3. Set primary tool's `gcode_*_offset = 0`
4. Calibrate other tools' `gcode_*_offset` to match primary tool position
5. Always home with the primary tool



**Note:** You can setup your printer to home with Tn if you're only using that one tool during printing, but it requires a more advanced print_start macro. See the [Advanced PRINT_START macro example](Examples/PRINT_START_Advanced.md) for reference.


### G-code Z Offset Calibration Procedure

**IMPORTANT:** 
- Only home or probe with T0 during this calibration
- `gcode_z_offset` on Tool 0 is always 0 (T0 is the reference tool)
- Set probe Z offset for all tools first before doing G-code offsets

1. Set probe Z offset for all tools first
2. Make sure T0 is on the shuttle and run `INITIALIZE_TOOLCHANGER`
3. Run `G28` to home all axes
4. Run `QUAD_GANTRY_LEVEL` to level the gantry
5. Run `G28` again to re-home after QGL
6. Run `G1 Z10 F600` to move to a safe height
7. Manually remove the current tool and place the next tool (T1, T2, etc.) on the shuttle
8. Do a [Manual Paper Test](https://www.klipper3d.org/Bed_Level.html#the-paper-test) as normal and adjust the Z
9. Once done, run `M114` and copy the Z value to `gcode_z_offset` in `[tool Tn]` of the tool config file (`stealthchanger/tools/Tn.cfg`)
10. Repeat from step 6 for all remaining tools
11. Run `FIRMWARE_RESTART` to apply changes

### Calibration Methods

#### Manual Calibration (Test Prints)

The simplest but most tedious method is to print test objects like [this one](https://www.printables.com/model/201707-x-y-and-z-calibration-tool-for-idex-dual-extruder) or [Nozzle Alignment Assist](https://www.printables.com/model/109267-nozzle-alignment-assist). For X and Y you are looking at the [vernier scales](https://en.wikipedia.org/wiki/Vernier_scale); for Z you are looking to have the same amount of squish on both sides (remember, T0 squish is set by the probe Z offset, not G-code offset).

#### Mechanical Calibration Probes

A mechanical calibration probe is basically an endstop that can be triggered by the nozzle moving along any axis. By tapping the probe from ±X, ±Y and +Z directions, the position of the nozzle opening can be determined (assuming a clean nozzle that is bored concentric to its outer surfaces).

**Available Probes:**

- **[Sexball](https://github.com/DraftShift/StealthChanger/wiki/Bill-of-Materials#sexball-probe)** - Replaces the pin of hartk's Sexbolt endstop with a ball. LDO StealthChanger kits come with a Sexball probe and this is probably a good place to start for new users.
- **[Nudge](https://github.com/zruncho3d/nudge)** - Another popular calibration probe design
- **Multiple designs** in the [NozzleAlign](https://github.com/viesturz/NozzleAlign) repo

**Mounting:**

You can permanently mount any of these probes in the overtravel region at the front or rear of the bed. With Sexball it is easy to remove the ball and pin, so that tools do not collide during printing. Alternately, you can make the whole probe assembly removable, as long as you have some way to rigidly attach it on or near the bed for calibration.

**Configuration:**

Any of these probes is basically the same from the firmware perspective. Consult the [NozzleAlign](https://github.com/viesturz/NozzleAlign) docs for information on configuration and commands.

**Temperature:**

Due to thermal expansion, it is best to probe as close to printing conditions as possible, though you must also ensure that the nozzle stays clean and does not ooze. 150°C is a good default.

#### Optical Calibration (Camera Tools)

Another approach for XY offsets is to use a camera looking up at the nozzle.

**Hardware Options:**
- [CXC by Ember Prototypes](https://www.emberprototypes.com/products/cxc)
- [DIY version](https://www.printables.com/model/1099576-xy-nozzle-alignment-camera) using OV9726 camera module
- Any USB microscope, if you can mount it rigidly

**Software Options:**
- **[kTAMV](https://github.com/TypQxQ/kTAMV)** - Automatically locates the nozzle using machine vision
- **[IDEX Nozzle Calibration Tool](https://github.com/Life0fBrian/Brians-IDEX-Nozzle-Calibration-tool)** - Another camera-based calibration tool
- **[Axiscope](https://github.com/nic335/Axiscope)** - Provides a web interface for manually locating nozzles. This involves more manual effort than kTAMV, but is simpler and can be more reliable. Axiscope can also interact with a Sexbolt or other Z endstop to calculate Z offsets.

## Tool Offset Calibration

A calibration probe (Sexball, Nudge, Axiscope, etc.) automatically measures the position differences between tools and sets the `gcode_x_offset`, `gcode_y_offset`, and `gcode_z_offset` values in your tool configuration files. This provides more accurate and repeatable tool alignment than manual calibration methods.

To use a calibration probe, configure the `_CALIBRATION_SWITCH` section in `stealthchanger/toolchanger-config.cfg` with your probe's endstop pin. This enables automatic calibration routines.

Example:
```ini
[_CALIBRATION_SWITCH]
pin: ^ar21  # Your calibration probe endstop pin
```

For information on available calibration probes and how to use them, see the [Mechanical Calibration Probes](#mechanical-calibration-probes) section below.

## Other Per-Tool Calibration

Additional calibration that may be needed per tool:

### PID Tuning

Each tool's heater may need PID tuning to maintain stable temperatures. PID tuning should be done for each tool's extruder heater.

**Important:** The heater name differs between T0 and other tools:
- **T0** uses `extruder` (no number)
- **T1** uses `extruder1`
- **T2** uses `extruder2`
- And so on...

**Procedure:**

1. Select the tool you want to tune (e.g., `T0` or `SELECT_TOOL T=0`)
2. Heat the nozzle to your typical printing temperature (e.g., 220°C for PLA, 250°C for ABS)
3. Run the PID calibration command:
   
   **For T0:**
   ```
   PID_CALIBRATE HEATER=extruder TARGET=220
   ```
   
   **For T1:**
   ```
   PID_CALIBRATE HEATER=extruder1 TARGET=220
   ```
   
   **For T2:**
   ```
   PID_CALIBRATE HEATER=extruder2 TARGET=220
   ```
   
   Adjust `TARGET` to your desired temperature.
4. Wait for the calibration to complete
5. The console will display the PID values (Kp, Ki, Kd). Copy these values.
6. Manually add the PID values to your tool configuration file:
   
   **For T0** - Add to `stealthchanger/tools/T0.cfg` in the `[extruder]` section:
   ```ini
   [extruder]
   control: pid
   pid_Kp: <Kp_value>
   pid_Ki: <Ki_value>
   pid_Kd: <Kd_value>
   ```
   
   **For T1** - Add to `stealthchanger/tools/T1.cfg` in the `[extruder1]` section:
   ```ini
   [extruder1]
   control: pid
   pid_Kp: <Kp_value>
   pid_Ki: <Ki_value>
   pid_Kd: <Kd_value>
   ```
   
   **For T2+** - Add to `stealthchanger/tools/Tn.cfg` in the corresponding `[extrudern]` section.
7. Run `FIRMWARE_RESTART` to apply the changes.

**Note:** Each tool's PID values are independent and should be tuned separately for optimal temperature stability.

### E-steps Calibration

Extruder steps per mm calibration ensures accurate filament extrusion. This should be done for each tool's extruder.

**Procedure:**

1. Select the tool you want to calibrate
2. Heat the nozzle to your typical printing temperature
3. Mark the filament 120mm from the extruder entrance
4. Extrude 100mm of filament:
   ```
   G1 E100 F300
   ```
5. Measure the actual distance the filament moved
6. Calculate the new steps per mm:
   ```
   New steps = (Current steps × 100) / Actual distance moved
   ```
7. Update the `rotation_distance` in your extruder config for T0:
   ```
   [extruder] # for T0
   rotation_distance: <calculated_value>
   ```
   - For T1, update `[extruder1]`
   - For T2, update `[extruder2]`
8. Run `SAVE_CONFIG` to save the changes

**Note:** If using `rotation_distance`, the formula is: `New rotation_distance = (Current rotation_distance × Actual distance) / 100`

### Input Shaper Calibration

Input shaper compensates for printer vibrations to reduce ringing and improve print quality. This is typically done once if you have the same exact toolheads.  If you have different toolhead types, you should run this for each tool.

**Procedure:**

1. Install an accelerometer on your toolhead (e.g., ADXL345, MPU-9250, or LIS2DW compatible)
2. Configure the accelerometer in your `printer.cfg`:
   ```ini
   [adxl345]
   cs_pin: <your_pin>
   spi_speed: 5000000
   
   [resonance_tester]
   accel_chip: adxl345
   probe_points:
       100, 100, 20  # Center of bed, 20mm above
   ```
3. Run the resonance test for X axis:
   ```
   TEST_RESONANCES AXIS=X
   ```
4. Run the resonance test for Y axis:
   ```
   TEST_RESONANCES AXIS=Y
   ```
5. Calculate input shaper parameters:
   ```
   SHAPER_CALIBRATE
   ```
6. Review the recommended shaper and frequency in the console output
7. Apply the recommended settings or manually configure:
   ```ini
   [input_shaper]
   shaper_freq_x: <recommended_frequency>
   shaper_type_x: <recommended_shaper>  # e.g., mzv, ei, 2hump_ei
   shaper_freq_y: <recommended_frequency>
   shaper_type_y: <recommended_shaper>
   ```
8. Run `FIRMWARE_RESTART` to apply the changes



---

**Next:** [Dock Calibration](DockCalibration.md) → Set up dock positions for reliable tool changes

## FAQ

### Do I need to calibrate offsets on every print?
No. Offsets remain stable unless hardware changes occur (toolhead disassembly, backplate preload screws adjustment, nozzle swap, etc.). Check periodically for drift, especially before long multicolor prints.

### Can I use `SAVE_CONFIG` after `PROBE_CALIBRATE`?
No. `SAVE_CONFIG` saves z-offset at the bottom of printer.cfg, not in the tool's probe section. Z-offset is fetched from the active tool and applied in homing_override. You must manually add the probe Z offset values to your tool configuration files (e.g., `stealthchanger/tools/T0.cfg`).

### My pressure advance doesn't work
Make sure to put the PA values in your filament settings in the slicer. If you have multitool ramming enabled, it will set pressure advance to 0 when ramming on the wipe tower, and if it's not in the filament settings, it can't put it back to what it's supposed to be during the print.

### Input Shaper shows "peak too far right" - what does this mean?
This usually means the accelerometer data is not being read correctly, or the toolhead is too light/heavy for the test. Try:
- Ensuring the accelerometer is properly mounted and connected
- Running the test with a different toolhead if available
- Checking that the accelerometer configuration matches your toolhead board
- Verifying the accelerometer is working with `ACCELEROMETER_QUERY`

### Do I need to run Input Shaper calibration for each tool?
If your toolheads are significantly different in weight or mass distribution, you may need to run Input Shaper calibration for each tool. If all toolheads are identical, you can typically use the same input shaper settings for all tools.

### My PID tuning values keep changing
PID values should stabilize after a few tuning cycles. If they keep changing significantly, check:
- Heater wiring connections
- Thermistor connections
- Heater block thermal paste/contact
- Ambient temperature stability
- Power supply stability
