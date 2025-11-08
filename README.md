# Passive A/V example circuit for Raspberry Pi 500

This simple circuit demonstrates composite video and audio out from Raspberry Pi 5 series computers, using only passive components (it cannot produce composite video from Raspberry Pi 400 or earlier models).  Video and audio quality are limited. There may be crosstalk between them, and power supply noise. The circuit is nevertheless useful for less-demanding applications.

This is not an official product. It has not undergone regulatory testing. But you can make and adapt it.

![Rendered image of PCB](images/populated.jpg)

The board was designed using KiCad 9.0. Production files were generated using the JLCPCB plugin. They are provided without any warranty of correctness or manufacturability.

## Quick instructions

 * Ensure you can log in remotely, in case you end up with no display.
 * `dtoverlay=vc4-drm-kms-v3d,composite` (or use `raspi-config` to enable composite).
 * `dtoverlay=vec-gpio-pi5,gpio27` (this is a new overlay, not yet released).
 * `dtoverlay=audremap,pins_12_13`
 * `power_force_3v3_pwm=1`
 * Optionally, set a video mode in `cmdline.txt` (the default will be 704x432i NTSC).
 * Power off before connecting anything.
 * Connect to Raspberry Pi 500 or 500+, ensuring connector is centrally aligned and covers all header pins! Component side up.
 * or install vertically on a Raspberry Pi 5 or CM5IO header, with component side facing towards the Raspberry Pi's HDMI ports.
 * To get both audio and video through the jack socket, connect a jumper between VIDEO and SLEEVE pins.
 * Unplug HDMI before booting with composite video enabled.

No picture? You might not have the new overlay. Type this (after Ctrl-Alt-T if using the GUI):

```
   pinctrl 4-11,27 a1
```

## Circuit notes

Here is the [schematic](images/schematic.pdf).

### Video

On the RP1 chip, three copies of an 8-bit signal from the VEC (video encoder) can be brought out to GPIO pins 4-27, with an optional clock on GPIO 0 (rather like DPI). It is usually clocked at 108 MHz.

Here we use a simple 7-bit resistor DAC. To improve linearity, the MSB is driven by two separate GPIOs (11 and 27).

Resistor values were chosen from JLCPCB's "Basic" range (mostly E12) to approximate the sequence (490 x 2<sup><i>n</i></sup>) - 20 &Omega;. More precise values for R5 and R7 might be 1.96 k&Omega;, 7.87 k&Omega;. All resistors should be 1%.

The output is DC-coupled with an impedance of about 125 &Omega;. Peak output is about 1.2 V into a 75 &Omega; load.

### Audio

This is a variant of the PWM filter used in some Raspberry Pi models, omitting the regulator and buffer for simplicity. Although intended for use with a TV or amplifier, it can (quietly) drive headphones with impedance &ge; 32 &Omega;.

C1, C3 are unpolarized; 10% tolerance and 10V rating are fine.

### Passive components and assembly

Only SMD passive components are listed in the [BOM](production/bom.csv) file.

If using JLCPCB assembly, select "user specified tooling holes" (or they might drill extra ones). There will be a warning that J1-J4 are missing from the BOM &mdash; this is expected.

I've used "Hand Solder" footprints (with longer pads) but they should work with automated assembly too. Consider reverting to normal footprints. Conversely, for easier hand soldering, the layout is spacious enough to accommodate 1206 footprints without major changes.

### Connectors

J2 is Same Sky RCJ-014, available from DigiKey. Other sockets exist with similar footprints.

J4 is Tensility 54-00174. There are other similar-shaped TRRS sockets (some bending of pins may be required).

J3 is a 5-pin header, for testing and to make the video->J4 connection optional (otherwise, headphones could short the video signal). You could hard-wire a link here to omit J2, J3 and use only J4.

The unusual choice of a right-angle socket for J1 was made with Raspberry Pi 500 in mind. Do not fit a socket on the underside! You would have to change the layout to adapt the board to HAT form factor.

### A note on missing footprints

The repository does not include any footprint or symbol libraries. Footprints for J2 and J4 are available from SnapMagic Search, Inc.

Note that I **changed their pin numbers** to match KiCad's symbols, trimmed silkscreen at board edges, and modified KiCad's `Raspberry_Pi_2_3` symbol to separate the GND pins.

## Version history

**2.0** First published version. Added tooling holes. Tested, works well.

**2.1** Add ground plane vias. Increase audio resistors, to reduce GPIO peak current (slightly reducing audio level). Move J2 slightly.
