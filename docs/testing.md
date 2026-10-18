# Testing

After soldering, here's how I check that everything's working. You don't need any tools for this, just the Plus+ and your phone.

1. **Turn it on.** It should start up like normal. Press the button yourself, both a quick tap and a hold, and make sure both still work the way they did before.
2. **Catch something.** When it lights up green, it should tap the button on its own, usually on the first blink. A quick flicker of green is normal, since the tap often happens before the LED is fully on.
3. **Check the white loop.** Disconnect it from your phone and press the button once. You should get nine white blinks and then it stops. The white should look white, not purple or yellow.
4. **Use it for a while.** It should tap when green or yellow comes on, and do nothing on red, blue or white.

## If something's off

**It keeps blinking white after you press it while disconnected.**
That's the white light loop, which means the blue check isn't working. Reflow the BLUE joint. You can stop the blinking by putting the Plus+ to sleep and waking it back up. See [white-loop.md](white-loop.md).

**Nothing happens when it turns green.**
Reflow the GREEN and GND joints.

**White looks magenta (purple) during the white blinks.**
Green is being pulled down while blue is on, which usually means a problem on the flex near the blue check. There's more background in [white-loop.md](white-loop.md#why-theres-a-series-resistor-white-turning-magenta).

**White looks yellow or orange.**
Blue is being pulled down, usually from a solder bridge between BLUE and GND.

**The button acts like it's stuck, or the Plus+ goes into odd modes.**
Look for a solder bridge between BTN and GND. Green Thumb never holds the button down on its own.

**It resets or acts strange after installing.**
Check the GND joint and look for solder bridges between any of the pads.
