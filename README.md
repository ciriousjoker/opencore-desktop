# opencore-desktop

This is my OpenCore setup for Ryzen 5 2600, ASRock B450 Pro4 with onboard LAN & audio. It runs Mac OS 11.1 Big Sur and is based on OpenCore 0.6.4.

# !! REPLACE YOUR PLATFORMINFO BEFORE YOU USE THIS. REPLACE \_\_PLACEHOLDER\_\_!!

## Hardware

- **CPU:** AMD Ryzen 5 2600
- **Mainboard:** [ASRock B450 Pro4](https://www.asrock.com/mb/AMD/B450%20Pro4/)
- **Audio:** Realtek ALC892 (also a Creative Soundblaster Z, but see [Troubleshooting](#Troubleshooting) for info on that)
- **Storage:** Samsung SSD EVO 840 (SATA) & Samsung SSD 970 EVO Plus (NVMe)
- **GPU:** AMD RX 580

## What's working

**Everything except:**

- Power management
- Sound card: SoundBlaster Z (see [Troubleshooting](#Troubleshooting))
- Booting Windows from inside OpenCore (BSOD on startup)

## Explanation

- **Drivers/Tools/Resources/Bootstrap:** As explained in the Getting Started guide. Nothing special here.
- **ACPI:**
  - **SSDT-EC.aml:** Has to be generated on your machine using SSDTTime. You will hang at [BSSTART] without it
  - **SSDT-USBX.aml:** I think I used a prebuilt here, although I'm not sure, probably from the official prebuilts mentioned in the Getting Started guide
  - **SSDT-XHC.aml:** I have no idea where I got this from, sorry. It's definitely not from [ryzen-catalina](https://github.com/alkalim/ryzen-catalina/) or [ryzenmacpro](https://github.com/aluveitie/RyzenMacPro). You might not need this, but I guess you most likely run into USB 3 issues without it.
- **Kexts:**
  - **AMD-USB-Map.kext:** My system hung at "IOUSBHostInterface deferred" or smth like that without it (potentially related to SSDT-XHC.aml). Sorry, I don't know where I got this from, I think some dude from Reddit posted it and it worked. Maybe there's an "official" build of this, but I won't touch this unless something breaks.
  - **AppleALC.kext:** I use alcid=2 and it seems to work fine (see [Troubleshooting](#Troubleshooting) for more info on audio)
  - **AppleMCEReporterDisabler.kext:** Not sure if this is absolutely necessary, but it didn't do any harm
  - **Lilu.kext:** Required.
  - **NVMeFix.kext:** Haven't checked if it's absolutely necessary, but my NVME SSD works out of the box (check if you have the latest firmware!)
  - **RealtekRTL8111:** For onboard LAN with Realtek Gigabit LAN
  - **VirtualSMC:** Required.
  - **WhateverGreen:** Required.
- **config.plist:**
  - I set language code to de:3 (German) because MacOS kept resetting itself to an ANSI keyboard (had en_US:0 there first)
  - I disabled a few debugging flags to speedup boot

## Troubleshooting

- This is a release build with debugging disabled. As a first step, replace `config.plist` with `config_debug.plist` to see log output
- If that doesn't spit out useful info, switch to the debug branch to get the debug binaries as well

---

- I used OpenCore release to speed up booting (kexts are still debug), so if you run into issues, switch to OpenCore debug first
- System hangs at [BSSTART] (or smth like that): Properly generated EC.aml was missing
- System hangs at "IOUSBHostInterface" (or smth like that): AMD-USB-Map.kext and possibly SSDT-XHC.aml was missing
- Disconnect other sound cards (I almost went crazy until I found this): https://github.com/acidanthera/bugtracker/issues/1000
- How to get 5.1 audio working:
  - Create new device in Audio-Midi-Setup.app that connects the 3 lines into a single device
- Mac OS keeps opening the DVD drive on boot. Unplug the DVD drive. Seriously this drove me nuts.

### Debugging Tools

- DCPIManager
- IORegistryExplorer
- SSDTTime
