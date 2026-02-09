# CLAUDE.md

This is a ZMK firmware configuration repository for a Lotus58 wireless split keyboard using Nice Nano v2 microcontrollers.

## Build Commands

Firmware is built via GitHub Actions. Pushing to any branch or opening a PR triggers a build.

```bash
# Manual trigger via GitHub CLI
gh workflow run build.yml

# Download firmware artifacts from GitHub Actions after build completes
gh run download
```

Local builds require the Zephyr west tool:
```bash
west init -l config
west update
west build -s zmk/app -b nice_nano_v2 -- -DSHIELD=lotus58_left
```

## Repository Structure

- `config/lotus58.keymap` - Keymap definitions, layers, combos, and behaviors
- `config/lotus58.conf` - Hardware feature toggles (encoders, display, Bluetooth)
- `config/west.yml` - West manifest pointing to ZMK v0.3
- `build.yaml` - GitHub Actions build matrix defining board/shield combinations
- `.github/workflows/build.yml` - CI workflow using ZMK's reusable build action

## Architecture

**Split keyboard design**: Separate firmware for left (primary) and right halves.

Build matrix produces three firmware files:
1. `lotus58_left` - Left half with Nice View display and ZMK Studio support
2. `lotus58_right` - Right half (peripheral)
3. `settings_reset` - Utility firmware to clear stored settings

## Hardware Configuration

Current settings in `lotus58.conf`:
- **Encoders**: EC11 rotary encoders enabled
- **Pointing**: Mouse/pointing device support enabled
- **Bluetooth**: TX power +8dBm for better range
- **Sleep**: Power saving enabled
- **Keyboard name**: "liminal"

## Keymap Structure

Five layers defined:
- Layer 0: Default (QWERTY base)
- Layer 1: Lower (F-keys, symbols, navigation)
- Layer 2: Raise (Bluetooth controls, media, system functions)
- Layer 3: Adjust (placeholder)
- Layer 4: Extra (placeholder)

### ZMK Patterns Used

**Mod-morphs** - Keys that change behavior when modifiers are held:
```
fofunc: sends 4, or F4 when Alt held
sleft:  sends S, or Left arrow when Gui held
fright: sends F, or Right arrow when Gui held
```

**Combos** - Simultaneous key presses:
```
positions 24+52 → LGUI (on layer 0 only)
```

**Sensor bindings** - Encoder actions per layer:
```
sensor-bindings = <&inc_dec_kp PG_UP PG_DN &inc_dec_kp C_VOL_DN C_VOL_UP>;
```
Format: `<left_encoder_binding right_encoder_binding>`

**Layer access**:
- `mo 1` - Momentary layer 1 (lower)
- `mo 2` - Momentary layer 2 (raise)

## Common Tasks

**Change a key binding**: Edit the `bindings` array in the appropriate layer in `config/lotus58.keymap`

**Add a new combo**: Add to the `combos` block with `key-positions`, `layers`, and `bindings`

**Modify encoder behavior**: Edit `sensor-bindings` in each layer

**Toggle features**: Edit `config/lotus58.conf` (comment/uncomment CONFIG lines)

**Flash firmware**: Download .uf2 files from GitHub Actions, put keyboard in bootloader mode (double-tap reset), drag .uf2 to the mounted drive
