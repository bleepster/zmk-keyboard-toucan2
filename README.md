# ZMK config for beekeeb Toucan2 Keyboard

[The beekeeb Toucan2 Keyboard](https://beekeeb.com/introducing-toucan2/) is a wireless split 42-key column‑stagger keyboard that a display and a trackpad, with an aggressive stagger on the pinky columns.

# Customizations

- **Keymap**: [config/toucan.keymap](config/toucan.keymap)
- **General configs**: [boards/shields/toucan/toucan_left.conf](boards/shields/toucan/toucan_left.conf) and [boards/shields/toucan/toucan_right.conf](boards/shields/toucan/toucan_right.conf)
- **Swipe shortcuts**: the `swipe_button_mapper` node in [boards/shields/toucan/toucan.dtsi](boards/shields/toucan/toucan.dtsi)
- **Invert scroll / trackpad settings**: the `tps43_trackpad` node in [boards/shields/toucan/toucan_right.overlay](boards/shields/toucan/toucan_right.overlay)

# Personal Corne v4 keymap

`config/toucan.keymap` carries the Corne v4 bindings, including home-row mods
(balanced, 200 ms tapping term, 175 ms quick tap, 125 ms prior idle), modifier
aliases, and all six layers in their original order:

| Index | Layer | Hold from Base |
| --- | --- | --- |
| 0 | Base | Default |
| 1 | Symbols | Enter or Space |
| 2 | Numbers | G or H |
| 3 | Functions | T or Y |
| 4 | Navigation | Caps Lock |
| 5 | Misc/BT | Z or period |
| 6 | Mouse | Touch the trackpad |

The six outer-column keys remain disabled, as in the Corne configuration.
The Functions-layer underglow toggle is inactive unless
`CONFIG_ZMK_RGB_UNDERGLOW` is enabled; Toucan2's status LED is a separate feature.

The mouse layer retains Toucan2's transparent main keys and thumb buttons
(left: middle/left/right; right: left/right/middle). The personal keymap overrides
only the touch processor's layer target, from 4 to 6, to avoid activating
Navigation on touch. The original scroll override still applies on layers 1 and
2 (now Symbols and Numbers). All trackpad gestures, sensitivity, scaling, split
transport, display, and power settings remain the Toucan2 defaults. The default
shield keymap remains available when no personal keymap is supplied.

# Verification

GitHub Actions builds all three entries in `build.yaml` on push and pull request:
left with the display and Studio, right with the trackpad, and settings reset.
Before merging, require all three builds to succeed and download their firmware
artifacts. See [local verification](docs/verification.md) for build commands and
results.

After flashing both halves, check every layer entry listed above, home-row
modifiers, Bluetooth/output selection, and the bootloader/reset bindings. Touch
the trackpad and confirm that the mouse layer appears, its thumb buttons work,
and release restores the previous layer. Check movement, taps, hold/drag,
two-finger scrolling, zoom, and three-finger swipes, plus scrolling while Symbols
or Numbers is held. Hardware behavior requires testing on the keyboard.

# License

The code in this repo is available under the MIT license.

The included shield nice_view_gem is modified from https://github.com/M165437/nice-view-gem licensed under the MIT License.

The linked trackpad module is based on https://github.com/geeksville/zmk_driver_azoteq

ZMK code snippets are taken from the ZMK documentation under the MIT license.

The embedded font QuinqueFive is designed by GGBotNet, licensed under under the SIL Open Font License, Version 1.1.
