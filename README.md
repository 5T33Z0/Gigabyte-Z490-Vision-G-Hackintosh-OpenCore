# Gigabyte Z490 Vision G Hackintosh OpenCore

**5T4TU5**: Completed 100%. Added Clover EFI as well.</br>
**IMPORTANT**: Concerning macOS Monterey 12.0 beta: installs without issues, but the I225 Ethernet Controller is currently not working…

[![Board](https://img.shields.io/badge/Gigabyte-Z490_Vision_G-informational.svg)](https://www.gigabyte.com/Motherboard/Z490-VISION-G-rev-1x/support#support-dl-bios)
[![OpenCore Version](https://img.shields.io/badge/OpenCore-0.7.1-important.svg)](https://github.com/acidanthera/OpenCorePkg/releases/latest)
[![Clover Version](https://img.shields.io/badge/Clover-r5136-important.svg)](https://github.com/CloverHackyColor/CloverBootloader/releases/tag/5134)
[![macOS Catalina](https://img.shields.io/badge/macOS-10.15.7-white.svg)](https://www.apple.com/li/macos/catalina/)
[![macOS Big Sur](https://img.shields.io/badge/macOS-11.5.1-white.svg)](https://www.apple.com/macos/big-sur/)
[![macOS Monterey](https://img.shields.io/badge/macOS-12.0_beta-white.svg)](https://www.apple.com/macos/monterey-preview/)
[![Release](https://img.shields.io/badge/Download-latest-success.svg)](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/releases)
![](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/Pics/BootPicker.png)

## Introduction

EFI folder for the Gigabyte Z490 Vision G mainboard I've been working on and refining constantly since September 2020. It's based on Dortania's OpenCore Install Guide, ACPI Hotpatches from Daliansky's "OC-Little" Repo and my own research. I dumped the system `DSDT`, analyzed it and added missing components to fine tune the config. 

This is a *genuine* Z490 Vision G EFI – unlike most EFIs posted on Forums, Repos and the newly released HackinDROM App. These are either based on a generic patched DSDT by Olarilla (patched DSTDs in 2021… in OpenCore… really?) or on SchmockLords EFI for the Gigabyte Z490 Vision D, which contains DeviceProperties and SSDTs useless to the Vision G; like Tunderbolt, an additional 1 Gig Ethernet Controller and a Wifi/BT Module – along with all the minor config.plist issues which won't let it pass config validation sucessfully.

My EFI does not contain all this unnecessary junk. It also doesn't require `FakePCIID.kext` to get the Intel® I225-V 2.5 Gigabit Ethernet Controller working. I think this is the most sophisticated Z490 Vision G EFI folder on Github yet! And just for fun, I added Clover, too.

Sucessfully tested with Catalina and Big Sur and Monterey (work in progrss).

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

* **Version**: 0.7.2 (check comments in `config.plist` for version details)
* **Compatible macOS**: 10.15.7 (Catalina) and 11.4+ (Big Sur)
* **System Definition:** `iMac20,2` (SMBIOS Infos need to be added with [GenSMBIOS](https://github.com/corpnewt/GenSMBIOS)). Using a divergent SMBIOS rather than `iMac20,2` may require remapping of USB Ports, since the `info.plist` inside the `USBPorts.kext` refers to `iMac20,2` as `model`.
* **ACPI Patches:** `SSDT-AWAC`, `SSDT-EC-USBX`, `SSDT-PLUG`, `SSDT-SBUS-MCHC`, `SSDT-DMAC`, `SSDT-PPMC`
* **OpenCanopy Enabled**: `yes`
* **Iconset**: `modern`
* **Chime**: `no`
* **FileVault**: `no`
* **SecureBootModel**: `j185f`(change it to `Disabled` if your system won't boot)
* **USB Ports Mapped:** `yes`. Details [here](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/Additional%20Files/USB_Ports.zip)
* **car-active-config:** HighSierra: `FF030000`, Catalina: `FF070000`, Big Sur: `67080000`
* **Issues**: High Siera requires SMBIOS `iMac18,3` as well as a Fake CPU-ID in order to boot. Also, Internet doesn't work in High Sierra since it does not contain the necessary kext for the I225 Controller.
* **Config Validation**: </br>
![](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/Pics/ConfigValidation.png)

***NOTE**: I've since scrapped the idea of running High Sierra altogether, because it partly breaks Big Sur: if you reboot the system after using macOS High Sierra, you can no longer start Big Sur without using the boot-arg`-no_compat_check` – otherwise you get the dreaded stop sign instead. I don't know what causes this issue. I assume booting High Sierra breaks the seal of Big Sur somehow but I am not sure. If you happen to face this isssue, simply reinstall Big Sur over your existing installation and everything will be back to normal. So don't panic! ;)

### Note about Kexts
The following Kexts are disabled by default since I don't know which CPU, GPU you are using:
- `CPUFriend.kext` and `CPUFriendDataProvider.kext` 
	- If you use a different CPU model, create your own DataProviderKext using [CPUFriendFriend](https://github.com/corpnewt/CPUFriendFriend), replace it and reenable the kext as well as `CPUFriend.kext`)
* `AGPMInjector.kext`disabled. Generate it or delete config entry and kext. See Section "Enable AGPM"
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
3. choose `csr-active-config` based on your macOS version to disable SIP: `67080000` for Big Sur, `FF070000` for Catalina/Mojave, `FF030000` for High Sierra
4. Graphics:
	- AMD GPUs may require additional `boot-args`. Check WhateverGreen repo to find out which you need.
	- If you want to use the Intel UHD 630 integrated graphics to drive a display, download this [Framebuffer-Patch](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/Additional%20Files/Intel_UHD_630_HDMI_DP_Framebuffer-Patch.plist). Open it with a plist editor and copy the dictionary `PciRoot(0x0)/Pci(0x2,0x0)` to `DeviceProperties > Add` (comment-out the existing entry with "#" first, to disable the existing entry).
5. Getting the Intel(R) I225-V Ethernet Controller to work:

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

6. Create SMBIOS infos for `iMac20,2` to the config.plist and save it.
7. Copy the EFI Folder to a FAT32 formated USB Stick
8. Reboot from USB Stick
9. Perform an NVRAM Reset
10. Boot macOS
11. If your system boots successfully, mount your ESP and copy over the EFI Folder to you HDD/SSD and reboot.
12. Continue with Post-Install!

</details>
<details>
<summary><strong>Post-Install Tweaks</strong></summary>
	
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
