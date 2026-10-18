# Green Thumb

[![Support me on Ko-fi](https://ko-fi.com/img/githubbutton_sm.svg)](https://ko-fi.com/jackvmakes)

<p align="center">
  <img src="images/green-thumb.png" alt="Green Thumb flex PCB, showing the BTN, GND, BLUE and GREEN pads" width="340">
</p>

This is a small flex PCB for the Pokémon GO Plus+ that presses the button for you when the green LED comes on. I wanted an auto catch mod that taps the button instead of holding it down, and that doesn't get stuck in the white light loop like a lot of the other mods out there.

The flex sits on the LED board and solders to four points: GREEN, BLUE, BTN and GND. When green turns on by itself, it taps the button for about 100 ms. If blue is on too (white or cyan), it does nothing, so it can't get stuck on white. The rest of the time it just sits there and doesn't draw any real power.

I went through a lot of the auto catch mods people have shared before landing on this design, and I learned a ton from them along the way. See [Credit and Thanks](#credit-and-thanks) below.

# Features

- Taps the button for about 100 ms when green comes on, instead of holding it down
- Taps on yellow too, same as green
- Ignores white and cyan, so it won't get stuck in the white light loop
- Ignores red and blue on their own
- Pressing the button yourself works exactly the same as before
- Draws next to nothing when idle, so no extra battery drain
- Doesn't dim or change the LEDs
- Four solder points, and nothing else on the Plus+ gets modified

# How it works

<p align="center">
  <img src="images/schematic.svg" alt="Green Thumb schematic" width="760">
</p>

The flex taps the green and blue control lines on the LED board. These are the points that turn each colour on, and they sit at about 0.8 V when that colour is lit and 0 V when it's off.

- **Q1** is a digital transistor (a transistor with its resistors built in). It pulls the button line to ground, which is the same thing that happens when you press the button.
- **C1** turns the green signal into a short pulse, so Q1 only taps the button for about 100 ms no matter how long green stays on.
- **R1** resets C1 after each tap so it's ready for the next one.
- **Q2** watches blue. Whenever blue is on, it holds Q1 off, so white and cyan can't press the button.
- **R2** sits between C1 and Q1 so that blocking white doesn't drag the green LED down with it. Without it, white turns magenta.

The full breakdown is in [docs/how-it-works.md](docs/how-it-works.md).

# The white light loop

If you press the button while the Plus+ is disconnected, it searches for your phone, blinks white nine times, then stops. White is red, green and blue all on at once, so every one of those blinks also turns on green. A mod that only watches green presses the button on every blink, every press restarts the search, and you end up with white blinking until you put it to sleep, power cycle it, or connect it.

<p align="center">
  <img src="images/white-loop.svg" alt="The white light loop: a green only mod versus Green Thumb" width="760">
</p>

I ran into this myself while building Green Thumb. Without the blue check, it looped. With its ground lifted, the Plus+ went back to nine blinks and stopped. Adding the blue check fixed it, since white has blue in it and the green catch state doesn't.

You'll see people say a diode or an optocoupler fixes it. On some units it seems to, but that's most likely because the part is right on the edge of turning on, not because it can actually tell white from green. More on that in [docs/white-loop.md](docs/white-loop.md).

# How it compares to other mods

<p align="center">
  <img src="images/timing.svg" alt="What the button line sees under each mod when green turns on" width="700">
</p>

Most of the other switchless mods hold the button down for as long as green is lit. That works, but if green stays on long enough the Plus+ can treat it as a long press, and most of them still loop on white. The single wire mod (with the LED left on the board) is the opposite: it holds the button down whenever green is *off*, which can get the Plus+ stuck and drains the battery. The vibration based mods only press while the Plus+ is sending its vibration signal, which cuts down on the white loop, but they can still press if white happens during a vibration.

Green Thumb taps instead of holding, and it ignores white.

**Please note: this isn't a complete list of mods. There are also mods that use switches, and I'm only comparing switchless mods here.**

The full comparison, with each mod's wiring and the problems people have run into, is in [docs/mod-comparison.md](docs/mod-comparison.md).

# Installing

Solder the four pads on the flex to their matching points on the LED board: GREEN, BLUE, BTN and GND. That's it, nothing else on the Plus+ gets modified. There's a quick guide in [docs/install.md](docs/install.md), and [docs/testing.md](docs/testing.md) has a few checks to make sure it's working.

# Docs

- [How it works](docs/how-it-works.md)
- [The white light loop](docs/white-loop.md)
- [Mod comparison](docs/mod-comparison.md)
- [Install](docs/install.md) and [testing](docs/testing.md)
- [FAQ](docs/faq.md)

# Disclaimer

This mod involves soldering to a small, battery powered device. If you don't have much experience with fine soldering, you can damage your Plus+, and it will void any warranty. I recommend practicing first if you're not comfortable with it.

I've tested Green Thumb on my own Plus+, but I can't guarantee it'll work the same on every unit. Some people have measured weaker green signals on their boards than I have on mine, so results can vary. I'm not obligated to help with troubleshooting and will only do so at my discretion, but if you run into an actual issue or have a suggestion to improve it, please reach out.

This project isn't affiliated with or endorsed by Nintendo, The Pokémon Company, or Niantic. Other people's mods are described from their public posts for comparison, and the credit for those designs belongs to them.

# Credit and Thanks

Huge thanks to the people in the community who shared their mods and findings. A lot of what's in here came from reading their posts and testing their ideas:

- u/spacefrog_feds for sharing the diode and LED pad mods and the notes that came with them
- u/PaperStckz for sharing a two MOSFET build with a blue inhibit
- vulp_vibes for the vibration based approach
- u/yurikaka for checking that a press during the white blinks resets the count, which helped pin down what the loop actually is
- Everyone who posted their measurements and photorelay testing

# Support

If Green Thumb saved you some button presses, you can buy me a coffee:

[![Support me on Ko-fi](https://ko-fi.com/img/githubbutton_sm.svg)](https://ko-fi.com/jackvmakes)

# License

**Please note that I DO ALLOW devices with Green Thumb installed to be sold, and offering installation as a service is fine too. Selling the board on its own, as a kit, or as a bare PCB is strictly prohibited.**

Green Thumb is released under the Green Thumb Hardware License v1.0. The design files, bill of materials and assembly details aren't published. See [LICENSE.md](LICENSE.md) for the full terms.
