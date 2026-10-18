# Switchless mods compared

> **Note:** this list isn't complete. There are also mods that use switches, and I'm only comparing switchless mods here.

All of these react to the green LED. The differences are where they pick up the green signal, what they do to the button, and whether they can tell green apart from white.

There are two problems that matter on the Plus+:

- **Long presses.** Holding the button down for a few seconds cycles through modes and can turn the Plus+ off. Any mod that holds the button down for as long as green is on can run into this.
- **The white light loop.** White has green in it, so a mod that only watches green presses on every white blink and keeps restarting the search. See [white-loop.md](white-loop.md).

<p align="center"><img src="../images/timing.svg" alt="What the button line sees under each mod when green turns on" width="700"></p>

## 1. Single wire

**Wiring:** a plain wire from a "green" pad on the LED board to the button.

**What it does:** it works backwards. The green control line is low whenever green is off, so the button reads as pressed most of the time and released when green comes on. It seems to work because a lot of firmware reacts when the button is let go.

**Problems:**
- *Getting stuck.* Any time the Plus+ checks whether the button has been let go, like when it wakes up or syncs, it finds a button that's been held down for minutes. That usually shows up as a red blink on a fixed timer until you power cycle it. Red has no green in it, so the wire never lets go.
- *White light loop.* Every white blink is a release and then a press.
- *Battery.* The button's pullup (about 13 kΩ from 2.8 V) is shorted to ground all day, so it draws about 200 µA nonstop, plus whatever a held button does to sleep.
- *Feeding back* into the green driver.

**Variant: LED removed.** Some people remove the RGB LED and solder the wire to the empty green pad instead. That pad connects straight to the board's green driver transistor, so the driver itself pulls the button low while green is on. That fixes the backwards logic, and removing the LED stops the LED's power from leaking into the button line. The downsides are that one of the four LEDs is gone for good, it holds the button for as long as green is on, and it still loops on white.

## 2. Optocoupler or photorelay (TLP785, G3VM-61G3 and similar)

There are two kinds of parts people use, and they act about the same here:
- **Optocouplers with a transistor output** (TLP785 and similar): the output transistor goes from the button to ground.
- **Photorelays** (G3VM-61G3, AQY212S, TLP172AM and similar): the output is a switch across the button.

**Wiring:** the part's internal LED is driven by green, either from a positive rail to the green driver's collector, or straight from the green pad to ground. The output goes across the button.

**What it does:** it presses the right way and it's fully isolated. The button is held down for as long as green is on.

**Problems:**
- It's a hold, so long presses can happen.
- The versions wired to the green driver's collector put the part's LED in the green LED's path, so green often gets dim or stops lighting.
- The versions wired to the green pad are running off only 0.67 to 0.86 V. The LED inside needs about 1.1 V to really turn on, so it barely lights. People report that only parts that trigger at very low current work, and they're less reliable on weaker boards.
- They vary a lot from part to part. A TLP785's gain can be anywhere from about 50% to 600%, so the same wiring can work great on one unit and not at all on another.
- It only watches green, so with the output going to ground it should loop on white. Some people say theirs doesn't, but that's most likely because it's right on the edge, not because it's a real fix. See [white-loop.md](white-loop.md#what-sometimes-seems-to-fix-it-a-diode-or-an-optocoupler).

**Variant: output to the motor's negative pad.** Some builds connect the output between the button and the vibration motor's negative pad instead of ground, and report fewer white loop issues. That pad only connects to ground while the Plus+ is sending its vibration signal, so it can only press while the Plus+ is trying to vibrate. The white blinks don't vibrate, so most of them can't press. It's fewer, not none, since white that happens during a vibration can still press. This makes it a vibration based mod (see 5).

## 3. MOSFET with pulldown

**Wiring:** gate to the green signal through a resistor, a pulldown from gate to ground, drain to the button, source to ground.

**What it does:** presses the right way and holds while green is on. The pulldown fixes the stuck press that came from the gate floating in earlier builds.

**Problems:** still a hold, and still loops on white. From the 0.8 V green pad, only MOSFETs that turn on at a really low voltage will work, and it comes down to the individual part whether yours does. That's one reason some builds added a diode and others didn't. Some diode builds are also reported to avoid the white loop; see [white-loop.md](white-loop.md) for why that's probably just being on the edge rather than a fix.

## 4. MOSFET with a blue check (u/PaperStckz)

**Wiring:** like 3, plus a second MOSFET on the blue signal that holds the first MOSFET's gate at ground. Both gates have resistors in series.

**What it does:** holds while green is on, but not while blue is on, so white can't trigger it. The author didn't see any white loops, and my testing points to the blue check being what stops them, not the pulldown.

**Problems:** still a hold, so long presses can still happen.

## 5. Vibration based (credited to vulp_vibes)

**Wiring:** there are a few versions:
- A MOSFET with its gate on green and its source on the vibration motor's switched pad instead of ground, so it only has a path to ground while the vibration signal is on.
- Two transistors in series on the button, one driven by green and one by the motor.
- A photorelay driven by green, with its output between the button and the motor's negative pad (see the variant under 2).
- Some builds add a diode in the ground path. When the source is on a motor pad, the diode stops a manual button press from feeding back and buzzing the motor.

**What it does:** presses only while green and vibration are both on. The Plus+ vibrates when it wants your attention and not during the search, so the white blinks mostly don't trigger it. White that happens during a vibration still can, so loops are cut down rather than gone. Vibrations are short, so the press ends up short too.

**Problems:**
- It only works if the pad you're using is the motor's switched side. On some boards the motor's negative side is just ground, and then the trick does nothing.
- It only presses at moments the Plus+ tries to vibrate. That still works with the motor removed, since the board keeps sending the vibration signal to the motor pads either way.
- The press lasts as long as the vibration, with no way to control it.

## 6. Green Thumb

The GREEN pad goes through a capacitor into a digital transistor that presses the button, so you get one tap of about 100 ms each time green turns on, nothing when it's idle, no long press risk and no stuck button. A second digital transistor on the BLUE pad holds the first one off whenever blue is on, with a resistor in between so it doesn't pull the green LED down. It taps when green or yellow turns on, and does nothing on white, cyan, red or blue.

## Summary

| | Single wire (LED left on) | Optocoupler or photorelay | MOSFET with pulldown | MOSFET with blue check | Vibration based | **Green Thumb** |
|---|---|---|---|---|---|---|
| Presses the right way | no, it's backwards | yes | yes | yes | yes | yes |
| How long it presses | holds while green is off | holds | holds | holds | as long as the vibration | tap |
| Long press risk | yes | yes | yes | yes | low | no |
| White light loop | likely | usually, though some units report none (on the edge), and fewer if the output goes to the motor's negative pad | yes | no | fewer | no |
| Gets stuck | yes | no | no | no | no | no |
| Battery drain when idle | about 200 µA | none | none | none | none | none |
| Green LED affected | feeds back | often dimmed | no | no | no | no |
| Only presses while vibrating | no | no | no | no | yes | no |
| Works from the 0.8 V green pad | by accident | barely (sensitive parts only, varies by unit) | barely | barely | barely | yes |
| Wires to the Plus+ | 1 | 4 | 3 | 4 | 3 or 4 | 4 |
