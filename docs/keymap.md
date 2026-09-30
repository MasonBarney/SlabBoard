# SlabBoard keymap

Generated from [`config/slabboard.keymap`](../config/slabboard.keymap). 5 rows x 12
columns, 60 keys. Each row reads straight across the board; `|` marks the split
between the left half (columns 0-5) and the right (columns 6-11).

| Symbol | Meaning |
|---|---|
| `▽` | transparent - falls through to the layer below |
| `*` | mod-tap: this is the **tap**; see *Hold behaviours* below |
| `~` | a macro or mod-morph; see *Custom behaviours* below |
| `G-` `A-` `C-` `S-` | Cmd, Alt, Ctrl, Shift |

| Layer | Reached by |
|---|---|
| Base | default |
| Lower | `MO1`, left thumb |
| Raise | `MO2`, right thumb |
| Adjust | `MO3`, either thumb |

## Base

```
 ESC     1      2      3      4      5    |   6      7      8      9      0      -   
  =      Q      W      E      R      T    |   Y*     U*     I*     O*     P      \   
 TAB     A*     S      D      F*     G    |   H      J      K      L      ;      '   
 BD~     Z*     X*     C*     V*     B*   |   N      M      ,      .      /      `   
 GUI    ALT    MO3    MO1    SHFT   GUI   |  ENT*   SPC    MO2    MO3    ALT   C-ESC 
```

**Encoder:** VOL+ / VOL-

## Lower

```
   `        F1       F2       F3       F4       F5    |    F6       F7       F8       F9      F10      DEL   
   ▽        1        2        3        4        5     |    6        7        8        9        0       F11   
   ▽        ▽        ▽        ▽        ▽        ▽     |   A-<-     <-*       v        ^       ->*      A-->  
   ▽        ▽       CIC~     PUW~   C-A-G-M     ▽     |    ▽        ▽        ▽       END      PGUP     PGDN  
   ▽        ▽        ▽        ▽        ▽        ▽     |    ▽        ▽        ▽        ▽        ▽        ▽    
```

**Encoder:** PGUP / PGDN

## Raise

```
 ~    !    @    #    $    %   |  ^    &    *    (    )   DEL 
 ▽    ▽    ▽    ▽    ▽    ▽   |  -    =    [    ]    \    `  
 ▽    ▽    ▽    ▽    ▽    ▽   |  _    +    {    }    |    ~  
 ▽    ▽    ▽    ▽    ▽    ▽   |  ▽    ▽    ▽    ▽    ▽    ▽  
 ▽    ▽    ▽    ▽    ▽    ▽   |  ▽    ▽    ▽    ▽    ▽    ▽  
```

**Encoder:** NEXT / PREV

## Adjust

```
BTCLR   BT0    BT1    BT2    BT3    BT4   |   ▽      ▽      ▽      ▽      ▽    RESET 
  ▽      ▽      ▽      ▽      ▽      ▽    |   ▽      ▽      ▽      ▽      ▽     BOOT 
  ▽      ▽      ▽      ▽      ▽      ▽    |   ▽      ▽      ▽      ▽      ▽      ▽   
  ▽      ▽      ▽      ▽      ▽      ▽    |   ▽      ▽      ▽      ▽      ▽      ▽   
  ▽      ▽      ▽      ▽      ▽      ▽    |   ▽      ▽      ▽      ▽      ▽      ▽   
```

**Encoder:** BRI+ / BRI-

## Hold behaviours (`*`)

Tap sends the legend shown in the grid; hold sends this instead.

| Binding | Tap | Hold |
|---|---|---|
| `&mt LG(Y) Y` | `Y` | `G-Y` |
| `&mt LG(U) U` | `U` | `G-U` |
| `&mt LG(I) I` | `I` | `G-I` |
| `&mt LG(O) O` | `O` | `G-O` |
| `&mt LG(A) A` | `A` | `G-A` |
| `&mt LG(F) F` | `F` | `G-F` |
| `&mt LG(Z) Z` | `Z` | `G-Z` |
| `&mt LG(X) X` | `X` | `G-X` |
| `&mt LG(C) C` | `C` | `G-C` |
| `&mt LG(V) V` | `V` | `G-V` |
| `&mt LG(B) B` | `B` | `G-B` |
| `&mt LG(RET) RET` | `ENT` | `G-ENT` |
| `&mt LA(LEFT) LEFT` | `<-` | `A-<-` |
| `&mt LA(RIGHT) RIGHT` | `->` | `A-->` |

## Custom behaviours (`~`)

| Grid | Name | Kind |
|---|---|---|
| `BD~` | `&bspc_del` | mod-morph |
| `CIC~` | `&cap_in_Chrome` | macro |
| `PUW~` | `&paste_unformatted_word` | mod-morph |

## Hold-tap timing

Applied globally to every `&mt` and `&lt` in the keymap.

| Behaviour | Settings |
|---|---|
| `&mt` | tapping-term-ms = <275>; flavor = "tap-preferred"; quick-tap-ms = <200>; require-prior-idle-ms = <150> |
| `&lt` | tapping-term-ms = <275>; flavor = "tap-preferred" |

## Changing it

The left half builds with ZMK Studio enabled (`studio-rpc-usb-uart`), so you can plug
it in over USB, open [ZMK Studio](https://zmk.studio) and remap live without
rebuilding. Studio writes into the keyboard's own settings and does **not** change
this repository - edit `config/slabboard.keymap` for changes you want kept here.
