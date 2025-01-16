# Gigabyte Z490 Vision G Intel I225-V Fix for macOS Monterey and newer
> **Disclaimer**: This fix may require flashing a custom firmware onto the EEPROM of the Intel I225-V Ethernet Controller. I am not responsible for any hardware failures that might occur during the process – execute the following guide at your own risk!

**TABLE of CONTENTS**

- [About](#about)
- [Technical Background](#technical-background)
	- [Verifying that you need a fix](#verifying-that-you-need-a-fix)
- [Option 1: Add `AppleIGC.kext`](#option-1-add-appleigckext)
- [Option 2: Using a SSDT with corrected header description (obsolete)](#option-2-using-a-ssdt-with-corrected-header-description-obsolete)
- [Option 3: flashing a custom Firmware (obsolete)](#option-3-flashing-a-custom-firmware-obsolete)
	- [Preparations](#preparations)
	- [Flashing the Firmware with OpenShell](#flashing-the-firmware-with-openshell)
- [Troubleshooting](#troubleshooting)
- [Credits and Resources](#credits-and-resources)

## About
On the Z490 Vision G, the I225-V Controller stopped working shortly after the first betas of macOS Monterey were released. Various tricks were tried to fix it: assigning IP addresses and settings manually, dropping the `DMAR` table, changing BIOS Settings and Quirks and – the scariest trick of them all – replacing the `IONetworkingFamily` kext of previously working builds, which breaks the seal of the snapshot partition and can corrupt macOS, leaving it in an unbootable state. On top of that, this method only worked temporarily until the next beta was released. There's a lengthy thread about the issue on [insanelymac](https://www.insanelymac.com/forum/topic/348493-discussion-intel-i225-v-on-macos-monterey/).

Until recently, the only reliable option was to just buy a third party network card supported by macOS 12 and newer. Fortunately, 2 fixes were discovered to get the I225-V working again. One requires flashing a modified firmware onto the NIC's EEPROM, so macOS can detect and attach it to the `com.apple.DriverKit-AppleEthernetE1000.dext` driver. The other one uses an SSDT to inject the correct header description data for the I225-V into macOS so it can attach to the .kext version of the` AppleEthernetE1000` driver.

Luckily, a dev ported a Linux driver for Intel I225/I226 2.5 GBit Ethernet controllers to macOS, so that we finally have a working kext and don't have to bother with these workarounds any longer.

## Technical Background
The stock firmware for the Intel I225-V used on some of this Boards (and possibly other Z490 Boards by Gigabyte), contains an incorrect Subsystem-ID and Subsystem Vendor-ID. The Vendor-ID (`8086` for Intel) is also used as Subsystem-Vendor-ID (instead of `1458`) and the Subsystem-ID only contains zeros instead of the correct value (`E000`). 

The screenshot below shows the file header of the I225MOD binary in hex code. Values highlighted in green are the ones that were changed in order to make the controller work again:

<img width="554" alt="I225VEE" src="https://user-images.githubusercontent.com/76865553/166050133-ff5ec23e-68af-439f-af07-81c32f7ebe76.png">

### Verifying that you need a fix
Before attempting to fix your Ethernet Controller make sure you have excluded all other sources for Network errors:

- Check your BIOS and config.plist [settings](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/I225_stock_vs_cstmfw.md#enabling-the-intel-i225-v-ethernet-controller) and adjust them accordingly
- Reset your Network Settings in Terminal:
	- `sudo rm /Library/Preferences/SystemConfiguration/NetworkInterfaces.plist`
	- `sudo rm /Library/Preferences/SystemConfiguration/preferences.plist`
- Reset your Router
- Dump the stock firmware (Steps 1 - 7 of [Option 2](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/I225-V_FIX.md#flashing-the-firmware-with-openshell)) and analyze it in TextEdit or a HexEditor. If the Subsystem ID, Subsystem-Vendor-ID are correct, you may not need a fix.

## Option 1: Add `AppleIGC.kext` 
The user Terminalstrip [pointed me to a new kext](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/issues/37) called [AppleIGC](https://github.com/SongXiaoXi/AppleIGC) which is an "Intel 2.5G Ethernet driver for macOS. Based on the Intel igc implementation in Linux". It works on both stock and custom firmware. Verzadil [managed to get it working](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/issues/38) with the stock firmware with the following settings:

- Drop the OEM's `DMAR` table and replace it by one with removed Reserved Memory regions. You can use SSDTTime to do so
- Add `AppleIGC.kext` to `EFI/OC/Kexts` and your `config.plist`.
- Under `Kernel/Quirks`, turn off `DisableIoMapper`.
- Enable `DisableIoMapperMapping` (req. when using macOS 13.3+)
- Optional: add `e1000=0` to `boot-args` (macOS Monterey+). For Big Sur, use `dk.e1000=0`. I don't need it on my system.
- Save your config and reboot
- Run IORegistryExplorer and verify that the kext is servicing the Intel I225-V: <br> ![](https://user-images.githubusercontent.com/88431749/259463074-b1d3801b-c46d-4250-ac8b-8f5c666698fe.png)

> [!NOTE]
> 
> - If you previously used "Option 2" to get Ethernet working, you need to disable `SSDT-I225V` and `AppleIntelI210Ethernet.kext` so the new kext can be used.
> - Replacing the `DMAR` table and changing the Quirks will allow `AppleVTD` service to work which is required by certain 3rd party WiFi/BT cards (verify in IORegistryExploer).

## Option 2: Using a SSDT with corrected header description (obsolete)
Before flashing a custom firmware as a last resort, you can try to inject the Intel I225-V controller via an SSDT containing the correct Subsystem-ID and Subsystem Vendor-ID. The good guy MacAbe at Insanelymac Forums has written an SSDT for it. For macOS Ventura, you also need to inject the .kext version of the AppleIntel210Ethernet driver to make it work.

**Instructions**

- [**Download**](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/Additional_Files/I225-V/SSDT-I225V.aml.zip?raw=true) the zipped SSDT-I225V and unpack it
- Add it to `EFI/OC/ACPI` folder and config.plist 
- Additional steps (**macOS 13** only): 
	- Add [**this kext**](https://github.com/5T33Z0/OC-Little-Translated/raw/main/01_Adding_missing_Devices_and_enabling_Features/Intel_I225-V_Fix_(SSDT-I225V)/AII210E.zip) 
	- Change `MinKernel` to `22.0.0` so the kext is only injected into macOS Ventura!
- Add boot-arg `dk.e1000=0` (macOS Big Sur) or `e1000=0` (macOS Monterey/Ventura)
- [**Adjust Config**](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/I225_stock_vs_cstmfw.md#settings-combinations-stock-firmware)
- Save the config and reboot

> [!NOTE]
> 
> Since I have flashed the modded firmware months ago I can't test this, but this fix has been reported as working successfully. But if this doesn't work for you, please use Option 1 instead.

## Option 3: flashing a custom Firmware (obsolete)

### Preparations

- **BIOS**
	- enable `VT-d`
	- Save and reboot into macOS
- **macOS**
	- Open **Network Settings**: set Ethernet > IPv4 to `DHCP` and Advanced… > Hardware > Configuration to `Automatic`.
- **OpenCore and config.plist**
	- Mount your EFI
	- Add `OpenShell` (included in OpenCorePkg) to `EFI/OC/Tools` 
	- Open your config.plist and change the following settings:
		- `ACPI/Add`: Disable the `DMAR` replacement table
		- `ACPI/Delete`: Disable the rule for dropping the `DMAR`table
		- `DeviceProperties`: Disable `PciRoot(0x0)/Pci(0x1C,0x1)/Pci(0x0,0x0)` (put `#` in front of it)
		- `Kernel/Add`: Delete `FakePCIID.kext` (if present)
		- `Kernel/Add`: Delete `FakePCIID_Intel_I225-V.kext` (if present)
		- `Kernel/Quirks`: Uncheck `DisableIOMapper`
		- `Misc/Tools`: Add `OpenShell`
		- `NVRAM/Add/…-E41995C9F82`: Disable/remove boot-arg `dk.e1000=0` and/or `e1000=0`
	- Save your `config.plist`
- **USB Flash Drive**
	- [**Download**](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/raw/main/Additional_Files/I225-V/I225-Vmod.zip) `I225-Vmod.zip` and extract it.
	- Copy `eeupdate64.efi` and `I225MOD`to the root folder of a FAT32 formatted USB Flash Drive.
	- Restart the system but stay in the boot picker

### Flashing the Firmware with OpenShell	
1. From the OpenCore GUI, select `OpenShell` (press Space Bar to show if it's hidden)
2. Type `fs0:` and hit `Enter` to change the working drive (`fs:0` is most likely your USB flash drive)
3. Type `ls` to list the content of the drive. In this case `ls0` is the correct drive letter:</br>![06143142](https://user-images.githubusercontent.com/76865553/162021483-39a7d188-5b96-4607-a1cd-a550dd1560d5.png)
4. Next, type `eeupdate64e /gui` and hit `Enter` to run the tool.
5. Select the "Intel(R) Ethernet Controller I225-V" with the arrow keys and hit `Enter`:</br>![06143155](https://user-images.githubusercontent.com/76865553/162020889-a98abf45-6f58-4c96-a7d3-ffb743895b16.png)
6. In the next screen, select "Raw EEPROM - Extended":</br>![06143203](https://user-images.githubusercontent.com/76865553/162020929-65ff5300-0838-4b6f-a26c-2401274b6b10.png)
7. Next, press `F3` to dump the original firmware to your Flash Drive
8. Enter a name for the backup file and confirm it with "OK". There won't be a any confirmation dialog, though:</br>![06143217_01](https://user-images.githubusercontent.com/76865553/162021033-ec75129f-4f4b-48f6-8403-2fc37f75446d.png)
9. Next, press `F4` to load the custom firmware file:</br>![06143217_02](https://user-images.githubusercontent.com/76865553/162021068-d4102c40-94e8-42f5-bc83-85605019ae0c.png)
10. Now type `I225MOD`, press `ENTER` and confirm loading the file. :warning: Keep the original MAC Address when flashing the firmware (you will be asked).
11. Press `ESC` to exit and confirm saving.
12. Reboot the system into macOS Monterey. 

If everything was done correct, you should now have working Internet connectivity. You may need to remove and add the network adapter again under System Preferences > Network.

## Troubleshooting

For me, the I225-V worked immediately after flashing the EEPROM and rebooting. The device is recognized by Hackintool as "Ethernet Controller I225-V". If you are still facing issues afterwards, you could try attaching the I225-V to the `AppleIntelI210Ethernet.kext` by using boot boot-arg `dk.e1000=0` (Big Sur) or `e1000=0` (Monterey).

If you can't access the Internet after flashing the custom firmware, remove the following preferences via Terminal and reboot:

- `sudo rm /Library/Preferences/SystemConfiguration/NetworkInterfaces.plist`
- `sudo rm /Library/Preferences/SystemConfiguration/preferences.plist`

If you still can't access the Internet, delete the following preferences followed by a reboot:

- `/Library/Preferences/com.apple.networkextension.necp.plist`
- `/Library/Preferences/com.apple.networkextension.plist`
- `/Library/Preferences/com.apple.networkextension.uuidcache.plist`

## Credits and Resources
- [**Custom Firmware and Guide**](https://www.hackintosh-forum.de/forum/thread/56123-l%C3%B6sung-f%C3%BCr-i225-v-v2-problem-auf-z490-plattform-vornehmlich-gigabyte-boards-unte/) by badbrain. Translated from german and modified by me.
- [**MacAbe**](https://www.insanelymac.com/forum/topic/352281-intel-i225-v-on-ventura/?do=findComment&comment=2786836) for the SSDT-I225-V.aml
