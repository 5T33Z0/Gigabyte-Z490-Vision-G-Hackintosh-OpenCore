# Gigabyte Z490 Vision G Hackintosh OpenCore

**5T4TU5**: Completed 100%. Added Clover EFI as well.</br>
**IMPORTANT**: Concerning macOS Monterey 12.0 beta: installs without issues, but the I225 Ethernet Controller is currently not working…

[![Board](https://img.shields.io/badge/Gigabyte-Z490_Vision_G-informational.svg)](https://www.gigabyte.com/Motherboard/Z490-VISION-G-rev-1x/support#support-dl-bios)
[![OpenCore Version](https://img.shields.io/badge/OpenCore-0.7.3-important.svg)](https://github.com/acidanthera/OpenCorePkg/releases/latest)
[![Clover Version](https://img.shields.io/badge/Clover-r5136-important.svg)](https://github.com/CloverHackyColor/CloverBootloader/releases/tag/5134)
[![macOS Catalina](https://img.shields.io/badge/macOS-10.15.7-white.svg)](https://www.apple.com/li/macos/catalina/)
[![macOS Big Sur](https://img.shields.io/badge/macOS-11.5.2-white.svg)](https://www.apple.com/macos/big-sur/)
[![macOS Monterey](https://img.shields.io/badge/macOS-12.0_beta-white.svg)](https://www.apple.com/macos/monterey-preview/)
[![Release](https://img.shields.io/badge/Download-latest-success.svg)](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/releases)
![](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/Pics/BootPicker.png)

## Introduction

EFI folder for the Gigabyte Z490 Vision G mainboard I've been working on and refining constantly since September 2020. It's based on Dortania's OpenCore Install Guide, ACPI Hotpatches from Daliansky's "OC-Little" Repo and my own research. I've dumped the system `DSDT`, analyzed it and added missing components to fine tune the config. 

This is a *genuine* Z490 Vision G EFI unlike most EFIs posted on Forums, Repos and the newly released HackinDROM App. These EFIs are either based on a generic patched DSDT by Olarilla (please stay away from those!) or on SchmockLords EFI for the Gigabyte Z490 Vision D, which contains unnecessary DeviceProperties for Tunderbolt, an I219 1 Gig Ethernet Controller, a Wifi/BT Module and won't pass validation sucessfully.

My EFI Folder does not contain any of this unnecessary ballast. It also doesn't require `FakePCIID.kext` to get the Intel® I225-V 2.5 Gigabit Ethernet Controller working. I think this is the most sophisticated Z490 Vision G EFI folder on Github yet! And just for fun, I added Clover, too.

Sucessfully tested with macOS Mojave, Catalina, Big Sur and Monterey (work in progress).

**NOTE**: For best results, read and follow the install instruction carefully and thoroughly. 

## Build Info

<details>
<summary><strong>Hardware Components</strong></summary>

### System Specs

| Component           | Details                                     |
| :------------------ | :------------------------------------------ |
| Mainboard           | Gigabyte Z490 Vision G                      |
| BIOS		      | F20. F5 or higher is required to disable `CFG Lock`. Otherwise use Kernel Quirk `AppleXcpmCfgLock`|
| CPU                 | Intel® Core i9 10850K (Codename Comet Lake) |
| RAM                 | 32 GB DDR4 2400 Crucial Basllistix Sport LT |
| iGPU		      | Intel® UHD 630. Configured `headless` for computational tasks only. If you need to drive a display [use this Framebuffer-Patch](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/Additional%20Files/Intel%20UHD%20630_HDMI_DP_Framebuffer-Patch.plist) instead      |
| GPU                 | MSI Geforce GTX 760 Twin Frozr Gaming       |
| Audio               | Realtek® ALC1220-VB (Layout-id: `28`)       |
| Ethernet            | Intel® 2.5GbE LAN chip. Incompatible with macOS High Sierra |
</details>
<details>
<summary><strong>BIOS Settings</strong></summary>

### BIOS Settings

* **Tweaker [TAB]**
	* Extreme Memory Profile (XMP): Enabled (if supported by RAM)
	* Advanced CPU Settings
		* VT-d: Enabled (disabled in config.plist anyway, so only relevant to Windows)
		* Intel Speed Shit: Enabled
* **Setings [TAB]**
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
		* Super IO Configurtaion
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

* **Version**: 0.7.3 (check comments in `config.plist` for details)
* **Compatible macOS**: 10.14 (Mojave), 10.15.7 (Catalina), 11.5+ (Big Sur), 12.0 beta (Monterey)
* **System Definition:** `iMac20,2` (SMBIOS Infos need to be added with [GenSMBIOS](https://github.com/corpnewt/GenSMBIOS)). Using a different SMBIOS may require remapping of USB Ports, since the `info.plist` inside the `USBPorts.kext` refers to `iMac20,2` as `model`.
* **ACPI Patches:** `SSDT-AWAC`, `SSDT-EC-USBX`, `SSDT-PLUG`, `SSDT-SBUS-MCHC`, `SSDT-PPMC`
* **OpenCanopy Enabled**: `yes`
* **Iconset**: `modern`
* **Chime**: `no`
* **FileVault**: `no`
* **SecureBootModel**: `Disabled`
* **USB Ports Mapped:** `yes`. Details [here](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/Additional%20Files/USB_Ports.zip)
* **car-active-config:** HighSierra: `FF030000`, Catalina: `FF070000`, Big Sur: `67080000`
* **Issues**: Intel(r) I-225 Ethernet Controller does not work with macOS Versions prior to macOS Catalina and macOS Monterey (as of now).

### Note about Kexts
The following Kexts are disabled by default since I don't know which CPU, GPU you are using:
- `CPUFriend.kext` and `CPUFriendDataProvider.kext` 
	- If you use a different CPU model, create your own DataProviderKext using [CPUFriendFriend](https://github.com/corpnewt/CPUFriendFriend), replace it and reenable the kext as well as `CPUFriend.kext`)
* `AGPMInjector.kext`disabled. Generate it or delete config entry and kext. See Section "Enable AGPM"
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
    │   ├── SSDT-PPMC.aml
    │   └── SSDT-SBUS-MCHC.aml
    ├── Drivers
    │   ├── HfsPlus.efi
    │   ├── OpenCanopy.efi
    │   └── OpenRuntime.efi
    ├── Kexts
    │   ├── AGPMInjector.kext
    │   ├── AppleALC.kext
    │   ├── CPUFriend.kext
    │   ├── CPUFriendDataProvider.kext
    │   ├── Lilu.kext
    │   ├── SMCProcessor.kext
    │   ├── SMCSuperIO.kext
    │   ├── USBPorts_iMac191.kext
    │   ├── USBPorts_iMac202.kext
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
3. Change the value for `csr-active-config` based on your macOS version to disable SIP: `EF0F0000` for Monterey, `67080000` for Big Sur and `FF070000` for Catalina/Mojave
	- AMD GPUs may require additional `boot-args`. Check WhateverGreen repo to find out which you need.
	- If you want to use the Intel UHD 630 integrated graphics to drive a display, download this [Framebuffer-Patch](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/Additional%20Files/Intel_UHD_630_HDMI_DP_Framebuffer-Patch.plist). Open it with a plist editor and copy the dictionary `PciRoot(0x0)/Pci(0x2,0x0)` to `DeviceProperties > Add` (comment-out the existing entry with "#" first, to disable the existing entry).
4. Getting the Intel(R) I225-V Ethernet Controller to work:

	- macOS Big Sur Users (macOS 11.4 or later) don't have to change anything here since this is the currently active config! But for completeness sake, this is what you would have to do otherwise:
	
		1. Disable (comment-out) `DeviceProperties` > `PciRoot(0x0)/Pci(0x1C,0x1)/Pci(0x0,0x0)`
		2. Go to `Kernel` > `Patch` and disable `I225-V Patch`.
		3. Add boot-arg `dk.e1000=0`
		
	- macOS Catalina and Big Sur Users (up to macOS version 11.3 ≤ Kernel 20.4) need to do the following:

		1. Enable (un-comment) `DeviceProperties` > `#PciRoot(0x0)/Pci(0x1C,0x1)/Pci(0x0,0x0)`
		2. Go to `Kernel` > `Patch` and enable `I225-V Patch`.
		3. Delete/disable boot-arg `dk.e1000=0`

	- macOS Monterey beta 2 Users (should supossedly work like this):
	
		1. Disable (comment-out) `DeviceProperties` > `PciRoot(0x0)/Pci(0x1C,0x1)/Pci(0x0,0x0)`
		2. Go to `Kernel` > `Patch` and disable `I225-V Patch`.
		3. Add boot-arg `dk.e1000=0`
		4. In Network Settings, manually assign an IP Address and set it up as a 1 gig connection (base1000T). I can't confirm this since I only have a base100T router and I can't get a connection to the internet going for reasons unknown to me.
	
	**NOTE**: In fact, you could leave the Device Property, Kernel Patch and boot-arg enabled for *both* macOS Catalina and Big Sur altogether. But I think it's cleaner to just enable what's necessary for each OS unless you run a multiboot setup with both Catalina and Big Sur. Then it's probably easier to leave the Device Property, the Kernel Patch and boot-arg enabled. See this discussion for more insight: https://github.com/dortania/bugtracker/issues/213

5. Create SMBIOS infos for `iMac20,2` to the config.plist and save it.
6. Copy the EFI Folder to a FAT32 formated USB Stick
7. Reboot from USB Stick
8. Perform an NVRAM Reset
9. Boot macOS
10. If your system boots successfully, mount your ESP and copy over the EFI Folder to you HDD/SSD and reboot.
11. Continue with Post-Install!

</details>
<details>
<summary><strong>Post-Install</strong></summary>

### Strengthen Security
Change the following settings to make your systm more secure:

- Change UEFI > APFS: `MinDate` and `MinVersion` from `-1` (disabled) to the correct values for the macOS version you are using. A list with the correct values for macOS High Sierra up to Big Sur can be found [here](https://github.com/acidanthera/OpenCorePkg/blob/master/Include/Acidanthera/Library/OcApfsLib.h).</br>

	**BACKGROUND**: OpenCore 0.7.2 introduced a new security feature which prevents loading the APFS driver if it does not match a specific Date and Version. If these values are left at their default `0`, your macOS partition will not show up in the Boot Picker unless macOS Big Sur or newer is installed since the APFS driver will not be loaded. For ease of use (and since I don't know which macOS you will be using) I've deactivated this feature. If you plan to setup a multiboot system running various iterations of macOS you probably should leave it at `-1`. Otherwise you won't be able to boot older OSes.

- `SecureBootModel`: Change from `Disabled` to `j185f` (for iMac20,2) or `j185` (for iMac20,1). **NOTE**: Only applicable to macOS Catalina and newer. You should test this first booting from a USB Stick since it can prevent the system from booting.
	
### Optimizing CPU Power Management
Use [CPUFriendFriend](https://github.com/corpnewt/CPUFriendFriend) to generate a `CPUFriendDataProvider.kext` to optimize the CPU Power Management of your CPU for a more efficent overall performance. You can [follow this Guide](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/Additional%20Files/CPU_Power_Management_EN.pdf) to create your own.
When you're done, reboot. Have a look at the CPU behavior using Intel Power Gadget. You can see, that the CPU idle frequency should be lower now:

![image](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/Pics/CPU_PM.png)

### Enabling Apple Graphics Power Management (AGPM) for dedicated GPUs (NVDIA and AMD)
- Generate `AGPMInjector.kext` for your GPU using [AGPMInjector](https://github.com/Pavo-IM/AGPMInjector) and 
- Copy it to `EFI\OC\Kexts`
- Enable the entry in the config.plist
- Save and reboot.
- Open [IORegistryExplorer](https://github.com/utopia-team/IORegistryExplorer/releases) and search for`PR00`. If it look like this, CPU Power Management and AGPM are working correctly:

![](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/Pics/AGPMEnabler.png)

**NOTE**: For more Post-Install tweaks and tips, check out my small collection of [Config Tweaks](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/Additional%20Files/OpenCore_Config_Tweaks_EN.md)

### Calculating Scan Policy (optional)
The items displayed in the Boot Picker Menu are based on a combination of bits representing kinds of devices (SATA, NVME, USB, etc.) as well as Filesystems (APFS, HFS, NTFS, etc.). There are 24 bits which can be turned on and off to modify what's displayed in the Boot Picker. The combination of selected bits create what's called the `ScanPolicy`. It's located under Misc > Security in the config.plist. The default value of my EFI is `0` which means "scan for everything which is bootable on the system". Although this is great for compatibility, it will also display EFI Folders on drives which are not the boot drive as well.

To change the `ScanPolicy` to your liking, you can make use of this online calculator: https://oc-scanpolicy.vercel.app/. I am using `2687747` for example which hides EFI Folders and NTFS Drives. If I need windows I just boot it from the BIOS Boot Menu (F12).

**IMPORTANT**: Calculating a wrong `ScanPolicy` can lead to the Boot Picker beeing empty, so you can't boot into macOS. So make sure to test the value first by booting from FAT32 formatted USB Stick containing your EFI Folder with the new value for "Scan Policy".
</details>

## CPU Benchmark
![image](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/Pics/BigSur%20Benchmark.png)</br>
[SEE ALL RESULTS](https://browser.geekbench.com/v5/cpu/5386949)

## Credits and Thank yous
- Acidanthera and Team for the [OpenCore Bootloader](https://github.com/acidanthera/OpenCorePkg)
- Dortantia for [OpenCore Install Guide](https://dortania.github.io/OpenCore-Install-Guide/)
- SergeySlice for [Clover Bootloader](https://github.com/CloverHackyColor/CloverBootloader)
- [Corpnewt](https://github.com/corpnewt) for SSDTTime, GenSMBIOS and ProperTree
- daliansky for [OC Little ACPI Hotpatch Collection](https://github.com/5T33Z0/OC-Little-Translated) 
- [Pavo-IM](https://github.com/Pavo-IM/) for APGM Injector and OC Gen X
- [SL-Soft](https://www.sl-soft.de/software/) for Kext Updater and ANYmacOS
- jsassu20 for [MacDown](https://macdown.uranusjr.com/) Markdown Editor
