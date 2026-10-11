# Hardware reference

The Green Thumb design files, bill of materials and assembly details are not published; see `LICENSE.md`.

Notes about the **device's** LED board, useful for installation and for anyone documenting a different device:

- The LED board's ribbon connector is a Molex 503566-1102 (0.3 mm, 11 circuits, bottom contact); the mating Molex cable is the 15031-04xx series. Noted for identification only — Green Thumb doesn't touch the ribbon.

## Flex pads

| Pad | Goes to |
|---|---|
| GRN_SGNL | base node of the green LED driver |
| BLU_SGNL | base node of the blue LED driver — must read 0 V in the pure-green state |
| BTN | button line |
| GND | ground |

On the reference board the blue and green control pads are adjacent on the Side B edge (blue on the left as viewed from Side B), so GRN_SGNL and BLU_SGNL sit next to each other on the flex.
