---
title: EBB36 T0 Configuration Example
parent: Examples
---

# EBB36 T0 Configuration Example

This is an example tool configuration file for T0 (Tool 0) using an **EBB36** toolhead board, compatible with klipper-toolchanger-easy.

**Important:** This is an example only. Copy the configuration below to `stealthchanger/tools/T0.cfg` and customize with your values. DO NOT use this file directly.

```ini
# Example tool configuration file for T0 (Tool 0) - EBB36 Board
# This example is compatible with klipper-toolchanger-easy
# Copy this to stealthchanger/tools/T0.cfg and customize with your values
# DO NOT use this file directly - it's an example only

# MCU Configuration (CAN bus example - adjust for your setup)
[mcu EBBT0]
canbus_uuid: xxxxxxxxxxx  # Replace with your CAN bus UUID

# Extruder Configuration
# NOTE: T0 uses "extruder" (no number), T1+ use "extruder1", "extruder2", etc.
[extruder]
step_pin: EBBT0:PD0
dir_pin: !EBBT0:PD1
enable_pin: !EBBT0:PD2
microsteps: 16
full_steps_per_rotation: 200
rotation_distance: 22.67895  # Calibrate this value for your extruder
gear_ratio: 50:10  # Adjust for your extruder (BMG is 50:17, Galileo is 7.5:1)
nozzle_diameter: 0.400
filament_diameter: 1.750
heater_pin: EBBT0:PB13
sensor_type: MAX31865  # Adjust for your thermistor type
spi_bus: spi1
rtd_nominal_r: 100
rtd_reference_r: 430
rtd_num_of_wires: 2
sensor_pin: EBBT0:PA4
max_power: 1.0
min_temp: -235
max_temp: 500
min_extrude_temp: 150
pressure_advance: 0.04  # Calibrate this value
pressure_advance_smooth_time: 0.040
control: pid
pid_kp: 26.213  # Run PID_CALIBRATE to get correct values
pid_ki: 1.304
pid_kd: 131.721
max_extrude_only_distance: 1400.0
max_extrude_only_velocity: 75.0
max_extrude_only_accel: 1500

# TMC Driver Configuration
[tmc2209 extruder]
uart_pin: EBBT0:PA15
interpolate: False
run_current: 0.6
sense_resistor: 0.110
stealthchop_threshold: 0

# Part Cooling Fan
# NOTE: klipper-toolchanger-easy uses fan_generic, not multi_fan
[fan_generic T0_partfan]
pin: EBBT0:PA1
max_power: 1.0
kick_start_time: 0.5
off_below: 0.0

# Hotend Fan
[heater_fan T0_hotend_fan]
pin: EBBT0:PA0
heater: extruder
heater_temp: 50.0
kick_start_time: 0.5

# Tool Selection Macro (optional - klipper-toolchanger-easy handles this)
[gcode_macro T0]
variable_color: ""
gcode:
    SELECT_TOOL T=0

# Tool Configuration
[tool T0]
tool_number: 0
extruder: extruder  # T0 uses "extruder" (no number)
fan: T0_partfan  # klipper-toolchanger-easy format (not multi_fan)
gcode_x_offset: 0  # Calibrate these offsets
gcode_y_offset: 0
gcode_z_offset: 0
t_command_restore_axis: z  # Restore Z axis after tool change

# Docking path - klipper-toolchanger-easy uses separate dropoff/pickup paths
params_dropoff_path: [
    {'y':9.5, 'z':4}, 
    {'y':9.5, 'z':2}, 
    {'y':5.5, 'z':0}, 
    {'z':0, 'y':0, 'f':0.5}, 
    {'z':-10, 'y':0}, 
    {'z':-10, 'y':16}
]

params_pickup_path: [
    {'z':-10, 'y':16}, 
    {'z':-10, 'y':0}, 
    {'z':0, 'y':0, 'f':0.5, 'verify':1}, 
    {'y':5.5, 'z':0}, 
    {'y':9.5, 'z':2}, 
    {'y':9.5, 'z':4}
]

# Dock park positions (calibrate these for your specific dock)
params_park_x: 28.0
params_park_y: -71.0
params_park_z: 381.0

# Input Shaper (calibrate per tool)
params_input_shaper_type_x: 'mzv'
params_input_shaper_freq_x: 62.4
params_input_shaper_damping_ratio_x: 0.01
params_input_shaper_type_y: 'mzv'
params_input_shaper_freq_y: 88.6
params_input_shaper_damping_ratio_y: 0.01

# Tool Probe (OctoTap) Configuration
[tool_probe T0]
pin: ^EBBT0:PB6  # OctoTap sensor pin
tool: 0
x_offset: 0  # Nozzle is the probe, so X/Y offsets are 0
y_offset: 0
z_offset: 0  # Calibrate this value (will be negative)
speed: 5.0
samples: 3
samples_result: median
sample_retract_dist: 2.0
samples_tolerance: 0.02
samples_tolerance_retries: 3
activate_gcode:
    _TAP_PROBE_ACTIVATE HEATER=extruder

# ADXL345 Accelerometer (optional, for input shaper)
[adxl345 T0]
cs_pin: EBBT0:PB12
spi_software_sclk_pin: EBBT0:PB10
spi_software_mosi_pin: EBBT0:PB11
spi_software_miso_pin: EBBT0:PB2
axes_map: x,z,y
```



