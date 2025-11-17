---
title: PRINT_START Macro Example (Advanced)
parent: Examples
---

# PRINT_START Macro Example (Advanced)

This is an advanced `PRINT_START` macro for StealthChanger using klipper-toolchanger-easy. This version includes smart QGL selection, tool detection, and other optimizations.

**Important:** klipper-toolchanger-easy provides a `PRINT_START` macro by default. This advanced example shows enhanced features beyond the basic implementation. For a simpler version, see the [Minimal PRINT_START Example](PRINT_START_Simple.md).

**Features:**
- Smart QGL tool selection (uses single tool for QGL if only one tool is used)
- Automatic tool detection based on slicer parameters
- Optimized startup sequence

## Common StealthChanger Variables

The `PRINT_START` macro uses several variables that slicers pass to indicate which tools will be used and their temperatures:

### Tool Selection Variables

- **`TOOL`** - The initial tool number to use for the print (e.g., `0`, `1`, `2`). This is the tool that will be selected first and used to start printing.
- **`TOOL_TEMP`** - The target temperature for the initial tool specified by `TOOL`.

### Multi-Tool Temperature Variables

For prints using multiple tools, slicers pass additional temperature parameters:

- **`T0_TEMP`** - Pre-heat temperature for tool 0 (if it will be used in the print)
- **`T1_TEMP`** - Pre-heat temperature for tool 1 (if it will be used in the print)
- **`T2_TEMP`** - Pre-heat temperature for tool 2 (if it will be used in the print)
- **`T3_TEMP`, `T4_TEMP`, `T5_TEMP`, etc.** - Additional tool temperatures as needed

**Note:** These `T{n}_TEMP` variables are only passed if the slicer detects that multiple tools will be used in the print. The macro uses these to:
1. Determine how many tools are being used (for smart QGL selection)
2. Pre-heat tools in the background while the initial tool is heating

### Bed Temperature Variable

- **`BED_TEMP`** - The target bed temperature for the print.

### How Slicers Pass Variables

Slicers automatically detect which tools/extruders are used in a print and generate the appropriate `T{n}_TEMP` parameters. For example:

- **Single tool print**: Only `TOOL`, `TOOL_TEMP`, and `BED_TEMP` are passed
- **Multi-tool print**: `TOOL`, `TOOL_TEMP`, `BED_TEMP`, plus `T0_TEMP`, `T1_TEMP`, etc. for each tool that will be used

The macro detects which tools are used by checking for the presence of `T{n}_TEMP` parameters, allowing it to optimize behavior (e.g., using the single tool for QGL instead of switching to T0).

{% raw %}
```ini
[gcode_macro PRINT_START]
description: "Initialize toolchanger, home, QGL, and prepare for printing"
gcode:
    # Initialize toolchanger
    INITIALIZE_TOOLCHANGER
    
    # Stop tool probe crash detection during startup
    STOP_TOOL_PROBE_CRASH_DETECTION
    
    # Determine which tools will be used in this print
    {% set initial_tool = params.TOOL|default(0)|int %}
    {% set tool_count = 1 %}
    {% for tool_nr in printer.toolchanger.tool_numbers %}
        {% set tooltemp_param = 'T' ~ tool_nr|string ~ '_TEMP' %}
        {% if tooltemp_param in params %}
            {% if tool_nr != initial_tool %}
                {% set tool_count = tool_count + 1 %}
            {% endif %}
        {% endif %}
    {% endfor %}
    
    # Set bed temperature
    M140 S{params.BED_TEMP|default(60)|float}
    
    # Home all axes
    G28
    
    # Determine if we should use T0 or the single tool for QGL
    {% if tool_count == 1 %}
        # Only one tool is being used - use it for QGL instead of switching to T0
        T{initial_tool}
    {% else %}
        # Multiple tools - always use T0 for QGL
        T0
    {% endif %}
    
    # Heat nozzle to 150C for QGL (softens any leftover filament)
    M109 S150
    
    # Run Quad Gantry Level
    QUAD_GANTRY_LEVEL
    
    # Re-home Z after QGL
    G28 Z
    
    # Stop heating (actual tool will be selected and heated next)
    M109 S0
    
    # Optional but recommended: Clean nozzle (uncomment if you have a _CLEAN_NOZZLE macro)
    # _CLEAN_NOZZLE
    
    # Optional but recommended: Calibrate bed mesh (uncomment if needed)
    # BED_MESH_CALIBRATE
    
    # Pre-heat tools that will be used in the print
    {% for tool_nr in printer.toolchanger.tool_numbers %}
        {% set tooltemp_param = 'T' ~ tool_nr|string ~ '_TEMP' %}
        {% if tooltemp_param in params %}
            M104 T{tool_nr} S{params[tooltemp_param]|float}
        {% endif %}
    {% endfor %}
    
    # Select the initial tool for printing
    {% if params.TOOL is defined %}
        T{params.TOOL|int}
    {% else %}
        T0  # Default to T0 if not specified
    {% endif %}
    
    # Heat the initial tool to printing temperature
    M109 S{params.TOOL_TEMP|default(220)|float}
    
    # Restart tool probe crash detection
    START_TOOL_PROBE_CRASH_DETECTION
    
    # Optional but recommended: Prime the nozzle (uncomment if needed)
    # Adjust coordinates and extrusion amount to match your printer setup
    # G92 E0                    # Reset extruder
    # G1 Z0.2 F3000            # Move to prime height
    # G1 X10 Y10 E5 F1000      # Prime line (adjust X, Y, and E values as needed)
    # G92 E0                    # Reset extruder again
```
{% endraw %}

## Parameters

**Required:**
- `TOOL` - The initial tool number to use (e.g., `0`, `1`, `2`)
- `TOOL_TEMP` - The temperature for the initial tool
- `BED_TEMP` - The bed temperature

**Optional (for multi-tool prints):**
- `T0_TEMP`, `T1_TEMP`, `T2_TEMP`, etc. - Pre-heat temperatures for tools that will be used in the print

## Example Usage

**Single tool print:**
```gcode
PRINT_START TOOL=0 TOOL_TEMP=220 BED_TEMP=60
```

**Multi-tool print:**
```gcode
PRINT_START TOOL=0 TOOL_TEMP=220 BED_TEMP=60 T0_TEMP=220 T1_TEMP=230 T2_TEMP=250
```

## What This Macro Does

1. Initializes the toolchanger (`INITIALIZE_TOOLCHANGER`)
2. Stops tool probe crash detection
3. Detects which tools will be used in the print (based on `TOOL` parameter and `T0_TEMP`, `T1_TEMP`, etc.)
4. Sets and waits for bed temperature (`M140` / `M190`)
5. Homes all axes (`G28`)
6. **Smart QGL tool selection:**
   - If only one tool is being used, uses that tool for QGL (no unnecessary tool change)
   - If multiple tools are being used, uses T0 for QGL (standard behavior)
7. Heats the QGL tool to 150°C for QGL (softens any leftover filament)
8. Runs Quad Gantry Level (`QUAD_GANTRY_LEVEL`)
9. Re-homes Z after QGL
10. Optional: Cleans nozzle (if `_CLEAN_NOZZLE` macro is uncommented)
11. Optional: Calibrates bed mesh (if `BED_MESH_CALIBRATE` is uncommented)
12. Pre-heats other tools if `T0_TEMP`, `T1_TEMP`, etc. are provided
13. Selects the specified `TOOL` and heats it to `TOOL_TEMP`
14. Restarts tool probe crash detection
15. Optional: Primes the nozzle (if priming code is uncommented)

## Customization

If you need to customize the `PRINT_START` macro:

1. **Do not override it** - Instead, modify the macro in `stealthchanger/macros.cfg`
2. **Use hooks** - klipper-toolchanger-easy may provide hooks for customization
3. **Common additions:**
   - Nozzle cleaning (`_CLEAN_NOZZLE`)
   - Bed mesh calibration (`BED_MESH_CALIBRATE`)
   - Prime lines or purge sequences
   - LED status indicators

**Note:** For multi-tool prints, T0 is used for QGL and Z homing to ensure accurate tool alignment. For single-tool prints, the macro uses the single tool for QGL to avoid unnecessary tool changes.


