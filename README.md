# SlabBoard

ZMK firmware for the SlabBoard — a hand-wired wireless split keyboard built on two
nice!nano v2 halves.

| | |
|---|---|
| Matrix | 5 rows × 12 columns, 60 keys (full 5×6 grid per half) |
| Diode direction | `col2row` |
| Split | BLE, left half is central |
| Right half | EC12 rotary encoder, nice!view display |
| Left half | Azoteq TPS65 trackpad *(not yet wired up — see below)* |
| Underglow | none, this board has no LEDs |

## Building

Pushes that touch `config/**` or `build.yaml` trigger the ZMK build in GitHub
Actions. Grab the `.uf2` files from the run's artifacts under the **Actions** tab.

Two targets are built, both on `nice_nano//zmk`:

| Shield | Extras |
|---|---|
| `slabboard_left` | ZMK Studio over USB (`studio-rpc-usb-uart`) |
| `slabboard_right` | `nice_view_gem` display shield |

To flash, double-tap reset on a half to mount it as a USB drive, then copy the
matching `.uf2` onto it.

## Pinout

Pins are named as they are silkscreened on the nice!nano v2 — raw nRF52840 port
names (`P0.22`, `P1.15`), not Pro Micro D-numbers.

| Net | Pins |
|---|---|
| ROW 0–4 | `P0.22`, `P0.24`, `P1.00`, `P0.11`, `P1.04` |
| COL 0–5 (left) | `P1.06`, `P0.09`, `P1.15`, `P0.02`, `P0.29`, `P0.31` |
| COL 6–11 (right) | the same six, reversed, with `col-offset = <6>` |
| EC12 encoder | A `P0.17`, B `P0.20` |
| nice!view | SCK `P0.10`, MOSI `P1.01`, CS `P1.11` |

The right half reverses its column list because the halves are mirror images of
each other. If a build types the right half backwards, that reversal is the thing
to undo — `slabboard_right.overlay` marks the spot.

## Not yet wired up

- **Azoteq TPS65 trackpad** (left half: SDA `P0.17`, SCL `P0.20`, RDY `P1.11`).
  ZMK has no in-tree Azoteq driver, so this needs an external west module.
  `slabboard_left.overlay` carries a commented stub whose `compatible` string and
  property names are **placeholders** — replace them with the real binding.
- **EC12 push switch** (`P1.02` pad). The 5×6 grid is full, so the switch has no
  matrix position; it needs a `zmk,kscan-gpio-direct` plus `kscan-composite` and a
  61st keymap entry.

See [`CLAUDE.md`](CLAUDE.md) for the full pin reference, the nice!nano silkscreen
mapping, and build notes.

## Layout

`config/slabboard.keymap` — four layers (Base / Lower / Raise / Adjust), 60
bindings each, with an encoder binding per layer.

## License

MIT — see [LICENSE](LICENSE).
