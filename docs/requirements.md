# Requirements

To use RadioConsole2, you'll need to have some kind of radio resource to communicate with. This can be a physical radio 
(controllable through the rc2 `daemon` application), or a [DVMProject FNE](https://github.com/DVMProject/dvmhost) (controllable
via the [`rc2-dvm`](https://github.com/W3AXL/rc2-dvm)) application.

## Radio Hardware Support

### Motorola

The following radios have been confirmed to work with the RC2 `daemon`:

- Motorola SB9600 Radios
    - Astro Spectra W9 Head
    - MCS2000 M3 Head
    - XTL5000 W9 Head

[Configuration Examples](config/daemon-config.md#motorola-sb9600)

The following radios are planned to be supported but are not yet fully implemented:

- Motorola Serial XMP Radios
    - XTL1500/2500/5000
- Motorola USB XCMP Radios
    - APX x500 series mobiles
    - XPR4x00/5x00 series mobiles

### Generic Radio Control Support

#### C-Media CM108/109/119 GPIO Control (also known as 4-wire E&M)

**NOTE: This control mode is currently under development**

RC2 supports control of any radio via the standard 4-wire E&M control mode, using CM108/109/119 USB soundcard radio interface devices.
In this mode, any radio can be interfaced to the console as long as TX audio, RX audio, and PTT can be accessed.

Configuration Examples TBD

#### Tone-Remote Control (TRC)

**NOTE: This control mode is currently under development**

RC2 supports control of remote radios via the [EIA-standard tone remote signalling system](https://en.wikipedia.org/wiki/Tone_remote). 
In this mode, tone remote *terminators* (endpoints) can be controlled using in-band function tones.

Configuration Examples TBD

## RC2-DVM

[RC2-DVM](https://github.com/W3AXL/rc2-dvm) is an application designed to allow for interfacing of the RC2 console to a DVMProject FNE (Fixed Network Equipment) network core.
This allows for direct communication with P25 & DMR radio networks without requiring a hardware radio. More information and configuration instructions can be found at the
linked Github repository.