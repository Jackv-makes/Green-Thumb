# FAQ

**Why does my Plus+ flash white forever?**
That's the white light loop. White has green in it, so a mod that only watches green presses the button on every white blink, and each press restarts the search. Putting it to sleep and waking it up stops it, and so does a power cycle. Green Thumb's blue check keeps it from happening in the first place. More in [white-loop.md](white-loop.md).

**Does a pulldown resistor fix the white loop?**
No. A pulldown fixes an input that's floating and keeps pressing after the LED turns off. Green Thumb's tap transistor already has one built in, and without the blue check it still loops. The loop happens because the mod is doing exactly what it's told on a green signal that's part of white. Only a second signal, like blue or vibration, can tell them apart.

**Does the diode some mods use fix it?**
Some people say it does. My best guess is that green is driven a little weaker during white, and the diode leaves the MOSFET only just able to pull the button down, which is enough for plain green but not for white. That depends on things that change between parts, temperature and battery level, so I wouldn't count on it. The loop is still there underneath too, since a manual press during the white flashes still resets the count. In vibration based builds a diode can also have a different job: stopping a manual press from feeding back into the motor. More in [white-loop.md](white-loop.md).

**Some people say their optocoupler mod doesn't loop on white. Why?**
Probably the same reason as the diode. The optocoupler barely turns on at the green pad's 0.67 to 0.86 V, and green seems to be driven stronger in some states than others, so on some units it registers a catch but not white. It changes from part to part and unit to unit, so it isn't something to rely on.

**What happens when blue turns off?**
Nothing. Green Thumb only taps when green turns on while blue is off. If blue turns off while green stays on, there's no new green signal, so no tap.

**Why not use vibration instead of blue?**
It can work, as long as the Plus+ always vibrates when it wants a press and never during the search, and you're tapping the motor's switched side. But it only presses at moments the Plus+ tries to vibrate, and the press is as long as the vibration. Using blue means Green Thumb doesn't depend on the vibration signal at all.

**Can it only tap on green that isn't followed by another colour?**
Not at the moment green turns on, since it can't see the future. It could wait and only press if green stays on by itself for a while, using a delay or a small microcontroller, but the Plus+ doesn't need that.

**Does it tap on yellow or cyan?**
It taps on yellow, which is what you want. It doesn't tap on cyan, since cyan has blue in it.

**Is Green Thumb a MOSFET mod?**
No. Both transistors are digital transistors (regular transistors with their resistors built in). They act like the MOSFET mods, but the pulldown is already built in and they turn on at a voltage that suits the 0.8 V green pad.

**Can it press before the LED lights up?**
Pretty much. It reacts to the green control signal, which turns on before you can actually see the LED, especially when the LED fades in.

**What if I press the button while Green Thumb is tapping?**
Nothing weird happens. Both pull the same line down, so the Plus+ just sees one press. Holding the button down works the same as stock.

**Does it drain the battery?**
Not when it's idle. A tap only uses a tiny bit of current for about 100 ms.

**Why is the "Green LED +" pad only 0.8 V?**
Because it isn't the LED's positive side. It's the base of the green driver transistor, and the base of a transistor that's switched on always sits at about 0.7 to 0.8 V.

**Can I build my own?**
The design files and parts list aren't published (see [LICENSE.md](../LICENSE.md)). The docs are here so you can understand how it works and how it compares.

**Why aren't there kits or bare boards?**
The license allows a Plus+ with Green Thumb installed to be sold, and installing it as a service is fine too, but the board can't be sold on its own, as a kit, or as a bare PCB. See [LICENSE.md](../LICENSE.md).
