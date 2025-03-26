# Radio Daemon Configuration

The radio interface daemon (also known as `rc2-daemon`) is the bridge between the frontend console client and radio *resources*. Radio resources can be
as simple as a USB soundcard connected to the speaker output of a scanner, or as complex as tone-remote base stations hundreds of miles away.

All daemon configuration is done via a `config.yml` file. Included in the repository and downloaded releases is a `config.example.yml`, which provides a
template to base your configuration off of.

## Common Configuration

There are several sections common to all radio control modes:

### Basic Information

These configuration items are used to connect to the daemon from the console client. The name and description will automatically update the 
name and description shown on the card for this radio in the client.

```yaml
# Base radio configuration parameters
daemon:
    # Name of this radio daemon (shown in console radio card header)
    name: "Radio"
    # Description of this radio daemon (shown on hover over name in console)
    desc: "RadioConsole2 Radio Daemon"
    # Listen address for this radio daemon
    listenAddress: 0.0.0.0
    # Listen port
    listenPort: 8801
```

### Audio Configuration

To obtain transmit and receive audio from an attached radio, you'll need a soundcard with speaker output and microphone input. Once your device 
is connected to the PC running the daemon, you can run `./daemon list-audio` and a list of valid audio device names will be displayed.

```yaml
# Audio settings for radio TX/RX audio
# Run `daemon list-audio` to get valid names for tx/rx devices
audio:
    # TX audio device (speaker)
    txDevice: "C-Media USB Headphone Set, USB Audio"
    # RX audio device (microphone)
    rxDevice: "C-Media USB Headphone Set, USB Audio"
```

### Softkeys

RC2 uses the concept of *softkeys* for controlling connected radios. When a radio is selected, the RC2 console client displays a row of 6 softkeys
at the bottom of the screen. Each button can be mapped to a specific softkey *function*, and multiple pages of softkeys can be scrolled using the
left and right arrows.

![](../media/softkey-bar.png)

Softkey *state* is indicated by a darkened button with brighter text. Softkey states are indicated by the RC2 daemon depending on current channel/radio status.

Valid softkeys are listed below, with comments explaining their function. These softkey names are loosely based off of the Motorola XTL-series softkey
names and perform similar functions.

```yaml
# List of softkeys shown for this radio in the console client
softkeys:
    - MON   # Monitor mode, disables CTCSS/DCS tone squelch
    - DEL   # Scan nuisance delete, temporarily removes the currently active channel from the scan list
    - LPWR  # Low power mode, reduces radio transmit power
    - SCAN  # Scan enable/disable
    - DIR   # Direct/Talkaround mode, configures radio to transmit on the repeater output frequency
    - HOME  # Home mode, reset softkey menu to first page and/or reset radio to configured home channel
    - CALL  # Unit call
    - PAGE  # Radio page
    - TGRP  # Talkgroup select
    - SEC   # Secure/Clear mode
    - RCL   # Recall, recalls the last scanlist member during scanlist editing mode
    - SEL   # Select
```

### Text Lookups

On some radios, available text for zone & channel display can be severely limited (some radios only allow for 8 total characters). In this instance, the RC2 daemon allows for
*text lookups*, which can translate short character strings into much longer zone & channel text.

For example, assume a radio indicates that it's on the first zone and channel by displaying `Z1 CHAN1` on the screen. The text lookups in the example configuration below would
translate these to the longer text strings of `Zone 1` and `Channel 1` respectively. You can configure as many text lookups as needed to properly describe your radio's configured
channels.

**Note:** Using a blank match field (`match: ""`) will cause that text lookup to *always match*, meaning the associated `replace` text will always be displayed on the console client.

```yaml
# Text lookups for Zone/Channel text replacement with single-line displays
textLookups:
    # Zone text lookups
    zone:
        # Lookups consist of multiple list entries as follows:
      - match: "Z1"       # Text matching "Z1" will replace the radio zone name
        replace: "Zone 1" # with the text "Zone 1"
        # Here's a second zone lookup entry
      - match: "Z2"
        replace: "Zone 2"
    # Channel text lookups
    channel:
        # Same thing for channel text
      - match: "CHAN1"
        replace: "Channel 1"
        # And a second
      - match: "CHAN2"
        replace: "Channel 2"
```

### Control

The control section configures how the RC2 daemon will control the attached radio. There are different sections depending on which control mode is selected,
but every daemon configuration must specify the control mode being used.

```yaml
control:
    # Control Mode
    #
    #   0 - VOX (control of TX/RX states is based on audio levels only) [Not Yet Implemented]
    #   1 - TRC (Tone remote control based on EIA tone signalling) [Not Yet Implemented]
    #   2 - SB9600 (emulation of Motorola Astro W-series and MCS2000 model-3 control heads over SB9600)
    #   3 - XCMP Serial (control of Motorola XTL radios via serial)
    #   4 - XCMP USB (control of Motorola XPR and APX radios via USB)
    controlMode: 2
```

For more information on configuring each control mode, keep reading.