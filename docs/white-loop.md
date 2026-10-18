# The white light loop

## What it looks like

With the Plus+ disconnected, you press the button and it starts flashing white, and it never stops on its own. Putting it into sleep mode and waking it back up stops it, and so does power cycling it or connecting it to your phone.

## What it normally does

Normally a press while disconnected starts a search, which shows up as nine white blinks, and then it stops.

## Why green only mods loop

White is red, green and blue all on together. During each white blink, the green channel gets switched on the exact same way it does for a real green: same control line, same voltage, same timing. A mod that only watches green can't tell the difference, so it presses the button on every white blink. Every press restarts the search, which makes more white blinks, which makes more presses.

<p align="center"><img src="../images/white-loop.svg" alt="The white light loop: a green only mod versus Green Thumb" width="760"></p>

## How I confirmed it

On my Plus+, Green Thumb's tap circuit without the blue check made the white flashing go on forever. With its ground lifted, so it couldn't press anything, the same press gave nine white blinks and stopped. With the blue check in place, it's nine blinks and done every time.

## What doesn't fix it

- **A pulldown on the input.** A floating input causes its own problem, where the press sticks after the LED goes off, and a pulldown does fix that. But Green Thumb's tap transistor already has one built in, and without the blue check it still loops. The loop isn't a stuck input. It's the mod doing exactly what it's told on a green signal that happens to be part of white.
- **A one shot on its own.** Turning a hold into a tap stops long press problems, but a tap on every white blink is still a press on every blink.

## What sometimes seems to fix it: a diode or an optocoupler

Some MOSFET builds put a diode in the ground leg, and the people who built them say it stops the white loop. Others pointed out that the loop is still there on those units, since pressing the button by hand during the nine white flashes still resets the count. So the diode must be stopping white from counting as a press, not getting rid of the cause.

Here's my best guess at what's happening:

- A lot of devices drive green a bit weaker during white to balance the colour, since green LEDs are much brighter than red and blue. If the Plus+ does this, the green signal during white is weaker than it is for plain green.
- With a diode in the ground leg, the MOSFET can only pull the button line down to about 0.6 to 0.7 V, which is right on the edge of what the Plus+ counts as a press.
- Plain green pulls it low enough to count. White's weaker green doesn't, or it flickers and the Plus+ ignores it.

If that's right, the diode works because it's sitting right on the edge, not because it can actually tell white from green. Differences between parts, temperature and battery level can push that either way, including to the point where plain green stops working too. It can work on some units, but it isn't something I'd rely on.

**Optocouplers wired to ground seem to work the same way.** Builds that run an optocoupler (like a TLP785) straight from the green pad, with its output from the button to ground, are also reported to not loop on some units. The optocoupler's internal LED barely turns on at 0.67 to 0.86 V, so how hard it pulls the button down depends a lot on how strongly green is being driven. One person measured about 0.666 V on the green pad normally and up to about 0.86 V while catching. If green is weaker during white, the optocoupler might not register white but still register a catch. It's fragile for the same reasons as the diode, and optocouplers also vary a lot from one part to the next, so the same wiring might loop on one unit and not press at all on another. Same as with the diode, a manual press during the white flashes still resets the count, so the loop is still there underneath.

A one shot without the blue check wouldn't get any help from this, since it fires on the very first green signal either way.

## What actually fixes it

You need a second signal that's different between white and green.

**A blue check (Green Thumb, and the two MOSFET build shared by u/PaperStckz).** White always has blue in it, and the green catch state doesn't. A second transistor on the blue control line holds the press transistor off whenever blue is on. Green on its own taps, and white or cyan does nothing. This only works if blue stays completely off while the Plus+ shows plain green, which it does on mine.

**Vibration (the approach credited to vulp_vibes).** Only press while green is on and the vibration motor is running. The Plus+ vibrates when it wants your attention and doesn't vibrate during the search, so the white blinks mostly can't press. People have done this a few ways: putting the press transistor's ground on the motor's negative pad, putting a second transistor in series that's driven by the motor, or wiring a photorelay's output between the button and the motor's negative pad. The photorelay builders report fewer white loops, but not none, since white that happens during a vibration can still press. It also only presses when the Plus+ vibrates, and it won't work if the motor has been removed, which a lot of mods do. Some wirings can let a manual button press feed back into the motor, and some builds add a diode to stop that.

## Why there's a series resistor (white turning magenta)

If the blue check grabbed Q1's input right at the capacitor, it would stop the loop, but white would look magenta. While the check is on, the capacitor would charge straight from the green driver with nothing limiting it, pulling the green driver's base down for the length of each blink. Green drops out, and red plus blue is magenta.

Green Thumb puts R2 between the capacitor and the spot where the blue check grabs. That way the check only takes a tiny bit of current from the green line, white stays white, and a green signal on its own still gets through R2 strong enough to tap.

## Things the blue check doesn't cover

- **Green that comes straight out of white isn't tapped.** If the Plus+ ever went from white straight to green with no dark gap in between, green would already be on when blue turned off, so there'd be no new green signal to tap on. On my Plus+, the catch state starts from dark (three green blinks, then solid), so this doesn't come up.
- **It can't see the future.** Green Thumb can't know, the moment green turns on, whether another colour is coming next. A delay or a small microcontroller could get close, but the Plus+ doesn't need it.
