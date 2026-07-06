# Endgame Split — ZMK-firmis

Langaton matalaprofiilinen split (44 näppäintä, Choc, nice!nano v2, ZMK).

## Rakenne
```
config/
  boards/shields/endgame/
    Kconfig.shield
    Kconfig.defconfig
    endgame.dtsi            # kscan-matriisi + matrix-transform (jaettu)
    endgame_left.overlay    # vasen: sarakepinnit
    endgame_right.overlay   # oikea: sarakepinnit + col-offset 6
    endgame.zmk.yml
  endgame.keymap            # BASE / NUM / SYM / FUN
  endgame.conf              # uni, akku, Studio, debounce
build.yaml                  # käännä molemmat puoliskot
```

## Matriisi (varmistettu PCB:stä endgame_left_v7)
- Sarakkeet COL0..5 = outer, pinky, ring, middle, index, inner → pro_micro 4,5,6,7,8,9
- Rivit ROW0..3 = top, home, bottom, thumb → pro_micro 21,20,19,18
- Peukalot ROW3: thumb_inner=COL2, near=COL3, mid=COL4, far=COL5
- diode-direction = col2row

## Käyttöönotto
1. Luo GitHub-repo tästä `zmk-config/`-kansiosta (tai forkkaa zmk-config-template ja korvaa sisältö).
2. Push → GitHub Actions kääntää → lataa artefaktit:
   - `endgame_left-nice_nano_v2-zmk.uf2`
   - `endgame_right-nice_nano_v2-zmk.uf2`
3. Bootloader kumpaankin (RST 2× nopeasti) → raahaa oikea .uf2 → NICENANO-levylle.
4. Paritus: vasen = central, se mainostaa; parita vasen tietokoneeseen. Oikea yhdistyy vasempaan automaattisesti. Jos puolet eivät löydä toisiaan: molemmat bootloaderiin, ZMK "reset settings" -snippetillä tai poista bond.

## FI vs EN
Base-layer on paikkaperustainen. Kun käyttöjärjestelmän layout on suomalainen,
näppäimet SEMI/SQT/LBKT tuottavat ö ä å. Kun layout on US (koodaus), samat
näppäimet tuottavat ; ' [. Vaihda layout käyttöjärjestelmästä — firmis pysyy samana.

## Hienosäätö
Kaikki tapping-termit, home-row-modit ja peukalot voi säätää ZMK Studiolla
(CONFIG_ZMK_STUDIO=y) ilman uudelleenflashausta. Jos jokin näppäin ei rekisteröi
tai rekisteröi väärän rivin/sarakkeen: tarkista ensin col2row-suunta ja pro_micro-pinnit.
