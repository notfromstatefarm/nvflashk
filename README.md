# Patched nvflash

This is a patched version of [nvflash](https://www.techpowerup.com/download/nvidia-nvflash/), nVIDIA's utility for modifying the vBIOS (amongst many other things) on their GPUs.

However, their software (understandably) comes with built-in limitations. One of those limitations is that the Board ID of your GPU must match any vBIOS you attempt to flash to it. While the PCI Subsystem ID can be overridden, the internal board ID cannot. Essentially, the BIOS had to have been made for your particular PCB.

For 99.999% of users, that should be the only need for nvflash: updating your cards to BIOS meant for them. However, extreme overclockers need more.. like the ability to flash the [1000w XOC BIOS for the RTX 4090](https://www.techpowerup.com/vgabios/253487/253487) to the ASUS 4090 TUF to make use of all that liquid metal and subambient cooling.

# Versioning/Support

The current version is `5.814.0.k1`. The version will be the base nvflash version i.e. `5.814.0` followed by a `.kN`. This way, if I have to release a patch to my patch, you know which version you have.

Currently this tool supports all nVidia GPUs up to and including the 4000 series. It has been extensively confirmed to work with flashing a 1000W STRIX XOC BIOS to the 600W TUF 4090 and back, but beyond that has not been tested further.

# How to use

First off, know what you're doing. Yes, technically speaking, flashing in and of itself should be a relatively safe ordeal. You can always flash back and many GPUs have dual BIOS switches.

But god knows what may be in those black box BIOS files and what may make them unique to one card or another. You are on your own here - your GPU may burst into flames because it was missing a capacitor or something. Some BIOS have absolutely no thermal protections and will let the card melt itself to death. But that's what XOC is all about, baby.

## Download

Get from releases page and place anywhere you'd like.

## Flashing

I'm not going to go in depth into the nvflash tool - this is the same feature set as the official version. The main difference is that I've force-enabled the built-in bypass that nVidia made for these mismatches, so if your board ID doesn't match, it will still flash.

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