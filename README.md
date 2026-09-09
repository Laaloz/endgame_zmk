# Endgame Split — ZMK firmware

Wireless low-profile split (44 keys, Choc, nice!nano v2, ZMK v0.3).

<img width="2048" height="1704" alt="eg-v1" src="https://github.com/user-attachments/assets/39979478-4d00-48ee-b398-5acb06f698cd" />

## Structure
```
config/
  boards/shields/endgame/
    Kconfig.shield          # endgame_left / endgame_right shield symbols
    Kconfig.defconfig       # keyboard name, split roles (left = central)
    endgame.dtsi            # kscan matrix + matrix-transform + physical layout (shared)
    endgame_left.overlay    # left: column pins
    endgame_right.overlay   # right: column pins + col-offset 6
    endgame.zmk.yml         # shield metadata (siblings: endgame_left, endgame_right)
  endgame.keymap            # BASE / NUM / SYM / FUN + home-row mod behaviors
  endgame.conf              # sleep, battery, debounce (Studio currently off)
  west.yml                  # ZMK dependency manifest (pinned to v0.3)
build.yaml                  # build both halves for nice_nano_v2
.github/workflows/build.yml # runs ZMK's build-user-config workflow (v0.3)
```

## Matrix (verified from the endgame_left_v7 PCB)
- Columns COL0..5 = outer, pinky, ring, middle, index, inner → pro_micro 4,5,6,7,8,9
- Rows ROW0..3 = top, home, bottom, thumb → pro_micro 21,20,19,18
- Thumbs ROW3: thumb_inner=COL2, near=COL3, mid=COL4, far=COL5 (COL0/COL1 are empty on ROW3)
- diode-direction = col2row (COL → switch → diode → ROW)
- Both halves use the same pins; the right half is shifted to columns 6–11 with
  `col-offset = <6>` in `endgame_right.overlay`

## Getting started
1. Fork or clone this repo. It is a complete ZMK user config: `config/` holds
   the shield, keymap and settings, `build.yaml` selects what gets built.
2. Push → GitHub Actions builds both halves → download the `firmware` artifact
   from the workflow run. The zip contains one file per half:
   - `endgame_left-nice_nano_v2-zmk.uf2`
   - `endgame_right-nice_nano_v2-zmk.uf2`
3. Put each half into the bootloader (double-tap RST quickly) → drag the
   matching `.uf2` onto the NICENANO drive. Flash the left half first.
4. Pairing: the left half is central and advertises — pair the left half to
   your computer. The right half connects to the left automatically. If the
   halves don't find each other: add a `settings_reset` entry to `build.yaml`
   (`board: nice_nano_v2`, `shield: settings_reset`), flash that firmware to
   both halves, then flash the normal firmware again and re-pair the host.
   `&bt BT_CLR` (FUN layer, top-left) only clears the bond of the active profile.

## FI vs EN
The base layer is position-based. When the OS keyboard layout is Finnish, the
SEMI/SQT/LBKT keys produce ö ä å. When the layout is US, the same keys produce
; ' [. Switch the layout in the OS — the firmware stays the same.

The SYM layer uses US-position keycodes, so its printed symbols are correct with
a US layout. With a Finnish layout some of them land elsewhere; a separate FI
symbol mapping would be needed for every symbol to match. AltGr (RALT) sits on
the outer right thumb for the third-level characters of the Finnish layout.

## Tuning
ZMK Studio is currently **disabled** (`CONFIG_ZMK_STUDIO=n` in `endgame.conf`,
and the `studio-rpc-usb-uart` snippet is removed from `build.yaml`) while the
battery/sleep behaviour is being tested. Tapping terms, home-row mods and thumbs
are therefore tuned in `config/endgame.keymap` and need a reflash:

- `hml` / `hmr` home-row mods: balanced, tapping-term 250 ms, quick-tap 150 ms,
  require-prior-idle 125 ms; a hold only triggers with keys on the opposite half
- `tl` thumb layer-taps: balanced, tapping-term 180 ms, quick-tap 150 ms

Current `endgame.conf`:
- Idle after 15 s, deep sleep after 5 min (`CONFIG_ZMK_SLEEP=y`)
- Battery reporting on, level reported every 10 min
- Choc debounce: 4 ms press / 6 ms release

To re-enable Studio: set `CONFIG_ZMK_STUDIO=y` and add
`snippet: studio-rpc-usb-uart` to the `endgame_left` entry in `build.yaml`. The
physical layout in `endgame.dtsi` and the `&studio_unlock` key (FUN layer,
top-right) are already in place.

If a key doesn't register, or registers the wrong row/column: first check the
col2row direction and the pro_micro pins.

## Keymap
- **BASE** — QWERTY with home-row mods (GACS: A=GUI, S=Alt, D=Ctrl, F=Shift,
  mirrored on J K L ;). ESC, Shift and Ctrl on the outer left column.
- **NUM** — numbers on the top row, arrows on the home row, Home/End/PgUp/PgDn
  on the bottom row, `- = \` on the right.
- **SYM** — coding symbols: shifted number symbols on the top row, brackets
  and `< >` on the home row, `~ | \ _ - + =` on the bottom row.
- **FUN** — F1–F12, media and volume, Bluetooth profiles 0–4, `BT_CLR`,
  USB/BLE output toggle, Studio unlock.

Thumbs (outer → inner | inner → outer):
`GUI | FUN | TAB/SYM | SPACE/NUM` — `ENTER/NUM | BSPC/SYM | FUN | AltGr`

<img width="1600" height="1000" alt="keymap-base" src="https://github.com/user-attachments/assets/034da217-4fb8-43a0-94b1-ae2287cd1b2e" />

<img width="1600" height="1000" alt="keymap-fun" src="https://github.com/user-attachments/assets/1227d4e4-b9bb-4c67-bf03-caece1139476" />

<img width="1600" height="1000" alt="keymap-num" src="https://github.com/user-attachments/assets/88267dea-a991-42a8-b3b5-96933553d161" />

<img width="1600" height="1000" alt="keymap-sym" src="https://github.com/user-attachments/assets/7828c694-9b30-43aa-bc4c-a806431b3677" />
