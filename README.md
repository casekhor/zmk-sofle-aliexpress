# Case's ZMK config for Sofle clone purchased off aliexpress

> **Note:** This documentation reflects current understanding and may require further evaluation. File roles and build behaviour should be verified before making changes.

The one I have is a cheap barebones build with no display, rotary, joystick. 

Config tweak was required to get ZMK Studio to connect to the keeb, and that's what I've been using to update the keymapping.

## Repository Structure

```bash
├── boards/arm/eyelash_sofle/          # Main board definition
│   ├── eyelash_sofle.keymap          # Primary keymap (active)
│   ├── eyelash_sofle.dtsi            # Hardware definition
│   ├── eyelash_sofle-layouts.dtsi    # Physical layout
│   ├── eyelash_sofle_left.dts        # Left half config
│   ├── eyelash_sofle_right.dts       # Right half config
│   └── *.zmk.yml, *.yaml            # Board metadata
├── config/                           # ZMK configuration
│   ├── eyelash_sofle.keymap          # Secondary keymap (different)
│   ├── eyelash_sofle.conf            # Hardware settings
│   ├── eyelash_sofle.json            # Layout for ZMK Studio
│   └── west.yml                      # Dependencies
├── keymap-drawer/                    # Visual keymap generation
│   └── eyelash_sofle.yaml           # Keymap drawer config
└── build.yaml                       # GitHub Actions build
```

### Duplicate Files Note

**Two different keymap files exist:**

- `boards/arm/eyelash_sofle/eyelash_sofle.keymap` - Active keymap used by firmware
- `config/eyelash_sofle.keymap` - Alternative keymap with different mouse settings and extra layer

The board version is the **active keymap** that gets compiled into firmware.

### config/ Directory Usage

The `config/` directory serves multiple purposes:

- **ZMK Studio**: `eyelash_sofle.json` provides layout data
- **keymap-drawer**: Triggers visual diagram generation on changes
- **Hardware settings**: `eyelash_sofle.conf` loaded as configuration overlay
- **Dependencies**: `west.yml` defines ZMK version and modules

**Important**: Due to `board_root: .` in `zephyr/module.yml`, the build uses `boards/` as primary source, making `config/eyelash_sofle.keymap` likely unused by firmware builds.

## Keymap Update Workflow

**Primary method:** ZMK Studio for real-time keymap editing (no files to edit)

**File-based changes (when needed):**

- `boards/arm/eyelash_sofle/eyelash_sofle.keymap` - Main keymap definition
- `config/eyelash_sofle.json` - Physical layout for ZMK Studio & external tools
- `keymap-drawer/eyelash_sofle.yaml` - For generating updated SVG diagrams

## Pre-Flash Checklist

**Before flashing firmware, verify these files match your needs:**

1. **`boards/arm/eyelash_sofle/eyelash_sofle.keymap`** - Your active keymap
2. **`config/eyelash_sofle.conf`** - Hardware settings (RGB, Bluetooth, etc.)
3. **`config/eyelash_sofle.json`** - Remove keys not present on your barebones build
4. **`boards/arm/eyelash_sofle/eyelash_sofle.dtsi`** - Hardware pin definitions match your PCB

**Files you likely don't need to change:**

- `*.dts` files (split half configurations)
- `west.yml` (dependencies)
- `build.yaml` (build process)

Repo also has keymap-drawer for visual diagrams.

- Repo forked from <https://github.com/a741725193/zmk-sofle>
  - Originally forked from <https://github.com/tokyo2006/zmk-config-sofle>

Sofle was created by Josef Adamcik
<https://josefadamcik.github.io/SofleKeyboard/>
<https://josef-adamcik.cz/electronics/let-me-introduce-you-sofle-keyboard-split-keyboard-based-on-lily58.html>

Other info
<https://github.com/db-ok/SofleChocWireless>
<https://github.com/mctechnology17/zmk-config>
<https://www.reddit.com/r/ErgoMechKeyboards/comments/tu3zhj/finished_for_now_sofle_choc/>

<https://github.com/zmkfirmware/>