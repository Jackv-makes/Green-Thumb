# Changelog

## First public release (October 2026)

- Taps the button for about 100 ms each time green turns on, and does nothing while green stays on or turns off.
- Blue check: a second digital transistor on the blue control line holds the tap off whenever blue is on, so white and cyan never press the button. This is what stops the white light loop.
- A resistor between the capacitor and the blue check, so the blue check doesn't pull the green LED down and white stays white.
- Pads: GREEN, BLUE, BTN, GND.
- Taps the base of the green and blue LED drivers on the LED board (PMC-WN-SUB-01-DK).
- Tested on my Plus+: green clears on the first blink, a press while disconnected gives nine white blinks and stops, red or blue on their own do nothing, pressing the button by hand works as normal, and there's no drain when idle.
