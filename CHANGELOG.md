# Changelog

## First public release — 2026-10

- One-shot tap: a ~100 ms button press on each green rising edge; nothing while green stays on or turns off.
- **Blue inhibit:** a second digital transistor on the blue channel's control line blocks the tap whenever blue is on, so white and cyan never press the button. This prevents the white-light loop.
- Series resistor between the coupling capacitor and the clamp node, so the inhibit doesn't load the green LED (white stays white).
- Pads: GRN_SGNL, BLU_SGNL, BTN, GND.
- Tap points: base nodes of the green and blue LED drivers on the LED board (PMC-WN-SUB-01-DK).
- Verified on the reference device: green state clears on the first blink; a press while disconnected gives nine white blinks and stops; red or blue alone do nothing; manual presses unaffected; no idle current.
