# Testing and troubleshooting

## Expected readings (reference device, meter black lead on ground)

| Point | Idle / colour off | Colour on |
|---|---|---|
| GRN_SGNL pad | 0 V | ~0.8 V while green (or white/yellow/cyan) is lit |
| BLU_SGNL pad | 0 V | ~0.8 V while blue (or white/cyan) is lit — **0 V in pure green** |
| BTN pad | ~2.8 V | 0 V for ~100 ms after a green-from-dark edge, then ~2.8 V |

## Symptoms

**Endless white blinking after a press while disconnected.**
- That's the white-light loop, which means the blue inhibit isn't acting: BLU_SGNL isn't connected, or is on the wrong pad. With white showing, BLU_SGNL must read ~0.8 V. See [white-loop.md](white-loop.md).

**Nothing happens on green.**
- GRN_SGNL reads 0 V with green lit → wrong pad.
- BLU_SGNL reads above ~0.3 V in pure green → this device mixes blue into green; the inhibit blocks every press. Report it with the reading.
- GRN_SGNL and BLU_SGNL swapped → green inhibits itself. Swap them.
- Check the GND joint.
- BTN drops but the device ignores it → the tap is too short for this firmware; report it with the device details.

**White looks magenta (green missing) during white blinks.**
- Something is loading the green line hard while blue is on. This points to a short between the clamp node and GRN_SGNL. (Why: [white-loop.md](white-loop.md#why-there-is-a-series-resistor-white-turning-magenta).).)

**White looks yellow or orange (blue missing).**
- The blue line is being pulled down — usually the inhibit's input and output reversed in a retrofit, or BLU_SGNL shorted to ground.

**Button appears stuck / device enters odd modes.**
- BTN sits at 0 V constantly → GRN_SGNL is on a steady high (the 2.8 V rail or the MCU pin rather than the base). Green Thumb must never hold the line.

**Taps on yellow.**
- Expected: yellow is red + green with no blue. It only matters if the device uses yellow in a state that shouldn't be acknowledged.

**Device resets or misbehaves when connected.**
- Almost always ground: GND on the antenna pad or a non-ground via. Re-check continuity to the ribbon's ground pin.

## Bench test without the device

Feed GND, and a 3 V pull-up (through ~10 kΩ) to BTN. Touch GRN_SGNL to ~0.8 V from a divider: BTN drops to near 0 V for about 100 ms and returns. Repeat with BLU_SGNL also held at ~0.8 V: BTN must not move.
