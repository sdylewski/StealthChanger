---
title: Additional Tuning
nav_order: 9
parent: Software & Configuration
---
<!-- Use the page layout at TOC.md:  https://github.com/sdylewski/StealthChanger/blob/main/docs/TOC.md -->

# Additional Tuning

Use this after your first successful print to refine quality and reliability. Nothing here is strictly required, but each item can reduce artifacts and increase consistency.

## Hotend PID Tuning

Each tool's heater may need PID tuning to maintain stable temperatures. PID tuning should be done for each tool's extruder heater.

**Important:** The heater name differs between T0 and other tools:
- **T0** uses `extruder` (no number)
- **T1** uses `extruder1`

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
   
7. Run `FIRMWARE_RESTART` to apply the changes.



## Mesh and Z-offset touch-ups (first layer)
- Print a big single-layer square or a first-layer test.
- If some areas are squished or sparse in the far corners from the umbilical, it may be pulling enough to affect the first layer. Adjust the umbilical and try again.

## Extrusion calibration (rotation_distance / steps)
- Mark filament and command a 100 mm extrude at print temp.
- Measure actual movement. Update extruder `rotation_distance` (Klipper) using the ratio: new = old × (commanded / measured).
- Do a short flow cube to validate; fine-tune slicer flow if necessary.

## Pressure Advance

Pressure advance compensates for filament compression and improves print quality, especially at corners and layer changes. Each toolhead may require different pressure advance values depending on the extruder, hotend, and nozzle configuration.

### Calibrating Pressure Advance

For each toolhead:

1. **Select the toolhead** you want to calibrate:
   ```
   SELECT_TOOL T=0  # Replace with your tool number
   ```

2. **Print a pressure advance test** or use Klipper's tuning tower:
   ```
   TUNING_TOWER COMMAND=SET_PRESSURE_ADVANCE PARAMETER=ADVANCE START=0 FACTOR=0.005
   ```

3. **Pick the segment** with the most consistent walls and neat corners.

4. **Note the value** for that toolhead.

### Setting Pressure Advance for Multiple Toolheads

**Important:** `SAVE_CONFIG` won't work properly for different toolheads because each toolhead has its own extruder configuration. Instead, you should set pressure advance values in your slicer.

#### Method: Slicer Filament Profiles

Create separate filament profiles in your slicer for each toolhead, each with its own pressure advance value:

1. **In your slicer** (OrcaSlicer, PrusaSlicer, etc.), create separate filament profiles:
   - **Profile for T0**: Set pressure advance value for toolhead 0
   - **Profile for T1**: Set pressure advance value for toolhead 1
   - **Profile for T2**: Set pressure advance value for toolhead 2
   - (Continue for each toolhead)

2. **Set the pressure advance value** in each filament profile's custom G-code or filament settings:
   - **OrcaSlicer**: Filament Settings → Custom G-code → Start G-code → Add `SET_PRESSURE_ADVANCE ADVANCE=<value>`
   - **PrusaSlicer**: Printer Settings → Custom G-code → Filament G-code → Add `SET_PRESSURE_ADVANCE ADVANCE=<value>`

3. **Assign the correct filament profile** to each toolhead when slicing:
   - When setting up your print, assign the T0 filament profile to toolhead 0
   - Assign the T1 filament profile to toolhead 1
   - And so on for each toolhead

**Example filament profile setup:**
- **PLA_T0** (for toolhead 0): `SET_PRESSURE_ADVANCE ADVANCE=0.04`
- **PLA_T1** (for toolhead 1): `SET_PRESSURE_ADVANCE ADVANCE=0.05`
- **PETG_T0** (for toolhead 0): `SET_PRESSURE_ADVANCE ADVANCE=0.03`
- **PETG_T1** (for toolhead 1): `SET_PRESSURE_ADVANCE ADVANCE=0.04`

This approach ensures that each toolhead gets the correct pressure advance value when it's selected during printing, and the slicer will automatically insert the correct `SET_PRESSURE_ADVANCE` command for each toolhead.

**Note:** If you're using multitool ramming (wipe tower), make sure pressure advance values are set in your filament settings, as the ramming process may temporarily set pressure advance to 0 and needs to restore it afterward.

## Ooze reduction
Slicer settings to reduce ooze: 
Orcaslicer settings:

## Resonance Testing and Input Shaper Calibration

Input shaper compensates for printer vibrations to reduce ringing and improve print quality. For toolchangers, you should run this calibration for each toolhead, especially if they have different weights or mass distributions.

### Setup

#### 1. Configure Accelerometers in Toolhead Config Files

Name each accelerometer separately in your tool configuration files (e.g., `Toolhead_T0.cfg`, `Toolhead_T1.cfg`):

```ini
# In Toolhead_T0.cfg
[adxl345 ant0]
cs_pin: EBBT0:PB12
spi_software_sclk_pin: EBBT0:PB10
spi_software_mosi_pin: EBBT0:PB11
spi_software_miso_pin: EBBT0:PB2
axes_map: x,z,y

# In Toolhead_T1.cfg
[adxl345 ant1]
cs_pin: EBBT1:PB12
spi_software_sclk_pin: EBBT1:PB10
spi_software_mosi_pin: EBBT1:PB11
spi_software_miso_pin: EBBT1:PB2
axes_map: x,z,y
```

**Note:** Use unique names for each accelerometer (e.g., `ant0`, `ant1`, `ant2`, etc.). The accelerometer type (e.g., `adxl345`) must match your hardware.

#### 2. Add `[resonance_tester]` Section in Main `printer.cfg`

Add a **single** `[resonance_tester]` section in your main `printer.cfg` file (not in each toolhead config):

```ini
[resonance_tester]
accel_chip: adxl345 ant0  # Change to ant0, ant1, etc. for the tool you're testing
probe_points:
    175, 175, 20  # Center of bed, 20mm above
```

**Important:** There should only be **ONE** `[resonance_tester]` section in your entire configuration. Place it in your main `printer.cfg` file, not in the individual toolhead config files.

#### 3. Verify Accelerometer is Working

Before running tests, verify each accelerometer is working:

```
ACCELEROMETER_QUERY CHIP=ant0
```

Replace `ant0` with your accelerometer chip name (e.g., `ant1`, etc.). You should see accelerometer values (x, y, z) in the response.

### Testing Methods

#### Method 1: Standard Klipper (`TEST_RESONANCES` + `SHAPER_CALIBRATE`)

This is the built-in Klipper method for input shaper calibration.

**For each toolhead:**

1. **Update `accel_chip` in `[resonance_tester]`** to match the tool you're testing:
   ```ini
   [resonance_tester]
   accel_chip: adxl345 ant0  # Change to ant0, ant1, etc.
   ```

2. **Run `FIRMWARE_RESTART`** to apply the change.

3. **Run resonance test for X axis:**
   ```
   TEST_RESONANCES AXIS=X
   ```

4. **Run resonance test for Y axis:**
   ```
   TEST_RESONANCES AXIS=Y
   ```

5. **Calculate input shaper parameters:**
   ```
   SHAPER_CALIBRATE
   ```

6. **Review the recommended shaper and frequency** in the console output.

7. **Apply the recommended settings** to your tool's input shaper configuration:
   ```ini
   # In your tool config file (e.g., Toolhead_T0.cfg)
   [input_shaper]
   shaper_freq_x: <recommended_frequency>
   shaper_type_x: <recommended_shaper>  # e.g., mzv, ei, 2hump_ei
   shaper_freq_y: <recommended_frequency>
   shaper_type_y: <recommended_shaper>
   ```

8. **Run `FIRMWARE_RESTART`** to apply the changes.

9. **Repeat steps 1-8 for each toolhead** (update `accel_chip` and restart for each tool).

#### Method 2: Klippain-ShakeTune

[Klippain-ShakeTune](https://github.com/Frix-x/klippain-shaketune) is an advanced input shaper calibration tool that provides detailed graphs and analysis. It's particularly useful for toolchangers as it can switch between accelerometers without editing config files.

**Option A: Using `ACCEL_CHIP` parameter (Recommended for toolchangers)**

This method allows you to test different toolheads without editing config files or restarting:

1. **Run the calibration command with the accelerometer chip specified:**
   ```
   axes_shaper_calibration ACCEL_CHIP="'adxl345 ant0'"
   ```
   
   **Examples:**
   - If your accelerometer chip name has a space (e.g., `adxl345 ant0`):
     ```
     axes_shaper_calibration ACCEL_CHIP="'adxl345 ant0'"
     ```
   - Alternative example with shorter name:
     ```
     axes_shaper_calibration ACCEL_CHIP="'adxl ant0'"
     ```
   
   **Note the quoting method:** When your accelerometer chip name contains a space, use double quotes around single quotes: `ACCEL_CHIP="'chip_name with space'"`. Replace the chip name with your actual accelerometer chip name (e.g., `'adxl345 ant1'`, `'adxl ant1'`, etc.).

2. **Review the results** in the ShakeTune web interface.

3. **Apply the recommended settings** to your tool's input shaper configuration (same as Method 1, step 7).

4. **Repeat for each toolhead** by changing the `ACCEL_CHIP` parameter in the command.

**Option B: Using config-based method**

If you prefer the config-based approach (similar to standard Klipper):

1. **Update `accel_chip` in `[resonance_tester]`** to match the tool you're testing.

2. **Run `FIRMWARE_RESTART`** to apply the change.

3. **Run the ShakeTune calibration:**
   ```
   axes_shaper_calibration
   ```

4. **Review the results** and apply settings.

5. **Repeat for each toolhead** (update `accel_chip` and restart for each tool).

**Note:** The `axes_shaper_calibration` command with `ACCEL_CHIP` parameter is specific to Klippain-ShakeTune and is the recommended method for toolchangers as it avoids the need to edit config files and restart between tool calibrations.


---

## FAQ (post-first-print fine-tuning)
- **Temperatures overshoot or oscillate**
  - Re-run hotend/bed PID at your common temps and `SAVE_CONFIG`.
- **First layer shifts near certain corners**
  - Likely cable/umbilical tug. Add slack, reclock strain relief, and re-test per the umbilical section.
- **Ringing/ghosting on walls**
  - Lower accel/junction limits or re-run input shaper calibration. Check shuttle to backplate spacing.
- **Stringing during tool changes**
  - Install nozzle blocker. Adjust retraction. Set slicer to reduce temperature when toolhead not printing.

