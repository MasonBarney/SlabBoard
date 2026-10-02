# Parked: ANO navigation wheel on a future build

Research notes for a **future** SlabBoard revision, kept so the work isn't lost.

> **Nothing here applies to the current board.** The built prototype (revision 1)
> and the revision-2 wiring diagram both use a single rotary encoder. The ANO was
> evaluated for revision 2 and deliberately deferred — it needs a case designed
> around it and consumes every free pin on the right half. See
> [`README.md`](README.md) for what the board actually is.

The pin map and firmware plan below are worked out and internally consistent, but
**none of it has been built, flashed or wired.** Treat it as a design, not a spec.

---

## What the part is

The **Adafruit ANO Directional Navigation and Scroll Wheel Rotary Encoder** — a flat
iPod-style thumb wheel with five buttons (up / down / left / right / centre) around a
24-detent rotary encoder. It is *not* a knob: no shaft, no threaded bushing.

### Buy 6311, not 6310

Adafruit sells the same encoder on two different boards, and only one of them works
with ZMK.

| SKU | Board | Interface | ZMK |
|---|---|---|---|
| **6311** | **Passive breakout, encoder pre-soldered** | **raw GPIO** | **✅ use this** |
| 6310 | I²C seesaw adapter, encoder pre-soldered | I²C via onboard MCU | ❌ |
| 5221 | Passive breakout PCB, no encoder | raw GPIO | ✅ but 6311 is cheaper |
| 5001 | Bare encoder, no board | — | ✅ needs 5221 |

**6311 was $10.95** and is the right buy: pre-soldered, passive, and *cheaper than
5001 + 5221 bought separately* (~$12). Pre-soldered matters — the original reason for
going to a breakout at all was that hand-soldering bare encoder legs kept snapping
them.

### Why the I²C versions are out

Boards marked **STEMMA QT / Qwiic / I²C / seesaw** have an ATtiny running Adafruit's
seesaw firmware between the encoder and the pins. **ZMK has no seesaw driver**, in
tree or as any community module I could find. Using one would mean writing a Zephyr
I²C driver that polls seesaw's encoder-delta and GPIO registers and raises
`zmk_sensor_event` plus key-position events — on the *peripheral* half, so everything
has to cross the split link correctly. That is a driver project, not a config change.

#### Telling the two families apart

| Signal on the product page | Verdict |
|---|---|
| Title says STEMMA QT, Qwiic, I²C or seesaw | ❌ MCU in the path |
| *"no pull-up or pull-down resistors on this PCB, use your microcontroller's … hardware support"* | ✅ passive |

**The pad count is the giveaway.** A passive breakout exposes **one pad per encoder
signal** — 9 for the ANO, 5 for an EC11. A seesaw board exposes ~6 pads whatever
encoder is fitted, because those pads are the I²C bus and power, not the encoder.

This caught two parts during the search: **5880** and **4991** (I²C QT Rotary Encoder,
with and without encoder) are both seesaw. 5880 even carries exactly the encoder you'd
want — a 24-detent EC11 with push switch — but its pins go to the ATtiny, not the
header.

---

## Pinout

Nine signals. Verified against Adafruit's documentation.

| Pad | Function |
|---|---|
| ENCA | rotary encoder A |
| ENCB | rotary encoder B |
| SW1 | **centre** button |
| SW2 | **down** button |
| SW3 | **right** button |
| SW4 | **up** button |
| SW5 | **left** button |
| COMA | common for SW1, ENCA, ENCB |
| COMB | common for SW2–SW5 |

**COMA and COMB are not tied together on the board.** Both go to ground, but each
needs its own wire. Missing one leaves half the board silently dead.

⚠️ **The button-to-direction mapping is counter-intuitive and easy to get wrong.**
SW1 is the *centre*, not up. Gemini produced a plausible-looking table with four of
the five directions wrong (SW1=up, SW3=left, SW4=right, SW5=centre) — only SW2 matched.
Wire from the table above, which is Adafruit's.

### Unverified

Adafruit's pinout page was blocked by the research proxy, so **the physical pad order
along the header was never confirmed** — only the pad names and functions. Check the
silkscreen against the table above rather than assuming an order.

Also unverified: Adafruit publishes **no rotational cycle rating** for the ANO, so it
cannot be compared against the Bourns PEC11R's 30,000 cycles.

---

## Pin map — fits the right half exactly

7 GPIO needed. Revision 2's right half has exactly 7 free header pins, so the fit is
exact with nothing to spare.

| Signal | Pin | Notes |
|---|---|---|
| ENCA | `P0.17` | already the encoder A pin in revision 2 |
| ENCB | `P0.20` | already the encoder B pin in revision 2 |
| SW1 — centre | `P0.08` | already the encoder switch pin in revision 2 |
| SW2 — down | `P1.13` | |
| SW3 — right | `P1.11` | |
| SW4 — up | `P0.10` | |
| SW5 — left | `P0.06` | |
| COMA | GND | serves SW1, ENCA, ENCB |
| COMB | GND | serves SW2–SW5 |

Keeping ENCA/ENCB on `P0.17`/`P0.20` means the existing `alps,ec11` node keeps its
pins unchanged.

**This takes the right half to 18 of 18 header pins**, matching the left. Only the
`P1.01`, `P1.02` and `P1.07` inner through-holes would remain spare anywhere on the
keyboard.

---

## Firmware plan

### 1. `kscan-composite` — the matrix and the buttons together

`slabboard.dtsi` currently defines `kscan0` as the 5×12 `zmk,kscan-gpio-matrix`. The
five ANO buttons need a second `zmk,kscan-gpio-direct` node, and a
**`zmk,kscan-composite`** to join them. The composite becomes the `chosen` kscan; the
matrix and direct nodes become its children with row/column offsets.

Defining a bare `kscan0` for the five buttons — as a naive snippet will suggest —
*replaces* the keyboard matrix and leaves a 5-key macropad.

### 2. Transform and layout grow 60 → 65

- `matrix_transform0`: 60 `RC()` entries today. Needs 5 more positions for the buttons.
- `slabboard-layouts.dtsi`: 60 `&key_physical_attrs` entries today. Needs 5 more.
- `config/slabboard.keymap`: 60 bindings per layer × 4 layers → **65 bindings × 4**.

The same `kscan-composite` plumbing was already owed for the single EC12 push switch,
so going from one direct GPIO to five is nearly free once it exists. That is the main
argument in the ANO's favour.

### 3. Encoder values — `steps = 4 × detents`

The ANO is **24 detents / 24 PPR**, so:

```dts
steps = <96>;              /* 24 detents × 4 quadrature transitions */
triggers-per-rotation = <24>;
```

**`steps` is quadrature transitions per revolution, not the datasheet's "pulses per
revolution."** ZMK's config reference describes it as *"Number of encoder pulses per
complete rotation,"* which reads like the datasheet figure and is misleading. Read
literally it yields `steps = <24>`, which over-triggers 4×.

Derivation, from ZMK source:

```c
/* app/module/drivers/sensor/ec11/ec11.c */
val->val1 = (pulses * FULL_ROTATION) / drv_cfg->steps;
```

The quadrature decoder returns `delta = ±1` per **state transition**, and one detent is
a full quadrature cycle — **4 transitions**.

```c
/* app/src/behaviors/behavior_sensor_rotate_common.c */
int trigger_degrees = 360 / sensor_config->triggers_per_rotation;
triggers = remainder.val1 / trigger_degrees;
```

So degrees reported per detent = `(4 × 360) / steps`. For one keymap trigger per
physical click:

```
(4 × 360) / steps = 360 / triggers_per_rotation
⇒ steps = 4 × triggers_per_rotation = 4 × detents
```

Corroborated by ZMK's own in-tree Kyria shield: `steps = <80>` with
`triggers-per-rotation = <20>` — exactly 4× detents.

### 4. GPIO flags

With both commons grounded, use ZMK's convention throughout:

```dts
a-gpios = <&gpio0 17 (GPIO_ACTIVE_HIGH | GPIO_PULL_UP)>;
b-gpios = <&gpio0 20 (GPIO_ACTIVE_HIGH | GPIO_PULL_UP)>;
```

Verified in ZMK's `app/boards/shields/kyria/kyria.dtsi`. `GPIO_ACTIVE_LOW` on A/B
appears in some third-party guidance; inverting both channels preserves direction but
shifts phase, and there is no reason to deviate from the convention the rest of this
board already follows.

### 5. Split considerations

The ANO would sit on the **right half, which is the BLE peripheral**. Both event types
cross the split link peripheral → central, so this works:

- Buttons become **key positions** via `kscan-composite` → forwarded as key positions.
- The wheel raises **`zmk_sensor_event`** → forwarded and re-raised by the central.

Layer state does *not* cross the link, which is why the nice!view sits on the left.
That constraint is unrelated to the ANO but worth remembering when placing anything
that needs to display state.

---

## Mechanical — the reason this was deferred

| | |
|---|---|
| Board size | 38.0 × 35.6 × 1.6 mm |
| Mounting | 2 × M2.5 holes |
| Shaft | **none** — flat thumb wheel |

38 × 35.6 mm is a large footprint for a keyboard, and with no shaft there is nothing
to panel-mount through a case wall. A case cut for a 6 mm encoder shaft cannot take
it; the board has to be designed around the wheel. The 2× M2.5 holes are a plus,
since bolting the board down is what keeps wire tension off the joints.

Combined with the 18-of-18 pin consumption and the 65-position keymap rework, this is
a new keyboard rather than a revision — hence parking it.

---

## Appendix: other encoders evaluated

For the knob path, if a future build wants a conventional rotary encoder instead.

| Part | Type | GPIO | Pre-soldered | Notes |
|---|---|---|---|---|
| **DFRobot SEN0235** | Passive EC11 breakout | 3 | ✅ | 20 PPR, 30k cycles, 3.3–5 V, 33.8 × 22.4 mm. One SKU from Mouser. Chosen for revision 2. |
| **Bourns PEC11R-4215F-S0024** | Bare encoder | 3 | ✗ | 24 det/24 PPR, 30k rotational / 20k switch cycles, **metal M7 × 0.75 threaded bushing** — the only option here that panel-mounts. Pair with [bgkendall/encoder-mount](https://github.com/bgkendall/encoder-mount) (CC0, KiCad + gerbers, 4 × M2 holes). |
| CTS 288 series | Bare encoder | 3 | ✗ | 16 mm, 50k cycles, real **solder-lug** terminals. But 16 detents against 4/6/8/10/12 PPR options — detents and pulses don't match, so one click ≠ one step. Verify the specific part number. |
| SparkFun BOB-11722 | Bare breakout PCB | 3 (+3 for LEDs) | ✗ | Only fits SparkFun's *illuminated* encoders, not a generic EC11. Original encoder (COM-10982) retired; substitute COM-15141 flips the LED common from anode to cathode. RGB LED can't be driven usefully by ZMK — not an addressable strip. |

**The real lesson from the broken encoders:** the part matters less than the mounting.
Bare encoder legs are ~0.3 mm stamped tabs; on a PCB the mounting stakes and bushing
carry all load and the signal pins carry only current. Hand-wired with no PCB, every
tug on a 22 AWG wire goes straight into a thin tab. Any breakout fixes this; so does
30 AWG wire.
