# Endgame Split — ZMK firmware

Wireless low-profile split (44 keys, Choc, nice!nano v2, ZMK).

## Structure
```
config/
  boards/shields/endgame/
    Kconfig.shield
    Kconfig.defconfig
    endgame.dtsi            # kscan matrix + matrix-transform + physical layout (shared)
    endgame_left.overlay    # left: column pins
    endgame_right.overlay   # right: column pins + col-offset 6
    endgame.zmk.yml
  endgame.keymap            # BASE / NUM / SYM / FUN
  endgame.conf              # sleep, battery, Studio, debounce
  west.yml                  # ZMK dependency manifest (tracks main)
build.yaml                  # build both halves
```

## Matrix (verified from the endgame_left_v7 PCB)
- Columns COL0..5 = outer, pinky, ring, middle, index, inner → pro_micro 4,5,6,7,8,9
- Rows ROW0..3 = top, home, bottom, thumb → pro_micro 21,20,19,18
- Thumbs ROW3: thumb_inner=COL2, near=COL3, mid=COL4, far=COL5
- diode-direction = col2row

## Getting started
1. Create a GitHub repo from this `zmk-config/` folder (or fork the
   zmk-config-template and replace the contents).
2. Push → GitHub Actions builds → download the two artifacts (one per half):
   - left half `.uf2`  (endgame_left)
   - right half `.uf2` (endgame_right)
3. Put each half into the bootloader (double-tap RST quickly) → drag the
   matching `.uf2` onto the NICENANO drive.
4. Pairing: the left half is central and advertises — pair the left half to
   your computer. The right half connects to the left automatically. If the
   halves don't find each other: put both into the bootloader and use ZMK's
   "reset settings" snippet, or remove the bond.

## FI vs EN
The base layer is position-based. When the OS keyboard layout is Finnish, the
SEMI/SQT/LBKT keys produce ö ä å. When the layout is US, the same keys produce
; ' [. Switch the layout in the OS — the firmware stays the same.

## Tuning
All tapping terms, home-row mods and thumbs can be adjusted with ZMK Studio
(CONFIG_ZMK_STUDIO=y) without reflashing. If a key doesn't register, or
registers the wrong row/column: first check the col2row direction and the
pro_micro pins.

<img width="1600" height="1000" alt="keymap-base" src="https://github.com/user-attachments/assets/034da217-4fb8-43a0-94b1-ae2287cd1b2e" />

<img width="1600" height="1000" alt="keymap-fun" src="https://github.com/user-attachments/assets/1227d4e4-b9bb-4c67-bf03-caece1139476" />

<img width="1600" height="1000" alt="keymap-num" src="https://github.com/user-attachments/assets/88267dea-a991-42a8-b3b5-96933553d161" />

<img width="1600" height="1000" alt="keymap-sym" src="https://github.com/user-attachments/assets/7828c694-9b30-43aa-bc4c-a806431b3677" />
