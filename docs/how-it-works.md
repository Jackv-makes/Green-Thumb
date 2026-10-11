# How Green Thumb works

## The signals it listens to

Each die of the device's RGB LEDs has its own driver: an NPN transistor on the LED board, its base driven from the MCU through a resistor, its collector on the die's cathode. When the MCU wants a colour, it drives that channel's base; the transistor pulls the cathode to ground and the die lights.

A driven NPN base sits at about 0.7–0.8 V regardless of what feeds it — the base-emitter junction clamps it. When the MCU releases the line, the base falls to 0 V. So each base node is a clean "this colour is being driven" signal with a 0 → 0.8 V swing. On the reference board the green and blue base nodes are exposed on adjacent pads on the Side B edge. (Community photos label the green one "Green LED +", which is misleading — it isn't the LED's anode.)

Green Thumb taps the **green** base node (GRN_SGNL) to know when to press, and the **blue** base node (BLU_SGNL) to know when not to.

## The circuit

<img src="../images/schematic.svg" width="760">

**Q1 is a digital transistor** — an NPN with its bias resistors in the package, one in series with the base and one from base to emitter. A fraction of a volt on its input turns it on, pulling the button line to ground. With no input, the internal pull-down holds it off; it can't float. It was chosen for a turn-on point below the 0.8 V available.

**C1 turns a level into a pulse.** When GRN_SGNL steps from 0 to 0.8 V, the step passes through C1 to Q1's input and Q1 turns on. C1 then charges, the input voltage decays, and once it drops below Q1's turn-on point — after roughly 100 ms — Q1 releases. One tap, however long green stays on. When green turns off, C1 passes a *negative* step, well within the part's reverse rating, and Q1 stays off.

**R1 drains C1** from the GRN_SGNL side so it's reset within a fraction of a second and ready for the next rising edge. It also limits how fast taps can repeat.

**Q2 is the blue inhibit,** another digital transistor. Its input is BLU_SGNL; its output is tied to Q1's input (the clamp node). Whenever blue is on, Q2 holds Q1's input at ground, so any green edge that arrives at the same time goes nowhere.

**R2 sits between C1 and the clamp node.** Without it, Q2 clamping during white would make C1 charge directly from the green driver's base, starving the driver for the length of the blink: green would drop out and white would look magenta. With R2 in the path, the clamp draws only a small current from the green line, and a lone green edge still drives Q1 through R2 with plenty of margin.

## What the button sees

| Event | Result |
|---|---|
| Green turns on, blue off | Tap (~100 ms) |
| Green stays on | Nothing more |
| Green turns off | Nothing |
| White or cyan blink (green + blue together) | Nothing — Q2 is already clamping when the green edge arrives |
| Blue turns off, green still on | Nothing — C1 charged through R2 during the clamp, so there's no rising edge left to pass |
| Blue turns off, green off | Nothing |
| Red or blue alone | Nothing |
| Yellow (red + green) | Tap |
| Fast green blinks | One tap per edge, up to the reset-rate limit |

Green and blue switch on within microseconds of each other during white. If green wins that race by a hair, Q1 may twitch for microseconds — far shorter than any firmware debounce.

The press usually lands within the first PWM cycles of a fade-in, before the die is visibly lit.

<img src="../images/white-loop.svg" width="760">

## What it doesn't affect

- **Manual presses.** Q1 and the physical button are in parallel on the same line. When you press, the line is already low; nothing Green Thumb does changes it. When Q1 is off it leaks nanoamps.
- **The LEDs.** Both taps draw microamps from nodes the MCU drives with milliamps. No dimming, no colour shift, no timing change.
- **Battery.** Nothing flows at rest. A tap costs tens of microamps for ~100 ms.

## Measured on the reference device

- Green and blue control pads: 0.8 V with the channel driven, 0 V otherwise.
- Blue control pad in the pure-green state: 0 V (the inhibit requirement).
- Button line: pulled up internally by the MCU, ~2.8 V idle, 0 V pressed.
- A hand *tap* in the green state clears it; a hand *hold* cycles modes and then powers down.
- Press while disconnected, no mod: nine white blinks, stop. Same circuit without the blue inhibit: endless. Green Thumb: nine and stop, white stays white.
