# remote-dj-b2b

Documentation and tools for enabling remote DJs to perform back to back from different locations, distanced miles (or kilometers) away from each other, with the performance streamed over the Internet

## Software

* [Parsec](https://parsecgaming.com/) - Low latency screen sharing and control tool designed for gaming
* [Voicemeeter Banana](https://vb-audio.com/Voicemeeter/banana.htm) - Software audio mixer with multi-device, ASIO, and low-latency network audio (VBAN)
* [VirtualHere](https://www.virtualhere.com/) - Present USB devices remotely over a network
* [Zerotier](https://www.zerotier.com/) - Low latency peer-to-peer VPN
* [Virtual DJ](https://www.virtualdj.com/) - DJ software, in theory any software which can handle multiple USB controllers will work.
* [OBS Studio](https://obsproject.com/) - Live stream broadcasting

## Hardware

* Mixing computer: GPU-enabled (preferably with an NVidia Tesla GPU) machine for DJ software and OBS Studio, to be controlled remotely in common by the DJs
  * Cloud machines from [Paperspace](https://www.paperspace.com/) and [Google Cloud](https://console.cloud.google.com/marketplace/product/nvidia/nvidia-gaming-windows-server-2019) have been tested.
  * Paperspace has a Parsec template which was by far the easiest to set up, however has more limited regional availability.
  * In theory, NVidia cloud gaming templates for AWS and Azure should also work.
  * In theory, one of the DJ's local computers, if powerful enough and with sufficient broadband quality, could function as this machine
* Local computers for each DJ
* USB DJ controllers for each DJ

## Theory of Operation

1. Shared display and mouse/keyboard control of the mixing computer using Parsec. DJs will primarily use their controllers and only use the viewing capabilities for song selection and audio displays.
1. Master and headphone outputs to virtual ASIO devices in Voicemeeter. Voicemeeter can then route the audio out over VBAN to the DJs. DJs must also be running Voicemeeter to receive the audio streams and then route them to local audio devices. DJs with multiple audio interfaces can route master and headphone audio to separate outputs.
1. VirtualHere Server will publish local DJ USB devices to the mixing computer, which will use VirtualHere client to connect the DJ controllers
1. ZeroTier provides a virtual local network for VBAN and VirtualHere to reduce the need to set up complex firewall and NAT routing between DJs
1. DJ software needs to provide a way for multiple controllers to control the same decks.

## Observations

* With the mixing computer and all DJs within 50 miles, DJs reported that Slicer mode was challenging but usable
* With the mixing computer and DJ approximately 800 miles away, DJs reported that beatmatching and EQ/fader transitions were quite usable, but Hot Cues were possibly too hard.

## Tuning

### Virtual DJ

`controllerTakeoverMode` should be set to `Pickup`. This means that a controller will need to me moved to the current position displayed in software to "pick up" a control, before the control can be changed. A shadow knob/fader will appear in the UI to show that a control is being moved on a controller which has not yet picked up the relevant control in software.

Headphone cue controls vary from deck to deck. Software-centric controllers like the DDJ-WeGO and XDJ-R1 (and most likely the DDJ-200 and DDJ-400) have the headphone cue buttons mapped to software and will function normally. More advanced controllers like the DDJ-SZ do headphone cueing in the internal mixer and the buttons on the deck can only control the internal mixer, which is not used here. In this case you will need to MIDI map a different button to perform the headphone cue function. In in Virtual DJ, this is done by mapping the desired MIDI control to `deck 1 pfl` or `deck 2 pfl`.

### Voicemeeter

Some tuning is required across all of the computers involved.
* At the Windows level, all audio devices should be set to 48000 Hz sample rate.
* Voicemeeter WDM and ASIO buffers should be tested and set to as low as possible while still being stable (usually 256 or 320). 
* WDM Input Exclusive Mode should be set to Yes
* Engine mode should be Swift
* Preferred Sample Rate should be 48000
