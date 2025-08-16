# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is Case's personal ZMK (Zephyr Mechanical Keyboard) firmware configuration for a Sofle clone purchased off AliExpress. The hardware is a barebones build with no display, rotary encoder, or joystick - just the basic split keyboard functionality. The config includes ZMK Studio integration (with required tweaks to get it connecting properly) and keymap-drawer for visual keymap generation.

**Project History:**
- Forked from https://github.com/a741725193/zmk-sofle
- Originally based on https://github.com/tokyo2006/zmk-config-sofle  
- Sofle keyboard designed by Josef Adamcik

## Build Commands

ZMK uses GitHub Actions for firmware builds. Local building requires the Zephyr development environment.

**GitHub Actions Build:**
- Builds are triggered automatically via the `build.yaml` workflow
- Produces firmware for both left and right halves plus variants
- Build configurations include standard, ZMK Studio-enabled, and settings reset variants

**Local Development Setup:**
```bash
# Install west (Zephyr's build tool)
pip3 install --user -U west

# Initialize workspace 
west init -l config/

# Update dependencies
west update

# Build firmware (example for left half)
west build -b eyelash_sofle_left -- -DCONFIG_ZMK_STUDIO=y -DCONFIG_ZMK_STUDIO_LOCKING=n
```

## Architecture

**Key Directories:**
- `boards/arm/eyelash_sofle/` - Board definition files (device tree, pin mappings, hardware config)
- `config/` - ZMK configuration and keymap files  
- `keymap-drawer/` - Visual keymap diagrams and configuration
- `zephyr/` - Zephyr module definition

**Hardware Platform:**
- Nordic nRF52840 microcontroller
- Split keyboard design with central/peripheral communication  
- Basic AliExpress Sofle clone (barebones build)
- No rotary encoder, OLED display, or joystick
- RGB underglow (WS2812 LEDs) 
- USB and Bluetooth connectivity

**Board Configuration:**
- `eyelash_sofle.dtsi` - Main hardware definition (GPIO mappings, matrix, peripherals)
- `eyelash_sofle_left.dts` / `eyelash_sofle_right.dts` - Split-specific configurations
- `eyelash_sofle.keymap` - Key layout and layer definitions
- `eyelash_sofle.zmk.yml` - ZMK board metadata

**Keymap Structure:**
- Layer 0: Base QWERTY layout with arrow cluster
- Layer 1: Function keys, mouse movement, RGB controls  
- Layer 2: Bluetooth settings, system controls, bootloader access
- Mouse functionality integrated with encoder for scrolling

## Development Workflow

**Keymap Modifications:**
1. Edit `boards/arm/eyelash_sofle/eyelash_sofle.keymap` for layout changes
2. Use `keymap_drawer.config.yaml` to customise visual representations
3. Generate keymap diagrams: Requires keymap-drawer Python package

**Hardware Changes:**
- Modify `.dtsi` files for GPIO or peripheral changes
- Update pin configurations in pinctrl sections
- Adjust matrix transform for physical layout changes

**Configuration Files:**
- `build.yaml` defines GitHub Actions build matrix
- `config/west.yml` specifies ZMK version and additional modules  
- Board-specific `.conf` files override default settings

**ZMK Studio Integration:**
- Enabled via `CONFIG_ZMK_STUDIO=y` build flag
- Provides real-time keymap editing capabilities
- USB UART interface for configuration
- Required config tweaks to get Studio connecting properly to this hardware
- Primary method used for keymap updates

The project follows ZMK's standard patterns and uses GitHub Actions for automated firmware builds. All customisation is done through device tree overlays and configuration files rather than C code modifications.