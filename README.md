# nvflashk
[![Github All Releases](https://img.shields.io/github/downloads/notfromstatefarm/nvflash/total.svg)]()

### [Download from the releases page](https://github.com/notfromstatefarm/nvflashk/releases)

[![Flashing a 4090 TUF OC to STRIX BIOS for 1.1v and getting huge gains](https://img.youtube.com/vi/25EarvBrkX8/default.jpg)](https://youtu.be/25EarvBrkX8) [![Flashing a 4090 TUF OC to the Founders Edition BIOS](https://img.youtube.com/vi/ADlVK0HoMkw/default.jpg)](https://youtu.be/ADlVK0HoMkw) [![Flashing a 1.1v 1000W 4090 STRIX XOC BIOS to a 1.07v 4090 TUF OC](https://img.youtube.com/vi/iEZBof0S9dc/default.jpg)](https://youtu.be/iEZBof0S9dc)

This is an early release of nvflashk, a patched version of [nvflash](https://www.techpowerup.com/download/nvidia-nvflash/), nVIDIA's utility for modifying the vBIOS (amongst many other things). It allows you to flash nearly any *signed* BIOS to your GPU.

If you need to reach me for any reason, you can email me at itskefi@proton.me or on Discord @kefinator. I'm also on the [Overclocking discord in the #gpu-overclocking channel](https://discord.gg/overclock). Suggestions for nvflashk are welcome!

# This is only the initial release of the bypass itself so advanced users did not have to wait longer. nvflashk will feature a graphical interface, auto-recovery, BIOS search, and more in the coming weeks, for those who want a safer, more user-friendly way to flash their GPUs.

Preview of nvflashk tool coming by the end of August:
![image](https://github.com/notfromstatefarm/nvflash/assets/86763948/ec5d60b9-f2ef-403e-b9a0-0cd21d30c62f)

> Before using this tool, make sure you back up your factory ROM. **Seriously, do it. You only have to do it once per card. This 10 second process is how you protect yourself against any potential failures, and failures DO happen randomly for absolutely no reason. Back the file up to another computer. Do not lose your factory ROM, you may never find a replacement that you are certain matches perfectly because the installed BIOS is what truly identifies your card. If you have a dual BIOS switch, back them both up. Instructions for doing so are below.** 


The factory nvflash comes with built-in limitations. One of those limitations is that the GPU ID, Board ID, and other IDs of your GPU must match any vBIOS you attempt to flash to it. While the PCI Subsystem ID can sometimes be overridden with `-6`, the internal board ID and others cannot. Essentially, the BIOS had to have been made for your particular PCB and chip combo.

For most users, that limitation makes sense. However, there are those who seek maximum performance from the hardware they buy. Have a 1.07v limited 4 series and want the full 1.1v? Want to flash a 1000W XOC BIOS to one of those 1.07v cards? Planning on slapping an AIB BIOS onto your FE? You'll probably need this tool.

nvflashk enables you to flash almost any nVIDIA BIOS to your GPU. Whether or not it will actually work is a different story, but you should always be able to flash back even if your power cuts out mid-flash. The GPU will either go into a fallback mode with generic display drivers, or you'll simply have to reflash using your iGPU, the secondary BIOS on your GPU if you have a dual BIOS, or another computer. One way or the other, you cannot permanently damage an NVIDIA GPU from a BIOS flash in and of itself. The results once you try to put load on it, however, cannot be guaranteed.

**The base of this release is copyright nVIDIA and has been provided in a modified fashion for the sole purpose of modifying personal property. This is dangerous and you will have no one but yourself to blame if things go wrong. Your warranty will be null and void the second you so much as think about using this tool.**

**God speed, overclocker.**

## So wait, this gets me 1.1v on my 4090/1.11v on my 4080 again? I can run those fancy BIOS that gave me 'Board ID mismatch' now?

Yep, if you're one of the unlucky bastards that bought a 4090/4080 later down the line, you can flash full voltage BIOSes to your neutered card now. **It appears that it was only a BIOS change, not a physical change, that enforced the 1.07v limitation**.

I won't tell you which BIOS to use, though. That's on you and the community to figure out. So far this has been confirmed on multiple 4090s and 4080s. We've even flashed a STRIX BIOS to a Founders Edition 3070. This means that not only do you get full voltage back, you also get to flash any of the higher power BIOSes that you wish again.

[Here's a video I recorded showing huge benchmark gains from upgrading to a 1.1v BIOS.](https://www.youtube.com/watch?v=25EarvBrkX8)

# Test cases

## Successful

* 4090 STRIX XOC 1.1v BIOS flashed to 4090 TUF OC card with 1.07v (4090 voltage fix confirmed!)
* 4080 flashed from 1.095v to original 1.11v BIOS (4080 voltage fix confirmed!)
* 4090 Founders Edition BIOS flashed to 4090 TUF OC card (this was supposed to be impossible due to 'different bios chips')
* 3070 STRIX BIOS flashed to 3070 Founders Edition (**Flashing AIB to FE 3xxx series confirmed!**)
* 4090 STRIX XOC BIOS to 4090 Founders Edition (**Flashing AIB to FE 4xxx series confirmed!**) - Solar Bay HOF result from doing this

## nvflashk allows but causes no display/cannot POST
Note that these can still be experimented with and reflashed, you just need a way to reflash without that GPU. See 'Recovery when something goes wrong' below.

* Flashing a 4080 BIOS to a 4090

## GPU will reject the ROM (nothing happens)
* Cross-flashing generations i.e. 3xxx to 2xxx
* Cross-flashing workstation/non-workstation, i.e. 4090 to an A5000. They're not actually identical though, which may be why.
 
## Not yet tested
* Unsigned/modified BIOSes
* 2xxx/3xxx series that had revisions which locked out older power-boosted BIOSes
 
# What does this actually bypass?

During the -6 command, nvflash still verifies a few different things against the BIOS ROM you are trying to flash. This version has overridden all of them and will allow you to flash *any* vBIOS to *any* card.

In theory, this includes the boards in the 2xxx and 3xxx series which received revised chips that then would not accept the higher power limit BIOSes available.

The items that are checked are:

* GPU PCI Device ID (GPU chip, i.e. 2xxx/3xxx/4xxx)
* PCI Subsystem ID (PCB ID)
* Board ID (PCB+GPU ID)
* Hierarchy (Unknown, potentially Lovelace/Turing/etc)
* A couple other minor items that appear to just be software-defined metadata

nVidia has implemented their own 'mismatch bypass' within the nvflash code, and this version has forced that bypass to be enabled at all times. This makes this a very, very dangerous version of nvflash. However, **it will still confirm you want to perform those bypasses and only when they're necessary, unlike former versions of patched nvflash**. This will not flash all willy nilly.

# Recovery when something goes wrong

*First off, thank you for your contribution and courage!* It's almost impossible to actually permanently brick an NVIDIA card in the sense of a BIOS that won't boot. If you burn out components by putting load on it that the hardware wasn't designed to handle, that's another story. But you should always be able to flash back if it fails to upload the ROM.

The only question is whether or not you'll have to use your iGPU or another computer to flash it back. In many cases, the answer is no: the GPU will go into fallback mode and you can flash it while using it. But if you somehow upload a BIOS that, say, has no video output.. you may be in trouble without another computer, PCIe slot, or dual BIOS GPU. Doing something like flashing the 4080 XOC BIOS to a 4090 will cause no video outputs and I had to use my BIOS switch to recover.

On the other hand, when I attempted to flash an A5000 BIOS to my 4090, the GPU simply rejected the firmware, regardless of whether nvflash wanted it there or not, and I just had to reboot my machine:
```
Update display adapter firmware?
Press 'y' to confirm (any other key to abort):
Reading EEPROM (this operation may take up to 30 seconds)

Nothing changed!



ERROR: Invalid firmware image detected.
```

I've also had mid-flash failures which resulted in this message:
```
WARNING: Broken image found, last flashing process might be interrupted and not complete.
```
But again, a simple reflash.

If you want to be safe when flashing, you need at least one of the following, sorted from easiest recovery to hardest:

* An integrated GPU and output on the motherboard. You simply use the main output while you flash the GPU as normal.
* A GPU with a dual BIOS switch. Shut down, flip the switch, start up, disable device in device manager, flip it back, flash, reboot. You can use `nvflashk.exe --version` to show what is on the card, and `nvflashk.exe --version file.rom` to show what is in a file, to ensure you are flashing the right things. **Make sure you back up your second BIOS ROM too in case it does something different you want to restore!**
* An extra PCIe x16 slot and extra GPU that can fit (probably only with a riser cable) and you have extra power cables for, in order to use a working GPU to flash the broken GPU.
* Another computer with an iGPU or extra PCIe slot as described above.

### In the rare case your flashed GPU causes the machine to not POST, usually by flashing one chip to another (ie 4080 to 4090), you can turn on the Compatiblity Support Module (CSM) in BIOS to get past that until you've reflashed the card.

# Instructions

First off, know what you're doing. Yes, technically speaking, flashing in and of itself should be a relatively safe ordeal. You can always flash back and many GPUs have dual BIOS switches.

But god knows what may be in those black box BIOS files and what may make them unique to one card or another. You are on your own here - your GPU may burst into flames because it was missing a capacitor or something. Some BIOS have absolutely no thermal protections and will let the card melt itself to death. But that's what XOC is all about, baby.

## Download

Get latest `nvflashk.exe` from [releases page](https://github.com/notfromstatefarm/nvflash/releases) and place anywhere you'd like.

## Flashing

I'm not going to go in depth into the nvflash tool - this is the same feature set as the official version. The main difference is that I've force-enabled the built-in bypass that nVidia made for these mismatches, so if your board ID doesn't match, it will still flash. You still need to use -6 for any of these bypasses to work.

As you run the tool, it will disable the video driver and your screen will black out momentarily. Don't panic. You can disable the video device yourself in Device Manager if you'd rather avoid this.

## Get a vBIOS
First, you'll need a vBIOS to flash. You may be able to find it at the [TechPowerUp vBIOS collection](https://www.techpowerup.com/vgabios/). Download it and place it next to the nvflashk binary.

The safest bet is flashing a BIOS that was compiled for your exact GPU. However, you wouldn't be here if that's what you were doing. So I hope you've chosen wisely.

## Backup factory ROM

Second, you should back up your factory ROM. If anything happens, you won't have to go searching for a copy. Just do it. I'm lazy too. Do it anyways.

> If you have a dual BIOS switch like me, you'll probably want to back up both of them *just in case*. You can simply flip the switch and run the command again with a different filename, and then switch back (or don't) before you flash. No reboot is required - the BIOS chip is only read from when your machine POSTs and the write destination can be changed on the fly with the switch.

```
nvflashk.exe -b factory.rom
nvflashk pre-release
github.com/notfromstatefarm/nvflashk - Safer GUI version with autorecovery coming by September!

Reading EEPROM (this operation may take up to 30 seconds)

Build GUID            : F0F470ABF21E46FAA818D1B995011D76
Build Number          : 31661963
IFR Subsystem ID      : 1043-889C
Subsystem Vendor ID   : 0x1043
Subsystem ID          : 0x889C
Version               : 95.02.18.80.83
Image Hash            : N/A
Hierarchy ID          : Normal Board
Build Date            : 08/08/22
Modification Date     : 10/13/22
UEFI Version          : 0x70007 ( x64 )
UEFI Variant ID       : 0x000000000000000B ( Unknown )
UEFI Signer(s)        : Microsoft Corporation UEFI CA 2011
XUSB-FW Version ID    : N/A
XUSB-FW Build Time    : N/A
InfoROM Version       : G002.0000.00.03
InfoROM Backup        : Present
License Placeholder   : Present
GPU Mode              : N/A
CEC OTA-signed Blob   : Not Present
```

## Disable write protect
If this is your first time flashing this GPU, you may need to disable the EEPROM write protect.

```
nvflashk.exe --protectoff
nvflashk pre-release
github.com/notfromstatefarm/nvflashk - Safer GUI version with autorecovery coming by September!

Setting EEPROM protection complete.
```

## Flash vBIOS to GPU
Now we're going to actually write the vBIOS to the GPU. Depending on the BIOS and GPU, the exact output of this command will vary. While flashing the 1000W XOC BIOS to the TUF, this is what we see:

```
nvflashk pre-release
github.com/notfromstatefarm/nvflashk - Safer GUI version with autorecovery coming by September!

Checking for matches between display adapter(s) and image(s)...

Reading EEPROM (this operation may take up to 30 seconds)

WARNING: Firmware image PCI Subsystem ID (1043.88E2)
  does not match adapter PCI Subsystem ID (1043.889C).
WARNING: None of the firmware image compatible Board ID's
match the Board ID of the adapter.
  Adapter Board ID:        03E3
  Firmware image Board ID: 0475

PCI Subsystem ID mismatch bypassed!
This could be dangerous. It could also get you a high score..

==BACK UP YOUR BIOS TO STAY SAFE==
Type "YES" to continue sending it:
```
At this point nvflash has detected the device's internal name for itself on PCIe buses differs from those the vBIOS was meant for. This part is normally overridable with an unpatched nvflash, but I've enabled the bypass regardless in case there is an edge case BIOS I wasn't aware of.

Go ahead and type `YES` in all caps and press enter to continue the checks.
```
Bypassing the PCI Subsystem ID mismatch

Board ID mismatch

Board ID mismatch bypassed!
This could be dangerous. It could also get you a high score..

==BACK UP YOUR BIOS TO STAY SAFE==
Type "YES" to continue sending it:
```
This is the part that you will struggle with on an unpatched nvflash. The internal board ID also differs, and nVidia will not allow you to flash a mismatched BIOS by default with nvflash.

However, there is an internal bypass that nVidia left behind in their code which I've forced on in the patched version.

Go ahead and type `YES` again in all caps and press enter. It won't flash yet.
```

Bypassing the Board ID mismatch

Current      - Version:95.02.18.80.83 ID:10DE:2684:1043:889C
               GPU Board (Normal Board)
Replace with - Version:95.02.3C.40.B7 ID:10DE:2684:1043:88E2
               GPU Board (Normal Board)

Update display adapter firmware?
Press 'y' to confirm (any other key to abort):
```
This is the moment of truth. Make sure you still have your backup of the factory BIOS.

```
Reading EEPROM (this operation may take up to 30 seconds)

[==================================================] 100 %
Reading EEPROM (this operation may take up to 30 seconds)

Reading EEPROM (this operation may take up to 30 seconds)


Reboot and say hi to @kefinator on discord.gg/overclock
```

Have fun! To revert, you just run the exact same command against the backup BIOS ROM file after rebooting.
