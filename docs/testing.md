# Testing and troubleshooting

## Expected readings (reference device, meter black lead on ground)

| Point | Idle / colour off | Colour on |
|---|---|---|
| GREEN pad | 0 V | ~0.8 V while green (or white/yellow/cyan) is lit |
| BLUE pad | 0 V | ~0.8 V while blue (or white/cyan) is lit — **0 V in pure green** |
| BTN pad | ~2.8 V | 0 V for ~100 ms after a green-from-dark edge, then ~2.8 V |

## Symptoms

**Endless white blinking after a press while disconnected.**
- That's the white-light loop, which means the blue inhibit isn't acting. Reflow the BLUE joint; with white showing, the BLUE pad must read ~0.8 V. See [white-loop.md](white-loop.md).

**Nothing happens on green.**
- The GREEN pad reads 0 V with green lit → the GREEN joint isn't making contact. Reflow it.
- Check the GND joint.
- BTN drops but the device ignores it → the tap is too short for this firmware; report it with the device details.

**White looks magenta (green missing) during white blinks.**
- Green is being pulled down while blue is on, which points to a fault on the flex near the inhibit. Background: [white-loop.md](white-loop.md#why-there-is-a-series-resistor-white-turning-magenta).

**White looks yellow or orange (blue missing).**
- The blue line is being pulled down — usually a solder bridge from BLUE to GND.

**Button appears stuck / device enters odd modes.**
- BTN sits at 0 V constantly → look for a solder bridge from BTN to GND. Green Thumb itself never holds the line.

**Taps on yellow.**
- Expected: yellow is red + green with no blue. It only matters if the device uses yellow in a state that shouldn't be acknowledged.

**Device resets or misbehaves when connected.**
- Check the GND joint and look for bridges between neighbouring pads.

## Bench test without the device

Feed GND, and a 3 V pull-up (through ~10 kΩ) to BTN. Touch the GREEN pad to ~0.8 V from a divider: BTN drops to near 0 V for about 100 ms and returns. Repeat with the BLUE pad also held at ~0.8 V: BTN must not move.
