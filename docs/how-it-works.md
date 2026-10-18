# How it works

## The signals

Each colour of each RGB LED on the Plus+'s LED board has its own little driver transistor. The main board turns a colour on by sending a signal through a resistor into that transistor's base, and the transistor then turns the LED on.

The base of a transistor that's switched on always sits at about 0.7 to 0.8 V, no matter what's feeding it. When the colour turns off, it drops to 0 V. That makes each base a clean on and off signal for its colour. On the LED board, the green and blue bases come out to two pads sitting right next to each other on the Side B edge. You'll see the green one called "Green LED +" in some photos, but it isn't actually the LED's positive side.

Green Thumb uses the GREEN pad to know when to press, and the BLUE pad to know when not to.

## The circuit

<img src="../images/schematic.svg" alt="Green Thumb schematic" width="760">

**Q1** is a digital transistor. It's a regular NPN transistor with two resistors built into the package: one in series with the input, and one from the input to ground that keeps it off when nothing is driving it. A fraction of a volt on its input turns it on, which pulls the button line to ground. I picked it because it turns on below the 0.8 V that's available on the pad.

**C1** turns the green signal into a short pulse. A capacitor passes changes but blocks a steady voltage. When the GREEN pad jumps from 0 to 0.8 V, that jump passes straight through C1 and turns Q1 on. C1 then charges up, the voltage at Q1's input fades away, and after roughly 100 ms Q1 lets go. That gives you one tap no matter how long green stays on. When green turns off, C1 passes a drop instead, which just keeps Q1 off.

**R1** drains C1 after each tap so it's reset and ready for the next one. It also sets how quickly taps can repeat.

**Q2** is the blue check, another digital transistor. Its input is the BLUE pad and its output connects to Q1's input. Whenever blue is on, Q2 holds Q1's input at ground, so a green signal that shows up at the same time can't do anything.

**R2** sits between C1 and Q1. Without it, Q2 holding Q1's input down during white would make C1 charge straight from the green driver. That starves the green LED for the length of the blink, and white would look magenta. With R2 in place, the blue check only takes a tiny bit of current from the green line, and a green signal on its own still has plenty of strength to turn Q1 on.

## What happens with each colour

| What the LED does | What Green Thumb does |
|---|---|
| Green turns on, blue is off | Taps the button for about 100 ms |
| Yellow turns on (red and green) | Taps the button, same as green |
| Green stays on | Nothing more |
| Green turns off | Nothing |
| White or cyan (green and blue together) | Nothing, because Q2 is already holding Q1 off |
| Blue turns off while green stays on | Nothing, because there's no new green signal to pass |
| Red or blue on their own | Nothing |
| Green blinks quickly | One tap per blink, as fast as R1 lets it reset |

During white, green and blue switch on within microseconds of each other. If green beats blue by a hair, Q1 might twitch for a few microseconds, but that's way shorter than anything the Plus+ would count as a press.

The tap usually happens right as the LED starts fading in, so it often lands before you can even see green.

<img src="../images/white-loop.svg" alt="The white light loop: a green only mod versus Green Thumb" width="760">

## What it doesn't change

- **Pressing the button yourself.** Q1 and the real button are on the same line, so when you press it the line is already low and Green Thumb doesn't change anything.
- **The LEDs.** Both pads only give up a tiny bit of current, so the LEDs look and behave the same.
- **Battery life.** Nothing flows when it's idle. A tap only uses a tiny amount of current for about 100 ms.

## What I measured on my Plus+

- The GREEN and BLUE pads sit at about 0.8 V when their colour is on and 0 V when it's off.
- The BLUE pad stays at 0 V when the Plus+ shows plain green, which is what lets the blue check work.
- The button line sits at about 2.8 V and drops to 0 V when it's pressed.
- Tapping the button while it's green works like it should. Holding it down cycles through modes and eventually turns it off.
- Pressing the button while disconnected gives nine white blinks and then it stops. Without the blue check, it blinks white until you put it to sleep. With Green Thumb, it's nine and done, and white stays white.
