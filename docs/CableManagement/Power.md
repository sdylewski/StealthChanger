---
title: Power Supply
nav_order: 5
parent: Electronics & Cable Management
---
<!-- Use the page layout at TOC.md:  https://github.com/sdylewski/StealthChanger/blob/main/docs/TOC.md -->

# Power
More toolheads active at the same time means you will need more baseload power required. A 200W 24V PSU might not suffice if all of your toolheads are heating up at the same time, 

Calculate the maximum power by tallying up all your heater wattages, add some margin for the motors and other things that are powered by 24V and use that as a reference number to select your PSU.

But this is only if all the toolheads are on at the same time and at maximum power.  If you can turn each toolhead on and wait until it reaches the set temperature before moving to the next one, your maximum power requirements will be much less.

### Example
For 24V power, and Power = I * V

| Toolhead Power| Current | x 2 | x 4 | x 6 |
| --- | --- | --- | --- | --- |
| 70W | 70W/24V=2.9A |  6.8A | 11.6A | 17.4A |

## Power supplies

### Meanwell LRS Series (24V)

Common options for 24V power supplies. Make sure you have enough room in your electronics bay because they are not all the same size.

| Model | Power Rating | Dimensions (L×W×H) | Cooling | Notes |
|-------|--------------|-------------------|---------|-------|
| **LRS-200-24** | 200W (8.8A) | 215 × 115 × 30 mm | Fanless | Silent operation, suitable for single toolhead or low-power multi-tool setups |
| **LRS-350-24** | 350W (14.6A) | 215 × 115 × 30 mm | Built-in fan | **Loud fan noise** - fan activates when temperature rises. Same case size as 200W model |
| **LRS-450-24** | 450W (18.8A) | 225 × 124 × 35 mm | Built-in fan | **Loud fan noise** - fan activates when temperature rises. **Larger case** than 200/350W models - check fit in electronics bay |
| **LRS-600-24** | 600W (25A) | 225 × 124 × 35 mm | Built-in fan | **Loud fan noise** - fan activates when temperature rises. Highest power option. **Larger case** than 200/350W models - check fit in electronics bay |

**Note:** Models 350W and above use active cooling with built-in fans that can be annoyingly loud when they kick in. The fan activates based on temperature, so good electronics bay ventilation is recommended to minimize fan runtime. The 200W model is fanless and silent.

### Meanwell U-Bracket Series (24V)

| Model | Power Rating | Dimensions (L×W×H) | Cooling | Notes |
|-------|--------------|-------------------|---------|-------|
| **UHP-200-24** | 200W (8.4A) | 194 × 55 × 26 mm | Convection | Slim design |
| **UHP-350-24** | 350W (14.6A) | 220 × 62 × 31 mm | Convection | Slim design |
| **UHP-500-24** | 500W (20.9A) | 233 × 81 × 31 mm | Convection | Slim design |

**Note:** At higher power supply temperatures, UHP (and other) power supplies reduce their output capability (derated). It's important to keep the power supply cool with good electronics bay ventilation/fan to maintain full rated output.

### Multiple Power Supplies

Other options include using multiple lower-wattage PSUs combined in parallel. **Important:** If you use multiple PSUs, you must tie the GND (ground) of each one together to have a common reference point, otherwise communications issues will arise. 

## Limiting power draw

To reduce peak power requirements, you can prevent all toolheads from heating to full temperature simultaneously. The most effective method is using **ooze prevention** settings in your slicer, which keeps inactive tools at a lower idle temperature (typically 30-50°C below print temperature) and only heats them up shortly before they're needed.

**Recommended approach:**
- Use [ooze prevention settings](../SoftwareAndConfig/Slicers.md#can-i-use-ooze-prevention-and-pre-heating) in your slicer (available in OrcaSlicer and PrusaSlicer)


**Other methods:**
- Stagger tool heating in your slicer start G-code (heat tools sequentially rather than simultaneously)
- Use lower pre-heat temperatures in `PRINT_START` and let tools reach full temperature during the print
- **Limit heater power in Klipper**: Use the `max_power` parameter in each tool's `[extruder]` or `[extruder1]`, `[extruder2]`, etc. section to limit maximum power output. The value ranges from 0.0 to 1.0 (where 1.0 = 100% power). For example, setting `max_power: 0.7` limits the heater to 70% of its maximum power. This will slow heating but reduces peak power draw. See [Klipper Heater Configuration](https://www.klipper3d.org/Config_Reference.html#extruder) for details.


