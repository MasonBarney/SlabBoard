# SlabBoard keymap

Generated from [`config/slabboard.keymap`](../config/slabboard.keymap). 5 rows × 12
columns, 60 keys. Each row reads straight across the whole keyboard; the `│` marks
the split between the left half (columns 0–5) and the right (columns 6–11).

`▽` is transparent — the key falls through to the layer below.

| Layer | Reached by |
|---|---|
| Base | default |
| Lower | `MO1`, left thumb |
| Raise | `MO2`, right thumb |
| Adjust | `MO3`, either thumb |

## Base

```
 ESC     1      2      3      4      5    │   6      7      8      9      0     BSPC 
 TAB     Q      W      E      R      T    │   Y      U      I      O      P      \   
 CTRL    A      S      D      F      G    │   H      J      K      L      ;      '   
SHIFT    Z      X      C      V      B    │   N      M      ,      .      /    ENTER 
 GUI    ALT    MO3    MO1   SPACE   ESC   │  DEL   ENTER   MO2    MO3    ALT    GUI  
```

**Encoder:** VOL+ / VOL-

## Lower

```
  `      F1     F2     F3     F4     F5   │   F6     F7     F8     F9    F10    DEL  
  ▽      1      2      3      4      5    │   6      7      8      9      0     F11  
  ▽      ▽      ▽      ▽      ▽      ▽    │   ←      ↓      ↑      →     HOME   F12  
  ▽      ▽      ▽      ▽      ▽      ▽    │   ▽      ▽      ▽     END    PGUP   PGDN 
  ▽      ▽      ▽      ▽      ▽      ▽    │   ▽      ▽      ▽      ▽      ▽      ▽   
```

**Encoder:** PGUP / PGDN

## Raise

```
  ~      !      @      #      $      %    │   ^      &      *      (      )     DEL  
  ▽      ▽      ▽      ▽      ▽      ▽    │   -      =      [      ]      \      `   
  ▽      ▽      ▽      ▽      ▽      ▽    │   _      +      {      }      |      ~   
  ▽      ▽      ▽      ▽      ▽      ▽    │   ▽      ▽      ▽      ▽      ▽      ▽   
  ▽      ▽      ▽      ▽      ▽      ▽    │   ▽      ▽      ▽      ▽      ▽      ▽   
```

**Encoder:** NEXT / PREV

## Adjust

```
BTCLR   BT0    BT1    BT2    BT3    BT4   │   ▽      ▽      ▽      ▽      ▽    RESET 
  ▽      ▽      ▽      ▽      ▽      ▽    │   ▽      ▽      ▽      ▽      ▽     BOOT 
  ▽      ▽      ▽      ▽      ▽      ▽    │   ▽      ▽      ▽      ▽      ▽      ▽   
  ▽      ▽      ▽      ▽      ▽      ▽    │   ▽      ▽      ▽      ▽      ▽      ▽   
  ▽      ▽      ▽      ▽      ▽      ▽    │   ▽      ▽      ▽      ▽      ▽      ▽   
```

**Encoder:** BRI+ / BRI-

## Changing it

The left half builds with ZMK Studio enabled (`studio-rpc-usb-uart`), so you can plug
it in over USB, open [ZMK Studio](https://zmk.studio) and remap live without
rebuilding firmware. Edit `config/slabboard.keymap` for changes you want in the
repository.

This layout is a starting point, not a tuned one. The bottom row is the weak part:
six thumb keys per hand is unusual, and it was padded with duplicates (`ESC` also on
row 0, `ENTER` also on row 3) to fill the grid.
