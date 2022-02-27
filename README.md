# Gigabyte Z490 Vision G Hackintosh OpenCore
[![BIOS](https://img.shields.io/badge/BIOS-F21-important.svg)](https://www.gigabyte.com/Motherboard/Z490-VISION-G-rev-1x/support#support-dl-bios)
[![OpenCore Version](https://img.shields.io/badge/OpenCore-0.7.9-cyan.svg)](https://github.com/acidanthera/OpenCorePkg/releases/latest)
[![Clover Version](https://img.shields.io/badge/Clover-r5145-lime.svg)](https://github.com/CloverHackyColor/CloverBootloader/releases/tag/5139)
[![macOS Catalina](https://img.shields.io/badge/macOS-10.15.7-white.svg)](https://www.apple.com/li/macos/catalina/)
[![macOS Big Sur](https://img.shields.io/badge/macOS-11.6.3-white.svg)](https://www.apple.com/macos/big-sur/)
[![macOS Monterey](https://img.shields.io/badge/macOS-12.3-white.svg)](https://www.apple.com/macos/monterey/)
[![Release](https://img.shields.io/badge/Download-latest-success.svg)](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/releases/latest)
![](https://raw.githubusercontent.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/main/Pics/BootPicker.png)

## Introduction

EFI folder for the Gigabyte Z490 Vision G mainboard I've been working on and refining since September 2020. It's based on Dortania's OpenCore Install Guide and analysis of an .ioreg file from a real iMac20,1. I've dumped the system `DSDT`, analyzed it and added missing components and features via `SSDT` Hotpaches from Daliansky's "OC-Little" Repo to get it as close to a real Mac as possible. USB Ports are mapped via `ACPI`, so no USBPort kext is required. I think this is the most sophisticated Z490 Vision G EFI folder on Github yet! And just for fun, I added Clover, too.

This is a *genuine* Z490 Vision G EFI, built from scratch. Unlike most pseudo Z490 Vision G EFIs posted on Forums and Repos, which are either based on generically patched DSDTs by Olarila/MaLDon (stay away from those!) or on SchmockLords EFI for the Z490 Vision D, which contains unnecessary DeviceProperties for Tunderbolt, an I219 Ethernet Controller and on-board WiFi/BT. My EFI Folder doesn't contain any of this junk.

Tested successfully with macOS Mojave, Catalina, Big Sur and Monterey.

**NOTE**: For best results, read and follow the install instruction carefully and thoroughly.

| :warning: Issues related to macOS Monterey 12.1 (beyond my control)|
|:-------------------------------------------------|
Intel I225-V Ethernet Controller doesn't work on macOS 12. You need to get an extra Network Card.
Bluetooth stack was rewritten in macOS 12 which causes [Bluetooth Issues](https://dortania.github.io/OpenCore-Install-Guide/extras/monterey.html#bluetooth)
600/700-series NVDIA Cards require [Geforce Kepler Patcher](https://github.com/chris1111/Geforce-Kepler-patcher) to enable GPU acceleration
		
## Build Info

<details>
<summary><strong>Hardware Components</strong></summary>

### System Specs

| Component           | Details                                                 |
| :-------------------|-------------------------------------------------------- |
| Mainboard           | Gigabyte Z490 Vision G                                  |
| BIOS		      | F21. F5 or newer is required to disable `CFG Lock`. Otherwise use Kernel Quirk `AppleXcpmCfgLock`|
| CPU                 | Intel® Core i9 10850K (Comet Lake)          	        |
| RAM                 | 32 GB DDR4 2400 Crucial Ballistix Sport LT              |
| iGPU		      | Intel® UHD 630. Configured for computing tasks only. For driving a display [use this Framebuffer-Patch](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/Additional_Files/Intel_UHD_630_HDMI_DP_Framebuffer-Patch.plist) instead|
| GPU                 | MSI Geforce GTX 760 Twin Frozr Gaming                   |
| Audio               | Realtek® ALC1220-VB (Layout-id: `28`)                   |
| Ethernet (onboard)  | Intel® I225-V 2.5GbE. Compatible with macOS Catalina and Big Sur only|
| Ethernet (PCI Card) | Intel® PRO/1000 PT Dual Port Server Adapter (amy macOS) |

</details>
<details>
<summary><strong>BIOS Settings</strong></summary>

### BIOS Settings
![BIOS](https://user-images.githubusercontent.com/76865553/141654274-6d6a5803-e18e-407e-aa9f-767ab18094bc.jpg)

* **Tweaker [TAB]**
	* Extreme Memory Profile (XMP): Enabled (if supported by RAM)
	* Advanced CPU Settings
		* VT-d: Enabled (disabled in config.plist anyway, so only relevant to Windows)
		* Intel Speed Shit: Enabled
* **Settings [TAB]**
	* Platform Power
		* Platform Power Management: Disabled
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

* **Version**: 0.7.7 (check comments in `config.plist` for details)
* **Compatible macOS**: 10.14 to 12.1
* **System Definition:** `iMac20,2` (SMBIOS Infos need to be added with [**OCAT**](https://github.com/ic005k/QtOpenCoreConfig)).
* **OpenCanopy Enabled**: `yes`
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
    │   ├── DNAR.aml
    │   ├── SSDT-AWAC-ARTC.aml
    │   ├── SSDT-DMAC.aml
    │   ├── SSDT-EC-USBX.aml
    │   ├── SSDT-FWHD.aml
    │   ├── SSDT-PLUG.aml
    │   ├── SSDT-PMC.aml
    │   ├── SSDT-PORTS.aml
    │   ├── SSDT-SBUS-MCHC.aml
    │   └── SSDT-XSPI.aml
    ├── Drivers
    │   ├── HfsPlus.efi
    │   ├── OpenCanopy.efi
    │   └── OpenRuntime.efi
    ├── Kexts
    │   ├── AppleALC.kext
    │   ├── CPUFriend.kext
    │   ├── CPUFriendDataProvider.kext
    │   ├── FeatureUnlock
    │   ├── Lilu.kext
    │   ├── NVMeFix.kext
    │   ├── RestrictEvents
    │   ├── SMCProcessor.kext
    │   ├── SMCSuperIO.kext
    │   ├── VirtualSMC.kext
    │   └── WhateverGreen.kext
    ├── OpenCore.efi
    ├── Resources (NOTE: removed files of sub-folders from tree view to reduce clutter)
    │   ├── Font
    │   ├── Image
    │   │   ├── Acidanthera
    │   │   │   ├── Chardonnay
    │   │   │   ├── GoldenGate
    │   │   │   └── Syrah
    │   │   └── Blackosx
    │   │  	└── BsxM1
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
3. Open `config.plist` with [OpenCore Auxiliary Tools](https://github.com/ic005k/QtOpenCoreConfig/releases) and adjust the following parameters according to your hardware and software configuration:
	- Change `csr-active-config` based on the macOS version to disable SIP (if you use the Kepler Patcher for NVIDIA Cards you have to disables SIP!): 
		- For High Sierra: `FF030000` (0x3FF)
    	- For Mojave/Catalina: `FF070000` (0x7FF)
    	- For Big Sur: `67080000` (0x867)
    	- For Monterey: `EF0F0000` (0xFEF)</br>
	- AMD GPUs may require additional `boot-args`. Check WhateverGreen repo to find out which you need.
	- If you want to use the Intel UHD 630 integrated graphics to drive a display, do the following in `DeviceProperties` > `Add`:
		- Disable `PciRoot(0x0)/Pci(0x2,0x0)`(put `##` in front of it)
		- Enable `#PciRoot(0x0)/Pci(0x2,0x0)` (delete the `#`)
4. Getting the Intel(R) I225-V Ethernet Controller to work in…</br>
	|macOS Version|Procedure|
	|-------------|---------|
	**macOS 12.0.1+**|Not working. Get a PCI LAN Card
	**macOS 11.4+**|	1. Disable `PciRoot(0x0)/Pci(0x1C,0x1)/Pci(0x0,0x0)` (add `#` in front of it)</br>2. Go to `Kernel` > `Patch` and disable `I225-V Patch` </br> 3. Add boot-arg `dk.e1000=0`</br> 4. Save and reboot</br>**NOTE**: This is the default configuration. If you are running Big Sur 11.4 and newer you don’t have to change anything here!
	**macOS 10.15 to 11.3**|	1. Enable `#PciRoot(0x0)/Pci(0x1C,0x1)/Pci(0x0,0x0)` (delete `#`)</br> 2. Go to `Kernel` > `Patch` and enable `I225-V Patch`.</br> 3. Delete/disable boot-arg `dk.e1000=0`</br> 4. Save and reboot
	**macOS 10.14 and older**|I225-V Controller is not supported. You need an additional PCI Card.</br>
5. Generate SMBIOS data for [iMac20,1](https://everymac.com/ultimate-mac-lookup/?search_keywords=iMac20,1) or [iMac20,2](https://everymac.com/ultimate-mac-lookup/?search_keywords=iMac20,2).
6. Save the `config.plist`
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
You can follow my [guide](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/Additional_Files/Optimizing_CPU_Power_%20Management.md) to use [CPUFriendFriend](https://github.com/corpnewt/CPUFriendFriend) to generate a `CPUFriendDataProvider.kext` alongside `CPUFriend.kext` to optimize the CPU Power Management for a more efficient performance. Have a look at the CPU behavior using Intel Power Gadget. The CPU idle frequency should be lower after adding the kexts:

![image](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/Pics/CPU_PM.png)

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
<summary><strong>Enabling NVIDIA Kepler Drivers (macOS 12.0 beta 7 and newer)</strong></summary>

### Patching-in Kepler Drivers

Apple removed Keppler support from macOS Monterey beta 7. So users of NVIDIA GeForce Cards from the Kepler family need to patch them back in post-install using [**Geforce-Kepler-Patcher**](https://github.com/chris1111/Geforce-Kepler-patcher). If you require this patch, you can no longer boot with SIP enabled, so you have to disable it!
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
- [SL-Soft](https://www.sl-soft.de/software/) for Kext Updater and ANYmacOS
- Chris1111 for [GeForce Kepler Patcher](https://github.com/chris1111/Geforce-Kepler-patcher)
- jsassu20 for [MacDown](https://macdown.uranusjr.com/) Markdown Editor
