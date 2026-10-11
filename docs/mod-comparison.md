# The mods that exist, and what each one actually does

All of these react to the green LED channel. They differ in *where* they sense it, *what* they do to the button line, and whether they can tell green from white.

Two failure modes matter on this device:

- **Long-press modes.** Holding the button for seconds cycles through modes and can power the device down. Any mod that holds the button for as long as green is lit risks this.
- **The white-light loop.** White contains green; a mod that only watches green presses on every white blink, restarting the search indefinitely. See [white-loop.md](white-loop.md).

<p align="center"><img src="../images/timing.svg" alt="What the button line sees under each mod when green turns on" width="700"></p>

## 1. Single wire

**Wiring:** a bare wire from a "green" pad on the LED board to the button line.

**What it does:** the logic is inverted. The green control line is low whenever green is **off**, so the button reads *pressed* most of the time and *released* when green comes on. It appears to work because many firmwares act on the release edge.

**Failure modes:**
- *Stuck-button error loop.* Any time the firmware checks for a released button — wake, sync, charge — it finds one that's been held for minutes. The usual result is a fixed-interval red blink until power-cycle; red contains no green, so the wire never lets go.
- *White-light loop.* Every white blink is a release-then-press.
- *Battery.* The button's pull-up (~13 kΩ from 2.8 V) is shorted through the MCU pin all day: ~200 µA continuous, plus whatever a "held" button does to sleep.
- *Back-feeding* the green driver's base.

## 2. Opto-relay (G3VM-61G3 and similar)

**Wiring:** the relay's input LED between a positive rail and the green driver's collector; its output contact across the button.

**What it does:** correct polarity, fully isolated. The button is held for as long as the green die is being driven.

**Failure modes:** held press (long-press modes); the relay's LED sits in the green die's current path, so green often dims or drops out; it needs milliamps, so it can't run from the base pad; four wires between two boards. It watches green only, so it also loops on white.

## 3. MOSFET with pull-down

**Wiring:** gate to the green signal through a series resistor, pull-down from gate to ground, drain to the button, source to ground.

**What it does:** correct polarity, holds while green is on. The pull-down fixes the floating-gate "stuck press" that earlier versions suffered.

**Failure modes:** still a hold; still loops on white. From the ~0.8 V base pad only very-low-threshold MOSFETs fire, and part-to-part spread decides whether a given one does — one reason some builds added a diode and others didn't. Some diode builds are also reported to avoid the white loop; see [white-loop.md](white-loop.md) for why that's probably a margin effect rather than a fix.

## 4. MOSFET with blue inhibit (u/PaperStckz)

**Wiring:** as #3, plus a second MOSFET gated from the blue signal, clamping the first MOSFET's gate to ground. Series resistors on both gates.

**What it does:** holds while green is on, but not while blue is on. White can't trigger it. The author reported no white loops; Green Thumb's testing indicates the inhibit, not the pull-down, is what prevents them.

**Failure modes:** still a hold, so long-press exposure remains.

## 5. Vibration-gated (credited to vulp_vibes)

**Wiring variants:**
- MOSFET gate on green, **source on the vibration motor's switched pad** instead of ground. The MOSFET only has a ground return while the motor runs.
- Two transistors in series in the button path, one driven by green and one by the motor signal.
- A diode in the ground path in some builds; where the source sits on a motor pad, a diode there blocks a back-feed path through the MOSFET's body diode that can otherwise buzz the motor on manual presses.

**What it does:** presses only while green **and** vibration are both on — an AND gate. The device vibrates when it wants attention and not during the search, so white blinks don't trigger it. Vibration bursts are short, so the press is naturally short too.

**Failure modes and trade-offs:**
- Only works if the motor's switched side is the pad you tap. Measure it idle: ~2.8 V means low-side switched (the source-on-motor method works); 0 V means it's just ground (that method does nothing).
- Auto-press only works with vibration enabled and only at moments the device vibrates.
- Press length is whatever the vibration burst is; there's no control over it.

## 6. Green Thumb

Base-node voltage tap → series capacitor → digital transistor → button: one ~100 ms tap on each green rising edge, nothing at rest, no long-press risk, no stuck button. Plus a blue inhibit: a second digital transistor on the blue control line clamps the tap transistor's input whenever blue is on, behind a series resistor so the clamp doesn't load the green LED. One tap on green-from-dark; nothing on white, cyan, red or blue.

## Summary

| | Single wire | Opto-relay | MOSFET + R | MOSFET + blue inhibit | Vibration-gated | **Green Thumb** |
|---|---|---|---|---|---|---|
| Polarity | inverted | correct | correct | correct | correct | correct |
| Duration | hold (while off) | hold | hold | hold | vibration burst | tap |
| Long-press risk | yes | yes | yes | yes | low | no |
| White-light loop | likely | yes | yes | no | no | no |
| Stuck-button loop | yes | no | no | no | no | no |
| Idle drain | ~200 µA | 0 | 0 | 0 | 0 | 0 |
| Green brightness | affected | often dimmed | unaffected | unaffected | unaffected | unaffected |
| Needs vibration on | no | no | no | no | yes | no |
| Fires from 0.8 V pad | by accident | no | marginal | marginal | marginal | yes |
| Wires to device | 1 | 4 | 3 | 4 | 3–4 | 4 |

The one thing Green Thumb gives up versus the hold-type mods is the ability to *hold* the button on purpose; a device that needs a hold is better served by mod 4 or 5.
