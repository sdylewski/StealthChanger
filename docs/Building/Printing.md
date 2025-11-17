---
title: Printing
nav_order: 4
parent: Building
---
<!-- Use the page layout at TOC.md:  https://github.com/sdylewski/StealthChanger/blob/main/docs/TOC.md -->

## Printer Calibration
The SteatlthChanger shuttle and backplate need to mate together with good tolerances, especially when using the recommended CNC-machined shuttles.  In order to not drive yourself crazy, please calibrate and tune your printer settings before starting this project. [Don't just use a calibration cube](https://www.youtube.com/watch?v=H7OsnMLDIMwa)!

#### E-steps, Pressure Advance, and Flow ratio:

* Ellis's Print Tuning guide (for E-steps, PA, flow ratio)

#### Shrinkage and Skew
* [Cauliflower](https://www.printables.com/model/682023-califlower-calibration-stl-calculator-mk1) (paid original)
* [Calistar](https://www.printables.com/model/778188-calistar-parametric-open-source-alternative-to-cal/files) open source alternative to Cauliflower


## Settings

All print settings are the same as [Voron Standards](https://docs.vorondesign.com/sourcing.html#print-settings) or better:

* Layer height: 0.2mm
* Extrusion width: 0.4mm, forced
* Infill percentage: 40%
* Infill type: grid, gyroid, honeycomb, triangle, or cubic
* Wall count: 4
* Solid top/bottom layers: 5
* Supports: NONE

Since every filament shrinks at a different rate and our tolerances are too tight to allow for "pre-scalling".  You need to calibrate the shrinkage for each and every filament you use, even the same brand but different colour can shrink at a different rate.  We recommend using [Cauliflower](#shrinkage-and-skew) for each filament.


- Shuttle is the only part that requires supports and should be set to `touching build plate`

## Orientation

Print orientation is flat on the parts back with supports enabled currently, we are working on models with integrated supports.

- All stls are in the proper print orientation
- Please check any user mods for requirements

![Print Orientation](https://github.com/DraftShift/StealthChanger/blob/main/media/Print_orientation.jpg?raw=true)
