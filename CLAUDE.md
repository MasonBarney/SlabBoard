# SlabBoard — ZMK config

ZMK firmware for the SlabBoard, a hand-wired wireless split keyboard on two
nice!nano v2 halves. One shield, `slabboard`, built as `slabboard_left` and
`slabboard_right`.

## The shield

Source of truth is `slabboard.dtsi` + `slabboard_{left,right}.overlay`. It matches
the published wiring diagram exactly.

- **Matrix**: 5 rows × 12 cols, **60 keys**, full 5×6 grid per half, no gaps
- **Diode direction**: `col2row` — columns are driven, rows are read with pull-downs
- **Rows** (both halves): `P0.22`, `P0.24`, `P1.00`, `P0.11`, `P1.04`
- **Cols left** (COL 0 outer pinky → COL 5 inner index):
  `P1.06`, `P0.09`, `P1.15`, `P0.02`, `P0.29`, `P0.31`
- **Cols right**: the same six pins **in reverse**, plus `col-offset = <6>`, because
  the halves are mirror images. COL 6 is the inner index column, COL 11 outer pinky.
- **Split**: BLE, **left half is central**
- **Encoder** (right only): EC12, A `P0.17` / B `P0.20`; `disabled` in the dtsi,
  enabled in `slabboard_right.overlay`
- **nice!view** (right only): SPI3 — SCK `P0.10`, MOSI `P1.01` (underside pad),
  CS `P1.11`. Sharp `ls0xx` memory LCD: chip-select/clock/data only, **no D/C line**
- **RGB underglow**: none. This board has no LEDs.
- **Keymap**: `config/slabboard.keymap` — 4 layers (Base / Lower / Raise / Adjust),
  60 bindings each, encoder sensor-binding per layer

### Not yet wired up

- **Azoteq TPS65 trackpad** (left half: SDA `P0.17`, SCL `P0.20`, RDY `P1.11`).
  ZMK has no in-tree Azoteq driver, so this needs an external west module. There
  is a commented-out stub in `slabboard_left.overlay` — its `compatible` and property
  names are placeholders and **must** be replaced with the real binding, not guessed.
- **EC12 push switch** (`P1.02` pad). The 5×6 grid is full, so the switch cannot
  live in the matrix; it needs a `zmk,kscan-gpio-direct` plus `kscan-composite`
  and a 61st keymap position.

## Other shields here

`nice_view_gem` and `lpm_view` are display shields carried over from the previous
config. `build.yaml` uses `nice_view_gem` on the right half. `lpm_view` is currently
unusable with this shield: it attaches to a `lpm_view_spi` label that
`slabboard_right.overlay` does not define — only `nice_view_spi` exists.

## nice!nano v2 pin naming

Manufacturer's pinout (the authority on what each pad is):
<https://nicekeyboards.com/static/1788ac663060fd510f4894b286cd97b1/3c492/pinout-v2.png>

The board silkscreens **raw nRF52840 port names** (`P0.22`, `P1.15`), not Pro Micro
D-numbers. Both dialects address the same physical pin. This repo uses `&gpioN`
throughout — keep it that way.

| Silkscreen | Pro Micro | | Silkscreen | Pro Micro |
|---|---|---|---|---|
| `P0.06` | 1 / TXO | | RAW | — |
| `P0.08` | 0 / RXI | | GND | — |
| GND | — | | RST | — |
| GND | — | | VCC | — |
| `P0.17` | 2 | | `P0.31` | A3 / 21 |
| `P0.20` | 3 | | `P0.29` | A2 / 20 |
| `P0.22` | 4 | | `P0.02` | A1 / 19 |
| `P0.24` | 5 | | `P1.15` | A0 / 18 |
| `P1.00` | 6 | | `P1.13` | SCK / 15 |
| `P0.11` | 7 | | `P1.11` | MISO / 14 |
| `P1.04` | 8 | | `P0.10` | MOSI / 16 |
| `P1.06` | 9 | | `P0.09` | 10 |

Also broken out on the underside: `P1.01`, `P1.02`, `P1.07`, plus SWD/SWC and the
`B+`/`B−` battery pads.

**Orientation**: labels are printed on the **bottom**. Read a pinout with the labels
facing you and USB-C pointing away — RAW falls on the left, `P0.06` on the right.
From the component side the columns are mirrored.

`P0.09` and `P0.10` are the nRF52840's NFC pins; they work as GPIO only with
`CONFIG_NFCT_PINS_AS_GPIO=y`, which ZMK's nice!nano board definition sets. `P0.09`
is COL 1 and `P0.10` is the display clock, so both halves depend on it.

## Published artifacts

- [nice!nano Wiring Reference](https://claude.ai/code/artifact/4d036039-86e7-446b-abcc-93ed0737649a)
  — hand-wiring pinouts, bottom view. **The `slabboard` shield matches this.**

## Open questions

- **Right-half column order is the most likely thing to be wrong.** `slabboard_right.overlay`
  assumes the halves are wired as mirror images and reverses the column list. If the
  right half is instead wired identically to the left, un-reverse it — the comment in
  that file says where.
- The firmware has never been built or flashed. Nothing here is hardware-verified.
- `nice_nano//zmk` is kept as the board string because that is what the previous
  repo was building with.
- The previous `build.yaml` set `snippet:` twice in one entry (`studio-rpc-usb-uart`
  then `nrf52840-nosd`). YAML keeps only the last, so Studio support was silently
  dropped. This file sets one snippet per entry; `nrf52840-nosd` is not carried over —
  restore it if it was actually needed.

## History

This project started in the `lunar-zmk` repository, where the shield was called
`lunar` and, before that, `paw` (from Bastardkb paw). The `paw` shield described a
different keyboard — 4×6 `row2col` with a PAW3222 trackball on SPI — and was left
behind rather than carried over. That history is still in `MasonBarney/lunar-zmk`.
