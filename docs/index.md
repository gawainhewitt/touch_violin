---
title: Home
---

# How to Build a Touch Violin

The Touch Violin is an accessible instrument built into a real violin: capacitive touch sensors on the fingerboard play notes, a textured wheel — turned by a real bow — controls volume, a small menu system on the side lets you switch modes, and an exciter glued to the body vibrates the violin itself as the speaker (no separate speaker). It runs on AA batteries, and has a tripod mount and table feet.

It was built by Gawain Hewitt for [OrchLab](#about-orchlab), a collaboration between the London Philharmonic Orchestra and Drake Music in 2024–2025.

This page is a warts-and-all build guide. It's a bespoke, one-off instrument that was designed as it was built rather than from a finished plan, so treat this as a detailed account of one build rather than a strict spec — you'll need to adapt some steps to your own violin and parts.

<img src="assets/images/241127_J-TYE_LPO_10_OrchLab-Festival-Day-2024_4783-lo-res-name-removed.jpg" alt="alt text" style="width:100%; max-width:800px; display:block; margin-bottom:1em;">

<img src="assets/images/DSC00579_small.jpeg" alt="alt text" style="width:100%; max-width:800px; display:block; margin-bottom:1em;">

## Watch it first

Before you start, it's worth watching this short video which shows you what the finished instrument is and does:

<div style="position:relative; width:100%; max-width:800px; aspect-ratio:16/9; margin-bottom:1em;">
  <iframe src="https://www.youtube.com/embed/janv5LjWCY4"
    title="Touch Violin video"
    style="width:100%; height:100%; position:absolute; top:0; left:0;"
    frameborder="0"
    allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share"
    allowfullscreen>
  </iframe>
</div>

Once you've seen it, here's how to build your own.

<img src="assets/images/original_sketch.jpg" alt="Original hand-drawn concept sketch of the Touch Violin" style="width:100%; max-width:800px; display:block; margin-bottom:0.3em;">
<p style="margin-top:0; margin-bottom:1em; font-style:italic;">The original concept sketch: a touch strip for bowing, a wheel for bowing (worked with the finger touch strip to "bow" the instrument), embedded rods on the fingerboard for choosing notes, buttons, a screen, a volume control, and a speaker — most of which made it into the final build in some form, with the exciter replacing the separate speaker idea.</p>

## What you'll need

### The violin

A cheap, playable, full-size second-hand violin. This build used one bought second-hand for around £30 — you don't need (and don't want) anything valuable, since you're going to be drilling and modifying it permanently.

<img src="assets/images/violin_untouched.jpg" alt="The violin before any work began" style="width:100%; max-width:800px; display:block; margin-bottom:1em;">

### Electronics

- Teensy 4.0
- Teensy Audio Board (or equivalent audio shield wired directly, as used here)
- MPR121 capacitive touch sensor breakout (12-channel) — this build uses a modified version of the Bare Conductive MPR121 library, `bc-mpr121-bus1`, on the 2nd I2C bus "bus1" as the audio shield uses bus0 and sharing it leads to glitches and audio dropout.
- A rotary encoder (for the bow wheel)
- A logic level shifter (the encoder wants 5V, the Teensy runs at 3.3V)
- A small I2C OLED/LCD display for the menu (driven with a custom `bus1_U8g2lib`, adapted from [u8g2](https://github.com/olikraus/u8g2))
- A joystick module (for menu navigation)
- A small audio amplifier board
- A power distribution board (this build used a couple)
- A [Pololu](https://www.pololu.com/) 5V step-up/regulator board, to give a constant 5V regardless of battery state
- An exciter/transducer (this is what turns the violin body into the speaker — no separate speaker is used)
- 3.5mm jack sockets: one for audio out, and a couple more as breakouts so external sensors can be wired in
- A bespoke PCB breaking out Teensy, Teensy Audio Board and MPR121 connections (see the wiring photos below. I have supplied the gerber files so you can get your own made or you can replicate this with stripboard/perfboard)
- Small offcuts of perfboard, for breaking out I2C bus1 to share between the audio board and the display, and for breaking out spare pins from the underside of the Teensy
- 4x AA batteries and a battery holder (3D printed for this build — see [3D printed parts](#3d-printed-parts))

### Fixings and hardware

- 13 x hex/allen-key bolts sized to fit through the fingerboard into the neck (4 form the electrical + mechanical connection directly; the other 4 use nuts underneath for the electrical connection). The rest are to attach the lid of the violin.
- 9 x brass threaded inserts (the same type used for heat-setting into 3D prints) — see the [fingerboard wiring](#the-fingerboard-and-neck) section for how these are used here, hammered rather than heat-set. Again 5 are used to secure the lid.
- Tripod mount thread (built into the 3D printed wheel-end housing in this build)
- Rubber table feet

### Tools

- A thin tool for opening the violin top (see the video linked below)
- A tool for scoring/marking the fingerboard centreline (a marking gauge or similar)
- A hand drill (or pillar drill) and a wood bit with a centre spike, for accurate starting points
- A sharp chisel (and ideally a curved/round one too, for cleaning grooves)
- A soldering iron
- A 3D printer, ideally one that can print PETG as well as PLA (see below)
- Wire, header pins, heat-shrink, etc. for wiring
- A tripod (optional, for use/display once finished)

*(This list is reconstructed from the build video and photos below — treat it as a strong starting point rather than an exhaustive parts list. Exact model numbers and links to be added.)*

## Opening the violin

This build didn't document the process of taking the top off the violin — instead, here's someone else doing it, which gives you a good sense of what's involved:

<img src="assets/images/opening_the_violin.jpg" alt="Prising the top away from the ribs of the violin" style="width:100%; max-width:800px; display:block; margin-bottom:1em;">

<div style="position:relative; width:100%; max-width:800px; aspect-ratio:16/9; margin-bottom:1em;">
  <iframe src="https://www.youtube.com/embed/DdFS7o22ptM"
    title="How to open a violin"
    style="width:100%; height:100%; position:absolute; top:0; left:0;"
    frameborder="0"
    allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share"
    allowfullscreen>
  </iframe>
</div>

Violin tops are held on with hide glue — an animal glue that's deliberately weaker than the wood, so it fails cleanly and can be cleaned off and reset later (this is why violins can be repaired again and again). Because the glue is brittle and the violin is built to be acoustically resonant, removing the top produces loud, alarming cracks and pops. This is normal. Have a good look at the video above first, then go for it.

<img src="assets/images/the_violin_opened.jpg" alt="The violin opened, top removed, neck and fingerboard still in place" style="width:100%; max-width:800px; display:block; margin-bottom:0.3em;">

<img src="assets/images/violin_top.jpg" alt="The underside of the removed violin top" style="width:100%; max-width:800px; display:block; margin-bottom:1em;">

## Drilling the fingerboard and neck

**This step has to happen before you remove the neck.** The bolts that run through the fingerboard are the capacitive touch sensors for each note, and they need to line up exactly with threaded inserts in the neck underneath. Drilling both while the fingerboard and neck are still assembled together is what guarantees they'll match up — trying to align them separately afterwards would be very difficult.

<img src="assets/images/1_scoring_centre_fingerboard.jpg" alt="Scoring the centre line along the fingerboard with a marking gauge" style="width:100%; max-width:800px; display:block; margin-bottom:0.3em;">
<p style="margin-top:0; margin-bottom:1em; font-style:italic;">Marking the exact centre line of the fingerboard at each sensor position. The fingerboard both flares and curves along its length, so the centre has to be re-checked at every point rather than assumed from one measurement.</p>

<img src="assets/images/1_t_neck_jig.jpg" alt="A simple wooden jig supporting the violin neck" style="width:100%; max-width:800px; display:block; margin-bottom:0.3em;">
<p style="margin-top:0; margin-bottom:1em; font-style:italic;">A simple jig — a block of softwood with a hole drilled in it and the top cut away — made to support the neck while drilling. A lot of downward pressure goes through the neck during this stage, so it's worth protecting the neck-to-body joint while you work.</p>

<img src="assets/images/2_drilling_fingerboard.jpg" alt="Drilling through the scroll end of the fingerboard" style="width:100%; max-width:800px; display:block; margin-bottom:0.3em;">
<p style="margin-top:0; margin-bottom:1em; font-style:italic;">Drilling one of the sensor holes. This build used a hand drill rather than a pillar drill — it worked fine. Use a wood bit with a centre spike so it doesn't wander when you start the hole.</p>

<img src="assets/images/3_drilling_fingerboard2.jpg" alt="Drilling a second hole further down the fingerboard" style="width:100%; max-width:800px; display:block; margin-bottom:1em;">

## Removing the neck and cleaning up

Once all the sensor holes are drilled, remove the neck — a similar process to removing the top, though less dramatic since it's not as acoustically tuned a joint.

<img src="assets/images/4_chisel_and_fingerboard.jpg" alt="A chisel and screwdriver laid next to the removed fingerboard" style="width:100%; max-width:800px; display:block; margin-bottom:0.3em;">
<p style="margin-top:0; margin-bottom:1em; font-style:italic;">With the fingerboard off, both it and the neck need cleaning up with a sharp chisel — mainly leftover fingerboard material on the neck, and old glue residue on the back of the fingerboard.</p>

<img src="assets/images/5_cleaning_neck_with_chisel.jpg" alt="Cleaning up the neck with a chisel" style="width:100%; max-width:800px; display:block; margin-bottom:0.3em;">
<p style="margin-top:0; margin-bottom:1em; font-style:italic;">You don't need this perfectly flat — the fingerboard and neck mate together, so some leftover material can actually help close any gap. Note that the fingerboard isn't glued back on at the end of this build; it's fixed with the sensor bolts instead.</p>

<img src="assets/images/6_stripped_back_violin.jpg" alt="The violin body with the neck and fingerboard removed" style="width:100%; max-width:800px; display:block; margin-bottom:1em;">

## Threaded inserts and wire channels

<img src="assets/images/7_threads_in_neck.jpg" alt="Threaded inserts fitted into the neck" style="width:100%; max-width:800px; display:block; margin-bottom:0.3em;">
<p style="margin-top:0; margin-bottom:1em; font-style:italic;">The small pilot holes from the fingerboard drilling now serve as guides for larger holes in the neck, sized to take brass threaded inserts — the same type normally heat-set into 3D-printed parts. Drill the hole slightly undersized and you can hammer the inserts in directly, without heat. Using threaded inserts (rather than gluing) matters because it keeps the instrument repairable — in keeping with the spirit of a real violin, which is designed to be opened and fixed again and again.</p>

<img src="assets/images/8_wiring_groove_for_neck.jpg" alt="A chiselled channel running down the back of the neck" style="width:100%; max-width:800px; display:block; margin-bottom:0.3em;">
<p style="margin-top:0; margin-bottom:1em; font-style:italic;">Cutting a channel to each threaded insert, to carry wiring down the back of the neck. This was started with a chisel and cleaned up with the end of a drill bit / a curved chisel.</p>

<img src="assets/images/9_wiring_groove_for_neck_2.jpg" alt="The wiring channel cleaned up" style="width:100%; max-width:800px; display:block; margin-bottom:1em;">

## Fitting the sensor bolts

<img src="assets/images/11_fitting_bolt.jpg" alt="A hex-key bolt being fitted into the neck" style="width:100%; max-width:800px; display:block; margin-bottom:0.3em;">
<p style="margin-top:0; margin-bottom:1em; font-style:italic;">Allen-key bolts are used for the sensors, so they sit neatly flush. They screw into the threaded inserts below, giving both a mechanical fixing and an electrical connection for four of the eight sensors; the remaining four use nuts underneath instead, to make their own electrical connection.</p>

<img src="assets/images/12_first_bolt_fixed.jpg" alt="The first bolt fitted through the fingerboard" style="width:100%; max-width:800px; display:block; margin-bottom:0.3em;">

<img src="assets/images/13_fitting_fingerboard_with_bolts.jpg" alt="The fingerboard clamped back onto the neck with the bolts" style="width:100%; max-width:800px; display:block; margin-bottom:1em;">

<img src="assets/images/10_empty_violin.jpg" alt="Gawain holding up the empty violin body, fingerboard and neck reassembled" style="width:100%; max-width:800px; display:block; margin-bottom:1em;">

Drilling holes in the body of the violin to reattach the top, and fitting the 3d printed table feet, both happen around this stage too — see the full walkthrough video at the bottom of this page for more on that.

## The battery holder

<img src="assets/images/14_battery_terminals.jpg" alt="Small metal battery terminal contacts" style="width:100%; max-width:800px; display:block; margin-bottom:0.3em;">
<p style="margin-top:0; margin-bottom:1em; font-style:italic;">The battery contacts used in the 3D-printed battery holder.</p>

<img src="assets/images/15_fitting_battery_holder.jpg" alt="Test-fitting a module against the side of the violin body" style="width:100%; max-width:800px; display:block; margin-bottom:0.3em;">
<p style="margin-top:0; margin-bottom:1em; font-style:italic;">Test-fitting against the curve of the violin's side. Designing a battery holder that actually fits the inside of a violin properly was one of the hardest parts of the whole build — it took the best part of a week of 3D design and printing to get right.</p>

<img src="assets/images/16_battery_holder_in_place.jpg" alt="The finished 3D printed battery holder fitted in the violin, with 4 AA batteries" style="width:100%; max-width:800px; display:block; margin-bottom:1em;">

**Material matters here.** The battery holder needs two different plastics: the outer shell, which doesn't need to flex, can be printed in PLA — but the inner part with the spring-like fingers that holds it in needs to be **PETG**, because PLA is too brittle there and will snap. This build's PETG version has been in daily use for over two years with no issues.

## Wiring the neck and fingerboard

<img src="assets/images/17_neck_wiring.jpg" alt="Wiring running down the back of the neck to the main electronics" style="width:100%; max-width:800px; display:block; margin-bottom:0.3em;">

<img src="assets/images/18_fingerboard_wiring.jpg" alt="Wiring connected to the back of the fingerboard" style="width:100%; max-width:800px; display:block; margin-bottom:1em;">

The four bolts wired directly, and the four using nuts, are all connected up here, running down the channel cut earlier into the body cavity.

## The main electronics: Teensy, audio board and touch sensor

<img src="assets/images/19_top_view_inside.jpg" alt="The main PCB with Teensy, MPR121 and wiring, mounted inside the violin body" style="width:100%; max-width:800px; display:block; margin-bottom:0.3em;">
<p style="margin-top:0; margin-bottom:1em; font-style:italic;">A bespoke PCB (designed for reuse across builds) breaks out the Teensy, a Teensy Audio Board, and an MPR121 12-channel capacitive touch sensor. Along the top edge, wires are soldered directly to the sensor's crocodile-clip prototyping pads. A header roughly halfway along connects several of the sensor channels out to small 3.5mm 4-way breakout jacks, so external sensors can also be plugged in.</p>

<img src="assets/images/20_wiring_under_teensy.jpg" alt="Wiring reaching up to solder pads on the underside of the Teensy" style="width:100%; max-width:800px; display:block; margin-bottom:0.3em;">
<p style="margin-top:0; margin-bottom:1em; font-style:italic;">The Teensy also has solder pads on its underside — fiddly to reach, but useful once you run out of broken-out connections elsewhere on the PCB.</p>

<img src="assets/images/21_close_up_inside.jpg" alt="Close up of the main electronics area with the fingerboard laid alongside" style="width:100%; max-width:800px; display:block; margin-bottom:1em;">

**A note on I2C:** the Teensy has two I2C buses, and one is dedicated to the Audio Board — sharing it, even though the library claims support, audibly messes up the audio. A small offcut of perfboard sits underneath the MPR121 purely to break out I2C so it can be shared with the display instead, working around this. This is also why the firmware for this build uses modified, "bus"-specific versions of the MPR121 and display libraries — see [Firmware and uploading the code](#firmware-and-uploading-the-code) below.

All of the small breakout boards here are mounted on custom 3D-printed standoffs, glued into the violin body.

## The bow wheel and encoder

<img src="assets/images/22_level_shifter.jpg" alt="Close up of the level shifter wired to the MPR121 breakout board" style="width:100%; max-width:800px; display:block; margin-bottom:0.3em;">
<p style="margin-top:0; margin-bottom:1em; font-style:italic;">The rotary encoder that reads the bow wheel runs on 5V, while the Teensy runs at 3.3V, so a logic level shifter sits between them.</p>

The encoder lives in its own custom 3D-printed housing. Its shaft connects into the textured, 3D-printed wheel that you turn with a real bow; the other end of the wheel has a short stub axle that fits into a second 3D-printed part, which doubles up as the tripod mount. There's also a bearing supporting that far end of the wheel.

*(This is the least-documented part of the build — there aren't many photos of the encoder assembly itself yet. More detail and photos of the wheel taken apart are planned for this section.)*

## Putting it all together

<img src="assets/images/23_entire_top_view.jpg" alt="Full top-down view of all the electronics assembled inside the violin body" style="width:100%; max-width:800px; display:block; margin-bottom:0.3em;">
<p style="margin-top:0; margin-bottom:1em; font-style:italic;">Everything assembled: alongside the main PCB, encoder and level shifter already covered above, there's also an audio amplifier, a couple of power distribution boards, and a small Pololu board sitting on top of the battery pack that supplies a constant 5V regardless of how charged the batteries are.</p>

<img src="assets/images/24_top_view_bottom_half.jpg" alt="The lower half of the violin body showing the exciter and amplifier" style="width:100%; max-width:800px; display:block; margin-bottom:1em;">

An exciter is glued to the underside of the violin top — this is what makes the violin body itself act as the speaker. On the opposite side of the body from the battery holder, a separate custom 3D-printed housing holds a joystick and a small screen, which together form the on-instrument menu system.

## 3D printed parts

Several parts of this build are custom 3D prints:

- The battery holder (outer shell in PLA, inner spring-fingered part in **PETG** — see [above](#the-battery-holder))
- The encoder housing
- The bow wheel, and the part at its far end that doubles as the tripod mount
- Standoffs that the various PCBs and breakout boards are glued or screwed to
- The joystick + screen housing on the opposite side of the body from the battery holder

**The STL/print files for these are not in this repository yet — they need adding.** Once they're uploaded (e.g. to a `/3d-files` folder in this repo), link them here.

## Firmware and uploading the code

The firmware is a [PlatformIO](https://platformio.org/) project targeting the Teensy 4.0, using the Arduino framework. The full source is in this repository.

### Libraries used

Most of the framework comes from Teensyduino (the Teensy Audio library, `Wire`, `SPI`, `SD`, `SerialFlash`), plus:

- `Encoder` (Paul Stoffregen) — reads the bow wheel
- `bc-mpr121-bus1` — a modified version of the Bare Conductive MPR121 library, adapted to work on a specific I2C bus (see the [I2C note above](#the-main-electronics-teensy-audio-board-and-touch-sensor)) — this library's source is included directly in this repo under `lib/`, rather than as a submodule
- `MPR121_Datastream`
- `bus1_U8g2lib` — a similarly bus-adapted version of the [u8g2](https://github.com/olikraus/u8g2) display library, used to drive the on-instrument menu screen

### Steps to flash it

1. Install [PlatformIO](https://platformio.org/install) (either the VS Code extension or PlatformIO Core).
2. Install [Teensyduino](https://www.pjrc.com/teensy/teensyduino.html) if PlatformIO doesn't already have Teensy 4.0 support set up on your machine.
3. Clone this repository and open the project folder in PlatformIO.
4. Plug the Teensy 4.0 in over USB.
5. Build and upload — in PlatformIO this is the `teensy40` environment defined in `platformio.ini`.
6. Open the serial monitor at 115200 baud if you want to see debug output.

### How the firmware fits together

- `constants.h` — pin numbers and shared settings (e.g. the reboot button pin, the bow's volume input pin, number of sensors)
- `touch.h` — sets up and reads the MPR121 capacitive touch sensor for the 12 fingerboard sensors
- `encoderFile.h` — reads the rotary encoder for bow position/speed
- `joystick.h` — reads the joystick for menu navigation
- `drawMenu.h` — draws the on-screen menu
- `wavetable.h` / `Violin_samples.h` — the sampled violin sounds and playback engine, built on the Teensy Audio library
- `reboot.h` — handles the reboot button
- `main.cpp` — ties it all together: on startup it initialises the touch sensor, joystick, screen and audio engine; in the main loop it reads the joystick and bow wheel, checks for new touches on the fingerboard, and plays or stops notes accordingly. A `currentFunction` mode switch (set via the menu) decides whether the instrument responds to touch, to bowing, or to both together.

## About OrchLab

The Touch Violin was built for OrchLab, a programme run by the London Philharmonic Orchestra in partnership with Drake Music that supports music-making and music appreciation with disabled adults, through workshops, accessible instruments, training and events. As part of it, new accessible instruments — like this one — are designed and built in direct response to participants' access needs, alongside an annual OrchLab Festival day at the LPO's home at the Southbank Centre.

The London Philharmonic Orchestra is one of the UK's leading orchestras, founded in 1932. Drake Music is a charity, run by disabled and non-disabled people together, focused on making music-making — including building new kinds of instruments — accessible to everyone.

This build was made by Gawain Hewitt for Orchlab 2024–2025.

## See it in action

<div style="position:relative; width:100%; max-width:800px; aspect-ratio:16/9; margin-bottom:1em;">
  <iframe src="https://www.youtube.com/embed/aExMwIFjilw"
    title="Touch Violin Overview"
    style="width:100%; height:100%; position:absolute; top:0; left:0;"
    frameborder="0"
    allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share"
    allowfullscreen>
  </iframe>
</div>

<div style="position:relative; width:100%; max-width:800px; aspect-ratio:16/9; margin-bottom:1em;">
  <iframe src="https://www.youtube.com/embed/Kw9H5wHm3kg"
    title="A close look at the violin"
    style="width:100%; height:100%; position:absolute; top:0; left:0;"
    frameborder="0"
    allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share"
    allowfullscreen>
  </iframe>
</div>

Build your own Touch Violin!