# Corne v4 migration verification

Local builds completed on 2026-09-05 using ZMK v0.3 (`edf5c08`),
Zephyr SDK 0.16.8 (ARM GCC 12.2.0), Python 3.12.13, and CMake 3.31.10.
The source keymap came from `cornev4-zmk` at `c5dbda0`.

| Target (seeeduino_xiao_ble) | Result | Flash | RAM |
| --- | --- | --- | --- |
| toucan_left rgbled_adapter nice_view_gem, Studio USB snippet | PASS, UF2 generated | 342,040 B | 155,898 B |
| toucan_right rgbled_adapter | PASS, UF2 generated | 193,608 B | 38,100 B |
| settings_reset | PASS, UF2 generated | 49,512 B | 11,640 B |

All builds configured, compiled, and linked successfully. They are not
warning-free: the dependency stack emits board devicetree/deprecation warnings,
the peripheral build reports USB dependency/no-source warnings, and the reset
build emits empty-keymap and unused zoom-driver warnings. Studio's nanopb
scripts also warn about deprecated `pkg_resources`; the local environment uses
`setuptools<81` because newer setuptools removes that module. No repository
build settings were changed to suppress warnings.

The generated left-half devicetree confirms touch activates layer 6, scrolling
still selects layers 1 and 2, and the home-row timing values are preserved.
A source-level comparison verified all 252 Corne bindings (with the RGB toggle
represented by its conditional macro), the entire home-row behavior, and all
42 original Toucan2 mouse bindings. Every active layer has 42 bindings.
`git diff --check` also passes.

## Reproduce

Use a ZMK v0.3-compatible build environment with the Zephyr SDK, west, CMake,
Ninja, and Zephyr's Python build dependencies. Studio additionally needs
protobuf tooling; the local build used `grpcio-tools` and `setuptools<81`.
Initialize a separate workspace, using absolute paths for the variables below:

```sh
TOUCAN_REPO=/path/to/zmk-keyboard-toucan2
TOUCAN_BUILD=/path/to/empty-build-workspace
mkdir -p "$TOUCAN_BUILD/config"
cp "$TOUCAN_REPO/config/west.yml" "$TOUCAN_BUILD/config/west.yml"
cd "$TOUCAN_BUILD"
west init -l config
west update
export Zephyr_DIR="$TOUCAN_BUILD/zephyr/share/zephyr-package/cmake"
export ZEPHYR_SDK_INSTALL_DIR=/path/to/zephyr-sdk-0.16.8

west build -p always -s zmk/app -d build/left -b seeeduino_xiao_ble \
  -S studio-rpc-usb-uart -- \
  -DZMK_CONFIG="$TOUCAN_REPO/config" -DZMK_EXTRA_MODULES="$TOUCAN_REPO" \
  -DSHIELD="toucan_left rgbled_adapter nice_view_gem" -DCONFIG_ZMK_STUDIO=y

west build -p always -s zmk/app -d build/right -b seeeduino_xiao_ble -- \
  -DZMK_CONFIG="$TOUCAN_REPO/config" -DZMK_EXTRA_MODULES="$TOUCAN_REPO" \
  -DSHIELD="toucan_right rgbled_adapter"

west build -p always -s zmk/app -d build/reset -b seeeduino_xiao_ble -- \
  -DZMK_CONFIG="$TOUCAN_REPO/config" -DZMK_EXTRA_MODULES="$TOUCAN_REPO" \
  -DSHIELD=settings_reset
```

Firmware is produced at `build/{left,right,reset}/zephyr/zmk.uf2`.
The local validation workspace and build logs are under `/tmp/toucan2-build`;
they are temporary and are not committed.

The existing manifest tracks moving branches for some dependencies. This run
resolved Zephyr to `dacab487`, the Azoteq driver to `c329f30`, the zoom processor
to `91bbe0c`, and the RGB status widget to `8756cb7`. Future GitHub Actions runs
may resolve different commits; confirm the full matrix succeeds on the PR.

## Hardware checks

Follow the layer and trackpad checklist in the repository README. Hardware
validation has not been performed. If a Studio keymap was previously saved on
the keyboard, restore its stock keymap in Studio to use this compiled keymap.
The imported Corne bindings do not include a Studio unlock key; Studio remains
enabled in the left build, but no new binding has been added for unlocking it.
