# The white-light loop

## Symptom

With the device disconnected, a button press makes it flash **white**, and it keeps flashing white forever instead of giving up. Only a power cycle (or a connection) stops it.

## What the device normally does

Pressing the button while disconnected starts a search. The stock behaviour is **nine white blinks, then stop**.

## Why a green-only mod loops

White is red + green + blue. During each white blink, the green channel's driver is switched on exactly as it is for a real green — same control line, same voltage, same timing. A mod that only watches green can't tell the difference, so it presses the button on every white blink. Each press restarts the search, which produces more white blinks, which produce more presses.

<p align="center"><img src="../images/white-loop.svg" alt="The white-light loop: a green-only mod versus Green Thumb" width="760"></p>

## How it was confirmed

On the reference device, Green Thumb's tap circuit *without* its blue inhibit made the white flashing endless. With that circuit's ground lifted (so it couldn't press anything) and everything else identical, the same button press gave **nine white blinks and stopped**. With the blue inhibit in place: nine blinks and stop, every time.

## What doesn't fix it

- **A pull-down on the gate/input.** Floating inputs cause their own problem — a press that sticks after the LED goes off — and a pull-down fixes that. But Green Thumb's tap transistor already has a pull-down built in, and without the blue inhibit the circuit still loops. The loop isn't a stuck input; it's a correct response to a green signal that happens to be part of white.
- **A one-shot on its own.** It turns a hold into a tap, which stops long-press problems, but a tap per white blink is still a press per blink.

## What sometimes appears to fix it: a diode or an optocoupler

Some MOSFET builds put a diode in the source (ground) leg, and their authors report it stops the white loop. Others have pointed out that the loop mechanism is still present on those devices — pressing the button by hand during the nine white flashes still resets the count — so the diode must be stopping *white* from registering as a press rather than removing the cause.

The likely explanation:

- Many devices drive green at reduced PWM duty during white, to balance the colour (green dies are much brighter than red and blue). If this device does, the green signal during white is weaker or chopped compared with pure green.
- A diode in the source leg means the MOSFET can only pull the button line down to about 0.6–0.7 V — right at the edge of what the MCU accepts as a logic low.
- Solid green drives the MOSFET hard enough to cross that edge; white's weaker green doesn't, or produces a flickering press that firmware debounce rejects.

If that's right, the diode works by sitting on a threshold, not by telling white from green. Part-to-part spread, temperature and battery voltage can move that threshold either way — including to the point where pure green stops registering. It's a plausible result on some units, not a reliable fix.

**Optocouplers wired to ground show the same effect.** Builds that drive an optocoupler (for example a TLP785) straight from the green control pad, with its output from the button to ground, are also reported not to loop on some units. The optocoupler's input LED barely conducts at 0.67–0.86 V, so how hard its output pulls the button depends steeply on how strongly green is being driven. One builder measured about 0.666 V on the green pad normally and up to about 0.86 V during catching. If green is weaker during white, the optocoupler can fail to register white while still registering a catch. It's fragile for the same reasons as the diode, plus the optocoupler's large part-to-part gain spread: the same wiring may loop on one unit and fail to press at all on another. As with the diode, a manual press during the white flashes still resets the count, so the loop mechanism hasn't gone away.

A one-shot without an inhibit wouldn't benefit from this effect: it fires on the first edge, PWM'd or not.

To check whether a device PWMs green during white, compare the green control pad on a DC voltmeter in solid green and in a held white; a clearly lower reading in white means it does.

## What reliably fixes it

A **second input** that is different between white and green.

**Blue inhibit — Green Thumb, and the two-MOSFET build shared by u/PaperStckz.**
White always contains blue; the green "press me" state doesn't. A second transistor driven from the blue channel's control line holds the press transistor's input at ground whenever blue is on. Green alone → tap. White or cyan → nothing.

Requirement: the blue control line must read **0 V in the pure-green state**. Some devices add a little blue to their green for colour balance; on those, a blue inhibit would block every press.

**Vibration gating — the approach credited to vulp_vibes.**
Press only while green is on *and* the vibration motor is running. If the device vibrates when it actually wants attention and doesn't vibrate during the search, white blinks never produce a press. Implementations put the press transistor's ground return on the motor's switched pad, put a second transistor in series driven by the motor signal, or wire a photorelay's output between the button and the motor's negative pad (builders of that version report fewer white-light loop issues — fewer, because white that coincides with vibration can still press). Trade-offs: it only works if the device always vibrates at the moments you want a press; turning vibration off also turns auto-press off; and if the motor is low-side switched, some wirings let a manual button press back-feed the motor (the diode in some builds blocks that path).

## Why there is a series resistor: white turning magenta

If the blue inhibit clamped the tap transistor's input directly at the coupling capacitor, it would stop the loop but white would look **magenta**. While the clamp is on, the capacitor would charge straight from the green driver's base node with nothing limiting the current, pulling the base below its turn-on voltage for the length of each blink: green drops out, and red + blue = magenta.

Green Thumb puts a series resistor (R2) between the capacitor and the clamp point. The clamp then costs the green driver a negligible current, white stays white, and a lone green edge still reaches the tap transistor through R2 with enough drive to press.

## Limitations of the blue inhibit

- **Green that emerges from white isn't tapped.** If the device goes white → green without a dark gap, green was already high when blue released, so there's no rising edge. On the reference device the "press me" state starts from dark (three green blinks, then solid), so this doesn't arise.
- **Yellow still taps.** Red + green has no blue in it. If a device used yellow for something that shouldn't be acknowledged, it would need a red inhibit as well.
- **It can't look ahead.** "Green not followed by another colour" isn't detectable at the moment green turns on. A delay-then-check circuit or a small microcontroller could approximate it; neither is needed on the reference device.
