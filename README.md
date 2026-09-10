# SlabBoard

ZMK firmware for the SlabBoard — a hand-wired wireless split keyboard built on two
nice!nano v2 halves.

| | |
|---|---|
| Matrix | 5 rows × 12 columns, 60 keys (full 5×6 grid per half) |
| Diode direction | `col2row` |
| Split | BLE, left half is central |
| Right half | EC12 rotary encoder, nice!view display |
| Left half | Azoteq TPS65 trackpad (I²C) |
| Underglow | none, this board has no LEDs |

## Building

Pushes that touch `config/**` or `build.yaml` trigger the ZMK build in GitHub
Actions. Grab the `.uf2` files from the run's artifacts under the **Actions** tab.

Two targets are built, both on `nice_nano//zmk`:

| Shield | Extras |
|---|---|
| `slabboard_left` | ZMK Studio over USB (`studio-rpc-usb-uart`) |
| `slabboard_right` | `nice_view` display shield (ZMK built-in) |

To flash, double-tap reset on a half to mount it as a USB drive, then copy the
matching `.uf2` onto it.

## Pinout

Pins are named as they are silkscreened on the nice!nano v2 — raw nRF52840 port
names (`P0.22`, `P1.15`), not Pro Micro D-numbers. For the board itself, see
nicekeyboards' [official nice!nano v2 pinout](https://nicekeyboards.com/static/1788ac663060fd510f4894b286cd97b1/3c492/pinout-v2.png).

| Net | Pins |
|---|---|
| ROW 0–4 | `P0.22`, `P0.24`, `P1.00`, `P0.11`, `P1.04` |
| COL 0–5 (left) | `P1.06`, `P0.09`, `P1.15`, `P0.02`, `P0.29`, `P0.31` |
| COL 6–11 (right) | the same six, reversed, with `col-offset = <6>` |
| EC12 encoder (right) | A `P0.17`, B `P0.20` |
| TPS65 trackpad (left) | SDA `P0.17`, SCL `P0.20`, RDY `P1.11` |

\* `P1.01`, `P1.02` and `P1.07` are plated through-holes set inboard of the two edge
headers rather than on them — ordinary holes, same size, soldered the same way.
| nice!view | SCK `P0.10`, MOSI `P1.01`\*, CS `P1.11` |

The right half reverses its column list because the halves are mirror images of
each other. If a build types the right half backwards, that reversal is the thing
to undo — `slabboard_right.overlay` marks the spot.

## Trackpad

The Azoteq TPS65 on the left half runs over I²C on `i2c0`, driven by
[AYM1607/zmk-driver-azoteq-iqs5xx](https://github.com/AYM1607/zmk-driver-azoteq-iqs5xx) — an external ZMK module pulled in by `config/west.yml`. Its README lists
the TPS65 as tested. Gesture and tuning options are documented in that module's
`dts/bindings/input/azoteq,iqs5xx-common.yaml`.

`P0.17` and `P0.20` do double duty: I²C on the left half, encoder A/B on the
right. Each half only enables its own.

## Not yet wired up

- **EC12 push switch** (`P1.02`). The 5×6 grid is full, so the switch has no
  matrix position; it needs a `zmk,kscan-gpio-direct` plus `kscan-composite` and a
  61st keymap entry.

[`docs/`](docs/) has the full wiring reference — rendered pinout diagrams for both
halves, complete pin tables, and the standalone HTML page with the diode/matrix and
battery schematics.

See [`CLAUDE.md`](CLAUDE.md) for the nice!nano silkscreen mapping and build notes.

## Layout

`config/slabboard.keymap` — four layers (Base / Lower / Raise / Adjust), 60
bindings each, with an encoder binding per layer.

## License

MIT — see [LICENSE](LICENSE).
