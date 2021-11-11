# Gigabyte Z490 Vision G Hackintosh OpenCore
[![Board](https://img.shields.io/badge/Gigabyte-Z490_Vision_G-informational.svg)](https://www.gigabyte.com/Motherboard/Z490-VISION-G-rev-1x#kf)
[![BIOS](https://img.shields.io/badge/BIOS-F21a-important.svg)](https://www.gigabyte.com/Motherboard/Z490-VISION-G-rev-1x/support#support-dl-bios)
[![OpenCore Version](https://img.shields.io/badge/OpenCore-0.7.5-cyan.svg)](https://github.com/acidanthera/OpenCorePkg/releases/latest)
[![Clover Version](https://img.shields.io/badge/Clover-r5141-lime.svg)](https://github.com/CloverHackyColor/CloverBootloader/releases/tag/5139)
[![macOS Catalina](https://img.shields.io/badge/macOS-10.15-white.svg)](https://www.apple.com/li/macos/catalina/)
[![macOS Big Sur](https://img.shields.io/badge/macOS-11.6-white.svg)](https://www.apple.com/macos/big-sur/)
[![macOS Monterey](https://img.shields.io/badge/macOS-12.0.1-white.svg)](https://www.apple.com/macos/monterey/)
[![Release](https://img.shields.io/badge/Download-latest-success.svg)](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/releases/latest)
![](https://raw.githubusercontent.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/main/Pics/BootPicker.png)

## Introduction

EFI folder for the Gigabyte Z490 Vision G mainboard I've been working on and refining constantly since September 2020. It's based on Dortania's OpenCore Install Guide, ACPI Hotpatches from Daliansky's "OC-Little" Repo and my own research. I've dumped the system `DSDT`, analyzed it and added missing components to fine tune the config. 

This is a *genuine* Z490 Vision G EFI built from scratch, unlike most EFIs posted on Forums, Repos and in the terribly awful HackinDROM App. These EFIs are either based on generically patched DSDTs by Olarila/MadLon (please stay away from those!) or on SchmockLords EFI for the Z490 Vision D, which contains unnecessary DeviceProperties for Tunderbolt, an I219 Ethernet Controller and on-board WiFi/BT. On top of that it won't even pass OC validate successfully.

My EFI Folder does not contain any of this unnecessary junk. It also doesn't require `FakePCIID.kext` to get the Intel® I225-V 2.5 Gigabit Ethernet Controller working. USB Ports are mapped via ACPI, using a corrected OEM Table for macOS, so no `USBPorts.kext` is needed any more. I think this is the most sophisticated Z490 Vision G EFI folder on Github yet! And just for fun, I added Clover, too.

Successfully tested with macOS Mojave, Catalina, Big Sur and Monterey.

**NOTE**: For best results, read and follow the install instruction carefully and thoroughly.

| :warning: Issues related to macOS Monterey 12.1 (beyond my control)|
|:-------------------------------------------------|
I-225 Ethernet Controller doesn't work. No fix available yet. Get a PCI LAN Card!
Bluetooth stack was rewritten in macOS 12 which causes [Bluetooth Issues](https://dortania.github.io/OpenCore-Install-Guide/extras/monterey.html#bluetooth)
NVIDIA Kepler Cards require [Geforce Kepler patcher](https://github.com/chris1111/Geforce-Kepler-patcher) to enable GPU acceleration
		
## Build Info

<details>
<summary><strong>Hardware Components</strong></summary>

### System Specs

| Component           | Details                                                 |
| :-------------------|-------------------------------------------------------- |
| Mainboard           | Gigabyte Z490 Vision G                                  |
| BIOS		        | F21a. F5 or higher is required to disable `CFG Lock`. Otherwise use Kernel Quirk `AppleXcpmCfgLock`|
| CPU                 | Intel® Core i9 10850K (Codename Comet Lake)             |
| RAM                 | 32 GB DDR4 2400 Crucial Ballistix Sport LT              |
| iGPU		        | Intel® UHD 630. Set-up for computing tasks only. For running a display [use this Framebuffer-Patch](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/Additional_Files/Intel_UHD_630_HDMI_DP_Framebuffer-Patch.plist)|
| GPU                 | MSI Geforce GTX 760 Twin Frozr Gaming                   |
| Audio               | Realtek® ALC1220-VB (Layout-id: `28`)                   |
| Ethernet (onboard)  | Intel® 2.5GbE LAN (requires macOS 10.15 or newer)       |
| Ethernet (PCI Card) | Intel® PRO/1000 PT Dual Port Server Adapter (amy macOS) |

</details>
<details>
<summary><strong>BIOS Settings</strong></summary>

### BIOS Settings

* **Tweaker [TAB]**
	* Extreme Memory Profile (XMP): Enabled (if supported by RAM)
	* Advanced CPU Settings
		* VT-d: Enabled (disabled in config.plist anyway, so only relevant to Windows)
		* Intel Speed Shit: Enabled
* **Settings [TAB]**
	* Platform Power
		* Platform Power Management: Enabled
		* PEG ASPM: Enabled
		* PCH ASPM: Enabled
		* DMI ASPM: Enabled 
		* ErP: Enabled (so USB Power turns off, after PC is shut down)
	* IO Ports
		* Internal Graphics: enabled (if CPU has integrated graphics). **NOTE**: The config.plist uses dGPU for Display(s) and iGPU for computational tasks only by default. If you want to use the iGPU to drive a display you need a different Framebuffer Patch (see "EFI Install Instructions" for details).
		* OnBoard LAN Controller: Enabled
		* Audio Controller: Enabled (if On-Board Sound Card is used)
		* Above 4G Decoding: Enabled
		* Re-Size BAR Support: Disabled
		* IOAPIC 24-119 Entries: Enabled
		* Super IO Configuration
			* Serial Port: Disabled
		* USB Configuration
			* Legacy USB Support: Disabled
			* XHCI Hand-off: Enabled
		* Network Stack Configuration
			* Network Stack: Disabled
* **Boot [TAB]**
	* CFGLock: Disabled (Option only available on newer BIOS versions)
	* Windows 10 Features: Windows 10 
	* CSM: Disabled (to get rid of legacy code from `DSDT`)
</details>
<details>
<summary><strong>OpenCore and Config Details</strong></summary>
	
### OpenCore Details

* **Version**: 0.7.5 (check comments in `config.plist` for details)
* **Compatible macOS**: 10.14 (Mojave), 10.15.7 (Catalina), 11.5+ (Big Sur), 12.0.1 (Monterey)
* **System Definition:** `iMac20,2` (SMBIOS Infos need to be added with [**OCAT**](https://github.com/ic005k/QtOpenCoreConfig)).
* **ACPI Patches:** `SSDT-AWAC`, `SSDT-EC-USBX`, `SSDT-PLUG`, `SSDT-SBUS-MCHC`, `SSDT-PPMC`, `SSDT-PORTS`
* **OpenCanopy Enabled**: `yes`
* **Iconset**: `modern`
* **Chime**: `no`
* **FileVault**: `no`
* **SecureBootModel**: `Disabled`
* **USB Ports Mapped:** `yes`. Details [here](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/Additional_Files/Intel_UHD_630_HDMI_DP_Framebuffer-Patch.plist)
* **csr-active-config:** macOS Mojave/Catalina: `FF070000`, Big Sur/Monterey: `67080000`

### Note about Kexts
The following Kexts are disabled by default since I don't know which CPU and Hard Disk you are using:
- `CPUFriend.kext` and `CPUFriendDataProvider.kext`. Create your own DataProvider.kext using [CPUFriendFriend](https://github.com/corpnewt/CPUFriendFriend), replace it and reenable the kext as well as `CPUFriend.kext`)
- `NVMeFix.kext`: recommended for all 3rd part NVME drives
</details>
<details>
<summary><strong>EFI Folder Content</strong></summary>

### EFI Folder Structure

```
EFI
├── BOOT
│   └── BOOTx64.efi
└── OC
    ├── ACPI
    │   ├── SSDT-AWAC-DISABLE.aml
    │   ├── SSDT-EC-USBX.aml
    │   ├── SSDT-PLUG.aml
    │   ├── SSDT-PORTS.aml
    │   ├── SSDT-PPMC.aml
    │   └── SSDT-SBUS-MCHC.aml
    ├── Drivers
    │   ├── HfsPlus.efi
    │   ├── OpenCanopy.efi
    │   └── OpenRuntime.efi
    ├── Kexts
    │   ├── AppleALC.kext
    │   ├── CPUFriend.kext
    │   ├── CPUFriendDataProvider.kext
    │   ├── Lilu.kext
    │   ├── NVMeFix.kext
    │   ├── RestrictEvents.kext
    │   ├── SMCProcessor.kext
    │   ├── SMCSuperIO.kext
    │   ├── VirtualSMC.kext
    │   └── WhateverGreen.kext
    ├── OpenCore.efi
    ├── Resources (NOTE: removed files of sub-folders from tree view to reduce clutter)
    │   ├── Font
    │   ├── Image
    │   │   └── Acidanthera
    │   │       ├── Chardonnay
    │   │       ├── GoldenGate
    │   │       └── Syrah
    │   └── Label
    ├── config.plist
    └── config_iMac19,1.plist
```
</details>

## Installation
<details>
<summary><strong>How to install macOS</strong></summary>

### Installing macOS
If you already have macOS installed but want to perform a clean install, you can either download macOS from the App Store or use [**ANYmacOS**](https://www.sl-soft.de/en/anymacos/). It's a hassle-free app than can download macOS High Sierra, Catalina and Big Sur. It also can create a USB Installer for you. And if you create multiple HFS partitions in the correct sizes, you can use it to create a multi macOS Installer USB Stick as well. 

If you are on Windows or Linux, follow the guide provided by [Dortania](https://dortania.github.io/OpenCore-Install-Guide/installer-guide/#making-the-installer)
</details>
<details>
<summary><strong>EFI Install Instructions</strong></summary>

### EFI Install Guide for OpenCore 
	
1. Download latest OC EFI Release and unpack it
2. Select the config of your choice and rename it to `config.plist`
3. Open `config.plist` and adjust the following parameters according to your hardware and software configuration:
	- Change `csr-active-config` based on the macOS version to disable SIP: 
		- For High Sierra: `FF030000` (0x3FF)
    	- For Mojave/Catalina: `FF070000` (0x7FF)
    	- For Big Sur: `67080000` (0x867)
    	- For Monterey: `EF0F0000` (0xFEF)
	- AMD GPUs may require additional `boot-args`. Check WhateverGreen repo to find out which you need.
	- If you want to use the Intel UHD 630 integrated graphics to drive a display, do the following in `DeviceProperties` > `Add`:
		- Disable `PciRoot(0x0)/Pci(0x2,0x0)`(put `##` in front of it)
		- Enable `#PciRoot(0x0)/Pci(0x2,0x0)` (delete the `#`)
4. Getting the Intel(R) I225-V Ethernet Controller to work:
	- macOS Big Sur/Monterey users don't have to change anything here. But for the sake of completeness, this is what you would have to do otherwise:
	
		1. Disable (comment-out) `DeviceProperties` > `PciRoot(0x0)/Pci(0x1C,0x1)/Pci(0x0,0x0)`(put a `#` in front of it) 
		2. Go to `Kernel` > `Patch` and disable `I225-V Patch`.
		3. Add boot-arg `dk.e1000=0`</br> 
	
		**NOTE**: For getting the I225 Controller to work in macOS Monterey, version beta 7 is required at least!
		
	- macOS Catalina and Big Sur Users (up to macOS version 11.3 ≤ Kernel 20.4) need to do the following:

		1. Enable (un-comment) `DeviceProperties` > `#PciRoot(0x0)/Pci(0x1C,0x1)/Pci(0x0,0x0)`
		2. Go to `Kernel` > `Patch` and enable `I225-V Patch`.
		3. Delete/disable boot-arg `dk.e1000=0`

	- macOS Mojave: I225-V is not supported. Needs additional PCI Card (See "Hardware Components").
	
	**NOTE**: In fact, you could leave the Device Property, Kernel Patch and boot-arg enabled for macOS Catalina and Big Sur/Monterey altogether, since the patch only is applied to Catalina and the boot-arg takes care of picking the correct Ethernet Driver in Big Sur/Monterey. But I think it's cleaner to just enable what's necessary for each OS unless you run a multiboot setup with both Catalina and Big Sur. Then it's probably easier to leave the Device Property, the Kernel Patch and boot-arg enabled. See this discussion for more insight: https://github.com/dortania/bugtracker/issues/213

5. Create SMBIOS infos for `iMac20,2` to the config.plist and save it.
6. Copy the EFI Folder to a FAT32 formatted USB Stick
7. Reboot from USB Stick
8. Perform an NVRAM Reset (hit Space Bar to reveal Tools)
9. Boot macOS
10. If your system boots successfully, mount your ESP and copy over the EFI Folder to you HDD/SSD and reboot.
11. Continue with Post-Install!
</details>
<details>
<summary><strong>Post-Install</strong></summary>

### Strengthen Security
Once you got macOS running, change the following settings to make your system more secure:

- Change UEFI > APFS: `MinDate` and `MinVersion` from `-1` (disabled) to the correct values for the macOS version you are using. A list with the correct values for macOS High Sierra up to Big Sur can be found [here](https://github.com/acidanthera/OpenCorePkg/blob/master/Include/Acidanthera/Library/OcApfsLib.h).</br>

	**BACKGROUND**: OpenCore 0.7.2 introduced a new security feature which prevents the APFS driver from loading if it does not match OS-specific Date (`MinDate`) and Version (`MinVersion`). If left at their default value `0` (as set in the `sample.plist`), the macOS partition will not show up in the Boot Picker unless Big Sur or newer is installed. For ease of use (and since I don't know which macOS you will be using) I've deactivated this feature. If you plan to setup a multiboot system running various iterations of macOS you probably should leave it at `-1`. Otherwise you won't be able to boot older macOSes.

- Change `SecureBootModel`from `Disabled` to `j185f` (for iMac20,2) or `j185` (for iMac20,1). **NOTE**: Only applicable to macOS Catalina and newer. You should test these settings first using a USB flash drive since it can prevent the system from booting. Disable it for installing macOS Monterey if you have issues.
	
### Optimizing CPU Power Management
Use [CPUFriendFriend](https://github.com/corpnewt/CPUFriendFriend) to generate a `CPUFriendDataProvider.kext` to optimize the CPU Power Management for a more efficient overall performance. You can [follow this Guide](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/Additional%20Files/CPU_Power_Management_EN.pdf) to create your own. Once you're done, reboot. Have a look at the CPU behavior using Intel Power Gadget. The CPU idle frequency should be lower now:

![image](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/Pics/CPU_PM.png)

### Enabling AGPM (Apple Graphics Power Management) for dedicated GPUs (NVIDIA and AMD)

- Generate an `AGPMInjector.kext` for your GPU using [AGPMInjector](https://github.com/Pavo-IM/AGPMInjector) 
- Copy it to `EFI\OC\Kexts`
- Enable the entry in the `config.plist`
- Save and reboot.
- Open [IORegistryExplorer](https://github.com/utopia-team/IORegistryExplorer/releases) and search for`PR00`. CPU Power Management and AGPM are working correctly if it looks like this: </br>
	![](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/Pics/AGPMEnabler.png)

### Calculating Scan Policy (optional)
The items displayed in the Boot Picker Menu are based on a combination of bits representing supported devices (SATA, NVME, USB, etc.) and file systems (APFS, HFS, NTFS, etc.). There are 24 bits which can be turned on and off to modify what's displayed in the Boot Picker. The combination of selected bits create what's called the `ScanPolicy`. It's located under Misc > Security in the `config.plist.` The default value of my EFI is `0` (everything). Although this is great for compatibility, it will also display EFI Folders on drives which are not the boot drive as well.

To change the `ScanPolicy` to your liking, you can make use of this online calculator: https://oc-scanpolicy.vercel.app/. I am using `2687747` for example which hides EFI Folders and NTFS Drives. If I need windows I just boot it from the BIOS Boot Menu (F12).

**IMPORTANT**: Calculating a wrong `ScanPolicy` can lead to the Boot Picker being empty, so you can't boot into macOS. So make sure to test the value first by booting from FAT32 formatted USB Stick containing your EFI Folder with the new value for "Scan Policy".

### Changing Themes
Besides the 3 themes from Acidanthera which provide the standard macOS look and feel, I've added 2 additional themes by Chris1111: `Minimal` and `Minimal-SSD.` Neither of them is enabled by default. To enable them, do the following:

- Open `config.plist`
- Go to Misc > Boot and change `PickerVariant` to: `chris1111\Minimal` or `chris1111\Minimal-SSD`
- Save and reboot

To revert these changes, enter `Acidanthera\GoldenGate` as `PickerVariant` and change the Flavour of the NVRAM Reset Tool back to `Auto`.

**NOTE**: For more Post-Install tweaks and tips, check out my small collection of [Config Tweaks](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/Additional%20Files/OpenCore_Config_Tweaks_EN.md)
</details>
</details>
<details>
<summary><strong>Switching between OpenCore and Clover easily</strong></summary>

### Bootloader Chooser
I recently discovered [BootloaderChooser](https://github.com/jief666/BootloaderChooser) which lets you pick the bootloader of choice prior to booting. This is how the folder structure looks like:	
![](https://user-images.githubusercontent.com/76865553/134049430-31a289ee-c5f2-4982-8e0b-7a3728c0bf46.png)

Basically, you put the "Clover" Folder on the same level as the the "OC" Folder and then replace the "BootX64.efi" in the "BOOT" Folder with the one that come with the Bootloader Chooser. Then you can select which Bootloader you want to use. Pretty nice for setting up your USB flash drive having Clover and OC to chose from:

![1614718620_Screenshot2020-10-06at19_59_49 png c92924a458ee86f0a4ff504d7a9118a6](https://user-images.githubusercontent.com/76865553/134054798-0e0e1f96-b2d6-4447-9daf-44c4df3e9601.png)
</details>
<details>
<summary><strong>Enabling NVIDIA Kepler Drivers (macOS 12.0 beta 7)</strong></summary>

### Patching-in Kepler Drivers

Apple removed Keppler support from macOS Monterey beta 7. So users of NVIDIA GeForce Cards from the Kepler family need to patch them back in post-install using [**Geforce-Kepler-Patcher**](https://github.com/chris1111/Geforce-Kepler-patcher). If you require this patch,you can no longer boot with SIP enabled,so you have to disable it.
</details>

## CPU Benchmark
![image](https://raw.githubusercontent.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/main/Pics/BigSur_Benchmark.png)</br>
[**SEE ALL RESULTS**](https://browser.geekbench.com/v5/cpu/5386949)

## Credits and Thank yous
- Acidanthera and Team for the [OpenCore Bootloader](https://github.com/acidanthera/OpenCorePkg)
- Dortania for [OpenCore Install Guide](https://dortania.github.io/OpenCore-Install-Guide/)
- SergeySlice for [Clover Bootloader](https://github.com/CloverHackyColor/CloverBootloader)
- [Corpnewt](https://github.com/corpnewt) for SSDTTime, GenSMBIOS and ProperTree
- daliansky for [OC Little ACPI Hotpatch Collection](https://github.com/5T33Z0/OC-Little-Translated) 
- [Pavo-IM](https://github.com/Pavo-IM/) for APGM Injector and OC Gen X
- [SL-Soft](https://www.sl-soft.de/software/) for Kext Updater and ANYmacOS
- Chris1111 for [GeForce Kepler Patcher](https://github.com/chris1111/Geforce-Kepler-patcher)
- jsassu20 for [MacDown](https://macdown.uranusjr.com/) Markdown Editor
