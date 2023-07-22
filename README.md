![](../../workflows/gds/badge.svg) ![](../../workflows/docs/badge.svg) ![](../../workflows/wokwi_test/badge.svg) [![License is Apache-2.0](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)

> Note: This is a repo for Tiny Tapeout 4 (TT04).
> It is essentially a fork of the template:
> [tt04-submission-template](https://github.com/TinyTapeout/tt04-submission-template).

# "Solo Squash" TT04 submission

![Solo Squash silicon layout and simulation](./doc/solo_squash_tt04_header.png)

This is a minimalistic VGA "game" implemented in Verilog as a 1x1 "tile" submission to the
Tiny Tapeout 4 (TT04) Caravel project, to be made into an ASIC via the chipIgnite 2309 shuttle.

The design itself is digital logic that implements a 640x480 VGA driver that allows one person to play
a super simple Pong-like game of bouncing a ball off a paddle and around 3 walls.

**A much more detailed run-down of the design is found in the [`solo_squash` repo](https://github.com/algofoogle/solo_squash).**

For more info about my previous Tiny Tapeout submission attempts including a discussion of a bit of the
testing and local build process, see:
*   [`anton1-tt03`](https://github.com/algofoogle/anton1-tt03)
*   [`tt03p5-solo-squash`](https://github.com/algofoogle/tt03p5-solo-squash)

**NOTE:** There is an extra feature in this design which is just an experiment: I've included a
leading zero counter with a 24-bit input that comes from the concatention of these internal
signals of the solo_squash module: `{offset[3:0],v,h}` -- It outputs the count
(from 0 to 24) on `uio_out[4:0]`, and also sets `uio_out[5]` if the count is 24
(i.e. "all zeroes" signal).


## How it works

### What is Solo Squash?

A 25MHz clock drives a simple 640x480 VGA generator, and in turn generates a simple "game"
that displays a bouncing ball within a playfield of 3 walls, with the 4th side open except
for a lone paddle that you as the player control. Pong? What's that?
Sounds too new-fangled for me!


### What's the objective of the game?

Keep the ball bouncing in the playfield for as long as you can. Sometimes it's harder than it sounds!
There is no score, and no other objective. It's educational, and offers mild thrills,
just like Edna.

One button moves the paddle up, the other down, at a constant rate.

Bounces off the walls produce a simple tone, and off the paddle produce a slightly different tone.
Letting the ball escape will humiliatingly drone on for a bit before the ball returns to play.

A hint: Let the drifting blue background pattern be your guide for where the ball will
ultimately end up as it bounces around.


### How does the design work?

Tones produced on `speaker` are derived from divisions (bits) of the vertical scan register.

Rendering of the walls, ball, and paddle are done using registers (more-so than combo logic)
that turn the VGA red/green channels on and off at the right times as the VGA beam marches on.
The blue background pattern is done with bitwise logic from the horizontal and vertical scan
counters, with another counter to create the drifting effect by applying an offset.

Detecting when a bounce happens is done by detecting if any pixel we're rendering coexists
in the ball and wall (or paddle). Long tone and ball return is actually just an accidental
side effect of counters rolling over, but that's good enough for me!


## How to test

### Testing the ASIC

Well, the ASIC doesn't exist yet, but when it does, follow Tiny Tapeout guides and...

Assuming ~3.3V outputs from the ASIC, put a 270&ohm; resistor on each of `red`, `green`, and
`blue` and then connect those to the respective VGA input signals.
This should give you close enough to the 0.7V standard VGA full-brightness signal for each of the
colour channels (i.e. by voltage-dividing with your VGA display's internal 75&ohm; impedance).

Also connect the `hsync` and `vsync` lines to their respective VGA inputs; I feel comfortable
using an extra 100&ohm; resistor on those, too, just to help in case there are accidental shorts.

Connect each of the 4 inputs *through* pushbuttons to high (VCC). The ASIC's internal pull-downs
should be enabled by default, meaning the (active-high) signals are **low** by default
(in the buttons' normally-open state), and get pulled **high** when pressed.

Optionally connect something for sound to the `speaker` output: e.g. a piezo speaker
(or ear-phone speaker) in series with a 1k&ohm; (or 670&ohm; minimum) current limiting resistor
to ground... or an "aux cord" to a line-in amplifier.

Supply about a 25MHz clock, and hopefully the design should run immediately as is, producing
a VGA display, but you can also assert reset if you want to start at a known state:
i.e. VGA scan immediately starting from the top-left pixel of the display, with the first
two full lines rendered hopefully yellow (i.e. `red` and `green` channels both asserted,
but not `blue`).

### Testing by Verilator simulation

For more info on this, see [Visual simulation with Verilator](https://github.com/algofoogle/solo_squash#visual-simulation-with-verilator).


# What is Tiny Tapeout?

TinyTapeout is an educational project that aims to make it easier and cheaper than ever to get your digital designs manufactured on a real chip!

Go to https://tinytapeout.com for instructions!

## Resources

* [FAQ](https://tinytapeout.com/faq/)
* [Digital design lessons](https://tinytapeout.com/digital_design/)
* [Learn how semiconductors work](https://tinytapeout.com/siliwiz/)
* [Join the community](https://discord.gg/rPK2nSjxy8)

## What next?

* Share your GDS on Twitter, tag it [#tinytapeout](https://twitter.com/hashtag/tinytapeout?src=hashtag_click) and [link me](https://twitter.com/matthewvenn)!
