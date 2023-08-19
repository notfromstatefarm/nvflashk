# nvflashk
[![Github All Releases](https://img.shields.io/github/downloads/notfromstatefarm/nvflash/total.svg)]()

[![Flashing a 4090 TUF OC to STRIX BIOS for 1.1v and getting huge gains](https://img.youtube.com/vi/25EarvBrkX8/default.jpg)](https://youtu.be/25EarvBrkX8) [![Flashing a 4090 TUF OC to the Founders Edition BIOS](https://img.youtube.com/vi/ADlVK0HoMkw/default.jpg)](https://youtu.be/ADlVK0HoMkw) [![Flashing a 1.1v 1000W 4090 STRIX XOC BIOS to a 1.07v 4090 TUF OC](https://img.youtube.com/vi/iEZBof0S9dc/default.jpg)](https://youtu.be/iEZBof0S9dc)


This is a patched version of [nvflash](https://www.techpowerup.com/download/nvidia-nvflash/), nVIDIA's utility for modifying the vBIOS (amongst many other things) on their GPUs which allows flashing *any* vBIOS to *any* nVIDIA GPU. Black magic stuff.

The factory nvflash comes with built-in limitations. One of those limitations is that the GPU ID, Board ID, and other IDs of your GPU must match any vBIOS you attempt to flash to it. While the PCI Subsystem ID can sometimes be overridden with `-6`, the internal board ID and others cannot. Essentially, the BIOS had to have been made for your particular PCB and chip combo.

For most users, that limitation makes sense. However, there are those who seek maximum performance from the hardware they buy. Have a 1.07v limited 4 series and want the full 1.1v? Want to flash a 1000W XOC BIOS to one of those 1.07v cards? Planning on slapping an AIB BIOS onto your FE? You'll probably need this tool.

nvflashk enables flashing *any* nVIDIA BIOS to *any* nVIDIA GPU up to the 4000 series.

**The base of this tool is copyright nVIDIA and has been provided in a modified fashion for the sole purpose of modifying personal property. This is dangerous and you will have no one but yourself to blame if things go wrong. Your warranty will be null and void the second you so much as think about using this tool.**

**God speed, overclocker.**

# Explanation
## So wait, this gets me 1.1v on my 4090/1.11v on my 4080 again? I can run those fancy BIOS that gave me 'Board ID mismatch' now?

Yep, if you're one of the unlucky bastards that bought a 4090/4080 later down the line, you can flash full voltage BIOSes to your neutered card now. **It appears that it was only a BIOS change, not a physical change, that enforced the 1.07v limitation**.

I won't tell you which BIOS to use, though. That's on you and the community to figure out. So far this has been confirmed on multiple 4090s and 4080s. We've even flashed a STRIX BIOS to a Founders Edition 3070. This means that not only do you get full voltage back, you also get to flash any of the higher power BIOSes that you wish again.

[Here's a video I recorded showing huge benchmark gains from upgrading to a 1.1v BIOS.](https://www.youtube.com/watch?v=25EarvBrkX8)

## What else have you tried?

So far the following scenarios have been confirmed, and I will continue to update as confirmations come in:

* 4090 STRIX XOC 1.1v BIOS flashed to 4090 TUF OC card with 1.07v (4090 voltage fix confirmed!)
* 4080 flashed from 1.095v to original 1.11v BIOS (4080 voltage fix confirmed!)
* 4090 Founders Edition BIOS flashed to 4090 TUF OC card (this was supposed to be impossible due to 'different bios chips')
* 3070 STRIX BIOS flashed to 3070 Founders Edition (**Flashing AIB to FE 3xxx series confirmed!**)


Things I'm curious about whether or not it works:
* Unsigned/modified BIOSes
* 2xxx/3xxx series that had revisions which locked out older power-boosted BIOSes
 
## What does this actually bypass?

During the -6 command, nvflash still verifies a few different things against the BIOS ROM you are trying to flash. This version has overridden all of them and will allow you to flash *any* vBIOS to *any* card.

In theory, this includes the boards in the 2xxx and 3xxx series which received revised chips that then would not accept the higher power limit BIOSes available. But test this at your own risk, I personally have not. All I know is that this tool *will* try to flash a 3060 vBIOS to my 4090 and I have not grown the balls to try.

The items that are checked are:

* GPU PCI Device ID (GPU chip, i.e. 2xxx/3xxx/4xxx)
* PCI Subsystem ID (PCB ID)
* Board ID (PCB+GPU ID)
* Hierarchy (Unknown, potentially Lovelace/Turing/etc)
* A couple other minor items that appear to just be software-defined metadata

nVidia has implemented their own 'mismatch bypass' within the nvflash code, and this version has forced that bypass to be enabled at all times. This makes this a very, very dangerous version of nvflash. However, **it will still confirm you want to perform those bypasses and only when they're necessary, unlike former versions of patched nvflash**. This will not flash all willy nilly.

## Safety

Alright, everyone's gonna ask about safety. Is it safe? I can't legally tell you if it is or isn't. I don't know.

What I can tell you is that nVidia's flashing protocol appears to be robust and failures do not prevent reflashing. If something breaks or your power cuts out, you can boot up and reflash, even without an iGPU. However, what happens when you do something like flash a BIOS from one series to another has never been seen before. It could cause major electrical problems. Who knows.

If you don't feel safe doing it, wait until someone else does. I'll keep a list of successful weird things in the README.

## Versioning/Support

The current version is `5.814.0.k1`. The version will be the base nvflash version i.e. `5.814.0` followed by a `.kN`. This way, if I have to release a patch to my patch, you know which version you have.


# Instructions

First off, know what you're doing. Yes, technically speaking, flashing in and of itself should be a relatively safe ordeal. You can always flash back and many GPUs have dual BIOS switches.

But god knows what may be in those black box BIOS files and what may make them unique to one card or another. You are on your own here - your GPU may burst into flames because it was missing a capacitor or something. Some BIOS have absolutely no thermal protections and will let the card melt itself to death. But that's what XOC is all about, baby.

## Download

Get latest `nvflash64k.exe` from [releases page](https://github.com/notfromstatefarm/nvflash/releases) and place anywhere you'd like.

## Flashing

I'm not going to go in depth into the nvflash tool - this is the same feature set as the official version. The main difference is that I've force-enabled the built-in bypass that nVidia made for these mismatches, so if your board ID doesn't match, it will still flash. You still need to use -6 for any of these bypasses to work.

As you run the tool, it will disable the video driver and your screen will black out momentarily. Don't panic. You can disable the video device yourself in Device Manager if you'd rather avoid this.

## Get a vBIOS
First, you'll need a vBIOS to flash. You may be able to find it at the [TechPowerUp vBIOS collection](https://www.techpowerup.com/vgabios/). Download it and place it next to the nvflash64k binary.

The safest bet is flashing a BIOS that was compiled for your exact GPU. However, you wouldn't be here if that's what you were doing. So I hope you've chosen wisely.

## Backup factory ROM

Second, you should back up your factory ROM. If anything happens, you won't have to go searching for a copy.

```
nvflash64k.exe -b factory.rom
NVIDIA Firmware Update Utility (Version 5.814.0.k1)

Mismatch bypass by @kefinator. JOIN DISCORD: discord.gg/overclock
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
nvflash64k.exe --protectoff
NVIDIA Firmware Update Utility (Version 5.814.0.k1)

Mismatch bypass by @kefinator. JOIN DISCORD: discord.gg/overclock
Setting EEPROM protection complete.
```

## Flash vBIOS to GPU
Now we're going to actually write the vBIOS to the GPU. Depending on the BIOS and GPU, the exact output of this command will vary. While flashing the 1000W XOC BIOS to the TUF, this is what we see:

```
NVIDIA Firmware Update Utility (Version 5.814.0.k1)

Mismatch bypass by @kefinator. JOIN DISCORD: discord.gg/overclock
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
# What happens if the flash fails?

Usually, you can just flash it again. The GPU still works for non-accelerated graphics so you can do this even without an iGPU. Here's an example of me flashing back to my stock BIOS after a failed flash to the XOC BIOS:

```
.\nvflash64k.exe -6 .\tufoc.rom
NVIDIA Firmware Update Utility (Version 5.814.0.k1)

Mismatch bypass by @kefinator. JOIN DISCORD: discord.gg/overclock
Checking for matches between display adapter(s) and image(s)...

Reading EEPROM (this operation may take up to 30 seconds)

WARNING: Broken image found, last flashing process might be interrupted and not complete.


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
YES

Bypassing the PCI Subsystem ID mismatch

Board ID mismatch

Board ID mismatch bypassed!
This could be dangerous. It could also get you a high score..

==BACK UP YOUR BIOS TO STAY SAFE==
Type "YES" to continue sending it:
YES

Bypassing the Board ID mismatch

Current      - Version:95.02.18.80.83 ID:10DE:2684:1043:889C
               GPU Board (Normal Board)
Replace with - Version:95.02.3C.40.B7 ID:10DE:2684:1043:88E2
               GPU Board (Normal Board)

Update display adapter firmware?
Press 'y' to confirm (any other key to abort):
Reading EEPROM (this operation may take up to 30 seconds)

WARNING: Broken image found, last flashing process might be interrupted and not complete.

[==================================================] 100 %
Reading EEPROM (this operation may take up to 30 seconds)

Reading EEPROM (this operation may take up to 30 seconds)


Reboot and say hi to @kefinator on discord.gg/overclock
```