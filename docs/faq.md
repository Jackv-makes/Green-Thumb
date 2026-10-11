# FAQ

**Why did my device flash white forever?**
The white-light loop. White contains green, so a mod that only watches green presses on every white blink, and each press restarts the search. Green Thumb's blue inhibit prevents it. Full explanation in [white-loop.md](white-loop.md).

**Does a pull-down resistor fix the white loop?**
No. A pull-down fixes a *floating* input that keeps pressing after the LED goes off. Green Thumb's tap transistor has a pull-down built in, and without the blue inhibit the circuit still loops. The loop is a correct response to a green signal that's part of white; only a second input — blue or vibration — can tell them apart.

**Does the diode some mods use fix it?**
Some builders report it does. The likely reason is that green is driven more weakly (PWM) during white, and a diode in the source leg leaves the MOSFET only just able to pull the button low — enough for solid green, not for white. That depends on margins that vary between parts, temperature and battery level, so treat it as unreliable. The loop mechanism itself is still there: a manual press during the white flashes still resets the count. In vibration-gated builds a diode can have a different job: blocking a back-feed path into the motor. Details in [white-loop.md](white-loop.md).

**What happens when blue turns off?**
Nothing. Green Thumb only taps on a green *rising* edge while blue is off. If blue turns off while green stays on (white → green), there's no new edge, so no tap.

**Why not gate on vibration instead of blue?**
It works if the device always vibrates when it wants a press and never during the search, and if you tap the motor's switched side. But auto-press then depends on vibration being enabled, and the press length is whatever the vibration burst is. Blue gating keeps auto-press independent of vibration.

**Can it trigger only on green that isn't followed by another colour?**
Not at the moment green turns on — the circuit can't see the future. It could *wait* (only press if green stays on alone for some time) with a delay stage or a small microcontroller. Not needed on the reference device.

**Does it trigger on yellow or cyan?**
Yellow (no blue) yes; cyan (has blue) no.

**Is Green Thumb a MOSFET mod?**
No. Both transistors are bipolar digital transistors with their pull-downs built in and a threshold that suits the 0.8 V tap.

**Can it press before the LED flashes?**
Effectively yes. It responds to the driver's control signal, which rises before the die is visibly lit, especially during a PWM fade-in.

**What if I press the button while Green Thumb is tapping?**
Nothing unusual. Both pull the same line low; the MCU sees one press. Manual holds behave exactly as stock.

**Does it use battery?**
Not at rest. A tap costs tens of microamps for ~100 ms.

**Why is the "Green LED +" pad only 0.8 V?**
It isn't the LED's anode; it's the base of the green driver transistor, clamped at one base-emitter drop.

**Can I build my own?**
The design files and parts list aren't published; see LICENSE.md. The docs explain the principle so the behaviour can be understood and compared.

**Why aren't there kits or bare boards?**
See LICENSE.md. Green Thumb is offered installed on a device or as an installation service, not as a standalone board or kit.
