---
title: Installation
nav_order: 1
parent: Software & Configuration
---
<!-- Use the page layout at TOC.md:  https://github.com/sdylewski/StealthChanger/blob/main/docs/TOC.md -->

# Installation

All sections are required, so please follow all steps carefully.

**Prerequisites:**
- **Klipper version:** Must be newer than Jun 14, 2024 (commit 1591a51)
- **DangerKlipper:** Not currently supported
- **Before starting:** Remove your existing `PRINT_START` and `PRINT_END` macros from your config, as klipper-toolchanger-easy provides specific versions required for toolchanging

## Installing klipper-toolchanger-easy

[klipper-toolchanger-easy](https://github.com/jwellman80/klipper-toolchanger-easy) is the recommended toolchanger software for StealthChanger. The original [klipper-toolchanger](https://github.com/viesturz/klipper-toolchanger/) is no longer maintained and the "easy" fork is actively developed.

### Installation Methods

#### Method 1: Installation Script (Recommended)

The easiest way to install klipper-toolchanger-easy is using the provided installation script:

**On your Klipper host system:**
```bash
cd ~
git clone https://github.com/jwellman80/klipper-toolchanger-easy.git
cd klipper-toolchanger-easy
./install.sh
```

The installation script will:
- Install the toolchanger files to your Klipper config directory
- Set up the necessary directory structure
- Configure Moonraker for automatic updates (optional)

#### Method 2: Manual Installation

If you prefer manual installation or need more control:

**On your Klipper host system:**
```bash
cd ~
git clone https://github.com/jwellman80/klipper-toolchanger-easy.git
cd klipper-toolchanger-easy
# Copy files to your config directory
cp -r stealthchanger ~/printer_data/config/
```

### Moonraker Update Manager (Recommended)

To keep klipper-toolchanger-easy automatically updated, add this to your `moonraker.conf`:

```ini
[update_manager klipper-toolchanger-easy]
type: git_repo
path: ~/klipper-toolchanger-easy
origin: https://github.com/jwellman80/klipper-toolchanger-easy.git
primary_branch: main
managed_services: klipper
```

**Note:** If you make manual changes to the installed files, they will be overwritten when Moonraker updates. Use the configuration override files instead (see [Configuration](Configuration.md)).

### Include in printer.cfg

Add this line to your `printer.cfg` to include the toolchanger configuration:

```ini
[include stealthchanger/toolchanger-config.cfg]
```

---

**Next:** [Configuration](Configuration.md) → Set up tool files and toolchanger settings

## FAQ

**Quick Links:**
- [Installation fails with "invalid syntax" error](#installation-fails-with-invalid-syntax-error)
- [Can I use Kalico instead of mainline Klipper?](#can-i-use-kalico-instead-of-mainline-klipper)
- [Do I need to remove my existing PRINT_START macro?](#do-i-need-to-remove-my-existing-print_start-macro)
- [Will Moonraker updates overwrite my customizations?](#will-moonraker-updates-overwrite-my-customizations)
- [My KTC easy config files are not visible in mainsail](#my-ktc-easy-config-files-are-not-visible-in-mainsail)
---

### Installation fails with "invalid syntax" error
Make sure you are using at least Python 3. The installation script requires Python 3 to run properly.

### Can I use Kalico instead of mainline Klipper?
No, klipper-toolchanger-easy is not currently compatible with Kalico. You must use mainline Klipper.

### Do I need to remove my existing PRINT_START macro?
Yes, klipper-toolchanger-easy provides its own `PRINT_START` macro that is required for toolchanging. Remove or rename your existing `PRINT_START` macro before installation to avoid conflicts.

### Will Moonraker updates overwrite my customizations?
Yes, if you make manual changes to files in the `stealthchanger/` directory, they will be overwritten when Moonraker updates. Use configuration override files or modify the files in your config directory instead of modifying the installed files directly.

### My KTC easy config files are not visible in mainsail
Click the refresh button manually to invalidate the cache.
