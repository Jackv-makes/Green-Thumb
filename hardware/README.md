# Hardware notes

The design files, bill of materials and assembly details for Green Thumb aren't published (see [LICENSE.md](../LICENSE.md)).

A couple of notes about the Plus+'s LED board:

- The LED board's ribbon connector is a Molex 503566-1102 (0.3 mm pitch, 11 pins, bottom contact), and the matching Molex cable is the 15031-04xx series. That's just for reference, since Green Thumb doesn't touch the ribbon.
- The blue and green control pads sit right next to each other on the Side B edge (blue is on the left when you're looking at Side B), so BLUE and GREEN are next to each other on the flex too.

## Flex pads

| Pad | Goes to |
|---|---|
| GREEN | the base of the green LED driver |
| BLUE | the base of the blue LED driver |
| BTN | the button line |
| GND | ground |
