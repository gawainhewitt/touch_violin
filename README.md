# Touch Violin

![The Touch Violin with a man wearing a cap behind it pointing with both fingers](images/241127_J-TYE_LPO_10_OrchLab-Festival-Day-2024_4783-lo-res-name-removed.jpg)

![The Touch Violin](images/DSC00579_small.jpeg)

An accessible electronic instrument built inside a real violin, developed by [**Gawain Hewitt**](https://gawainhewitt.co.uk) for [**OrchLab**](https://orchlab.org/) — a long-term partnership between the [**London Philharmonic Orchestra**](https://lpo.org.uk/project/orchlab/) and [**Drake Music**](https://www.drakemusic.org/music/projects-commissions-participation/orchlab/) that creates accessible instruments and music-making opportunities for disabled adults.

The Touch Violin gives a meaningful, expressive violin-playing experience to people with a wide range of physical impairments, without requiring the fine motor control needed to play a conventional violin.

## How it works

- **Fingerboard** – Capacitive touch sensors on the fingerboard trigger notes, so a light touch or even a hovering finger (proximity) can play a note.
- **Bow wheel** – A textured wheel on the body can be turned with a genuine violin bow (or a hand, or any object with some grip). An optical/mechanical encoder tracks its speed and direction, mapping bowing speed to volume in real time.
- **Sound** – There is no speaker. An exciter fixed to the body makes the violin's own wood resonate, so the instrument itself produces the sound — a wavetable violin sample played through the wood, just like an acoustic violin projects sound.
- **Side menu** – A small OLED display and joystick let the player (or a facilitator) change key, scale, playing mode, and volume on the fly.
- **Mounting** – Rubber feet let it sit securely on a table, and a tripod thread on the underside lets it mount on a camera tripod or an articulated arm (e.g. a Manfrotto arm) for a range of accessible playing positions.
- **Power** – Runs on standard AA batteries, so it's fully portable and cordless, just like an acoustic violin.

### Playing modes

The instrument supports three playing modes, selectable from the menu:

| Mode | Behaviour |
|---|---|
| **Touch** | Touching a fingerboard sensor plays a note directly; releasing it stops the note. |
| **Bowing** | Turning the wheel controls volume/expression on its own — good for one-handed bow-only play. |
| **Both** | Combined technique: holding a fingerboard note *and* bowing the wheel together controls both pitch and, via bowing speed, volume/velocity — closer to how an acoustic violin is played. |

## Hardware

- **Teensy 4.0** microcontroller (Arduino framework, built with [PlatformIO](https://platformio.org/))
- **MPR121** capacitive touch controller for the fingerboard sensors
- **Rotary encoder** on the bow wheel for bowing direction/speed
- **Analogue joystick** + centre push-button for menu navigation
- **SSD1306 128×64 I2C OLED display** (via a custom `u8g2` bus adaptation) for the on-instrument menu
- **Teensy Audio Shield (SGTL5000)** driving a bone-conduction style exciter mounted to the violin body (no conventional speaker)

## Software architecture

The firmware is a single PlatformIO/Arduino project (`main.cpp`) split into focused header modules:

| File | Responsibility |
|---|---|
| `main.cpp` | Setup/loop, ties all modules together, dispatches touch/bowing events based on the current mode |
| `constants.h` | Global pin numbers and constants (reboot button, volume pin, sensor count, polyphony) |
| `touch.h` | MPR121 capacitive touch sensor initialisation and configuration |
| `wavetable.h` | Audio engine setup (Teensy Audio library graph: wavetable synth → envelope → reverb → mixer → amps → I2S out), note frequency table, scales (major, minor, pentatonic, major/minor blues), and note on/off logic |
| `Violin_samples.h` / `Violin_samples.cpp` | Wavetable violin instrument sample data used by the synth engine |
| `encoderFile.h` | Bow wheel encoder reading, direction/speed detection, and volume ramping for the "Bowing" mode |
| `joystick.h` | Joystick and button reading/debouncing, and menu navigation logic (key, scale, function, volume) |
| `drawMenu.h` | Renders the current key, scale, playing mode, and volume to the OLED display |
| `reboot.h` | Soft-reboots the Teensy when the reboot button is pressed |

### Main loop overview

On each pass through `loop()`, the firmware:

1. Reads the joystick/buttons and updates the menu (key, scale, mode, volume).
2. Polls the MPR121 for new touches/releases on the fingerboard.
3. Depending on the current mode (Touch / Bowing / Both), plays or stops notes and/or reads the bow wheel and ramps the volume accordingly.
4. Redraws the display if anything has changed.
5. Checks the reboot button.

## Building and flashing

This project uses [PlatformIO](https://platformio.org/) targeting a **Teensy 4.0** board.

```bash
# Build
pio run

# Build and upload to a connected Teensy 4.0
pio run --target upload

# Open the serial monitor (115200 baud)
pio device monitor
```

Dependencies (declared in `platformio.ini`) are pulled in automatically by PlatformIO and include `Wire`, `SPI`, and `SerialFlash`, alongside the Teensy Audio, `u8g2`, `Encoder`, and Bare Conductive MPR121 libraries used by the header modules above.

## About OrchLab

[**OrchLab**](https://orchlab.org/) supports music-making and music appreciation with disabled adults through workshops, accessible instruments and technology, bespoke web activities, training, and events. It is run by the [**London Philharmonic Orchestra**](https://lpo.org.uk/project/orchlab/) in partnership with [**Drake Music**](https://www.drakemusic.org/music/projects-commissions-participation/orchlab/), and each year develops a brand-new accessible instrument in response to the access needs of its Community Partner participants. The Touch Violin is one of these instruments in the wider [**OrchLab Touch Instruments**](https://orchlab.org/meet-the-orchlab-touch-instruments/) series.

## Author

Built by **Gawain Hewitt** — [gawainhewitt.co.uk](https://gawainhewitt.co.uk) · [github.com/gawainhewitt](https://github.com/gawainhewitt)

## License

MIT License — see [LICENSE](./LICENSE) for details.