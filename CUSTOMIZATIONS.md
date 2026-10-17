# Personal customizations

This fork builds the Ækeynox / Selenium keymap for a **Quacken Flex** used with
an **Ergo-L** host layout on Linux. Everything below differs from upstream
[OneDeadKey/zmk-config-aekeynox](https://github.com/OneDeadKey/zmk-config-aekeynox).

| File | What changed |
|------|--------------|
| `include/aekeynox/settings.h` | build options |
| `config/quacken_flex.keymap` | home-row mod tuning and layer overrides |
| `build.yaml` | only builds the Quacken Flex |

Hardware: only **two outer keys per side** (L: Tab, Esc; R: Backspace, Enter).
The bottom-row outer Shift keys of the 42-key layout have no physical key.

## Build options (`settings.h`)

```c
#define KB_LAYOUT_ERGOL   // host layout
#define LINUX             // shortcuts and HRM order for Linux
#define VIM_NAVIGATION    // VimNav + NumRow instead of NavNum
#define ENABLE_MOD_HOLDS  // mods held when entering a layer stay held
#define HRM_SHIFT         // Shift on the pinky home-row keys (a, u)
// hold-taps: default (HT_HOME_ROW_MODS)
```

`ENABLE_MOD_HOLDS` gives one-handed Alt+Tab: hold `n` (Alt), hold the L-home
thumb (VimNav), release `n`, tap L-index (Tab) / L-mid (Shift+Tab), release the
thumb to switch. A modifier held when entering a layer stays held until the
layer thumb is released.

## Keymap (`config/quacken_flex.keymap`)

Notation: `□` transparent (falls through to Base), `—` none.
Rows read left pinky → left inner │ right inner → right pinky.

### Home-row mods

```c
&hrm { require-prior-idle-ms = <150>; };
```

A home-row key pressed within 150 ms of the previous key is always a letter,
which stops misfires on rolls. Pause briefly before holding a home-row mod.

### Symbols

`;` and `:` sit on the same keys as `,` and `.` in Ergo-L (Shift+`,` = `;`,
Shift+`.` = `:`).

```
bottom, right:   | ! ; : ?   →   : ! | ; ?
```

### VimNav

Tab switching and close tab on the left top row; natural vertical scrolling.

```
top, left:       — Ctrl+W vim-prev vim-next —
             →   vim-prev vim-next Ctrl+PgUp Ctrl+PgDn Ctrl+W

bottom, right:   scroll← scroll↓ scroll↑ scroll→ —
             →   scroll← scroll↑ scroll↓ scroll→ —
```

Ctrl+W is sent as `LC(T)`, the QWERTY `T` key being `w` in Ergo-L.

### NumRow

Operators on the bottom row; `-` `.` `,` stay transparent on their Ergo-L keys.

```
bottom:   — — — — —  │ - , . : /
      →   — — □ = #  │ □ + : □ /
types:    — — - = #  │ . + : , /
```

### FnMedia

F20 (mic mute) on the left inner home key, empty upstream.

## Updating from upstream

```sh
git fetch upstream
git merge upstream/main
```

- `build.yaml` conflicts: keep this fork's version, unless upstream changed
  the Quacken Flex entry or its `cmake-args`.
- The layer overrides replace whole layers: if upstream changed one of these
  layers in `include/aekeynox/selenium.keymap`, port the change into the
  matching `&..._layer` override.
- Push: GitHub Actions builds `quacken_flex.uf2` (artifact `firmware-selenium`).

## Flashing

1. Bootloader: hold **L-reach** (FnMedia) + tap **L-home**, or hold the
   board's BOOT button while plugging in.
2. Copy `quacken_flex.uf2` to the `RPI-RP2` drive; the keyboard reboots.
3. If anything was changed in ZMK Studio, reset it (Studio → unlock with
   FnMedia → R-tuck → **Restore Stock Settings**): Studio edits are stored per
   layer index and override the firmware keymap.
