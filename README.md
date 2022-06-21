# Gigabyte Z490 Vision G Hackintosh OpenCore
[![BIOS](https://img.shields.io/badge/BIOS-F21-important.svg)](https://www.gigabyte.com/Motherboard/Z490-VISION-G-rev-1x/support#support-dl-bios) [![OpenCore Version](https://img.shields.io/badge/OpenCore-0.8.2-cyan.svg)](https://github.com/acidanthera/OpenCorePkg/releases/latest) [![macOS Catalina](https://img.shields.io/badge/macOS-10.15.7-white.svg)](https://www.apple.com/li/macos/catalina/) [![macOS Big Sur](https://img.shields.io/badge/macOS-11.6.7-white.svg)](https://www.apple.com/macos/big-sur/) [![macOS Monterey](https://img.shields.io/badge/macOS-12.5-white.svg)](https://www.apple.com/macos/monterey/) [![macOS Monterey](https://img.shields.io/badge/macOS-13_beta-white.svg)](https://www.apple.com/macos/macos-ventura-preview/) [![Release](https://img.shields.io/badge/Download-latest-success.svg)](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/releases/latest)</br>
![15161753](https://user-images.githubusercontent.com/76865553/173877386-1dd1b451-5e50-46b7-9f1e-554485b3a48a.png)

<details><summary><strong>TABLE of CONTENTS</strong> (click to reveal)</summary>

- [About](#about)
- [Build Info](#build-info)
	- [System Specs](#system-specs)
	- [BIOS Settings](#bios-settings)
	- [OpenCore Details](#opencore-details)
	- [EFI Folder Structure](#efi-folder-structure)
	- [About included ACPI Tables](#about-included-acpi-tables)
	- [Disabled Kexts](#disabled-kexts)
- [Installation](#installation)
	- [Installing macOS](#installing-macos)
	- [EFI Install Guide for OpenCore](#efi-install-guide-for-opencore)
- [Post-Install](#post-install)
	- [Strengthen Security (recommended)](#strengthen-security-recommended)
	- [Optimizing CPU Power Management](#optimizing-cpu-power-management)
	- [Calculating Scan Policy (optional)](#calculating-scan-policy-optional)
	- [Changing Themes](#changing-themes)
	- [Patching-in Kepler Drivers](#patching-in-kepler-drivers)
- [CPU Benchmark](#cpu-benchmark)
- [Credits and Thank yous](#credits-and-thank-yous)
</details>

## About
EFI folder for the Gigabyte Z490 Vision G mainboard I've been working on and refining since September 2020. It's based on Dortania's OpenCore Install Guide and analysis of an .ioreg file from a real iMac20,1. I've dumped the system `DSDT`, analyzed it and added missing components and features via `SSDT` Hotpatches from Daliansky's "OC-Little" Repo to get it as close to a real Mac as possible. USB Ports are mapped via `ACPI`, so no USBPort kext is required. I think this is the most sophisticated Z490 Vision G EFI folder available on Github. 

This is a *genuine* Z490 Vision G EFI, built from scratch. Unlike most pseudo Z490 Vision G EFIs posted on Forums and Repos, which are either based on generically patched DSDTs by Olarila/MaLDon (stay away from those!) or on SchmockLords EFI for the Z490 Vision D, which contains unnecessary DeviceProperties for Tunderbolt, an I219 Ethernet Controller and on-board WiFi/BT. My EFI Folder doesn't contain any of this junk.

Tested successfully with macOS Mojave, Catalina, Big Sur and Monterey.

**NOTE**: For best results, read and follow the install instruction carefully and thoroughly.

| :warning: Issues related to macOS Monterey/Ventura|
|:--------------------------------------------------|
|The Intel I225-V Ethernet Controller doesn't work in macOS 12+ by default. You need to [flash a custom firmware](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/I225-V_FIX.md).
|Getting 3rd party WiFi/BT/LAN cards to work is hit or miss. Since I don't use WiFi nor Bluetooth I can't help you with that. All I now about the issue and the required settings is documented [here](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/I225_stock_vs_cstmfw.md).
|600/700-series NVIDIA Cards require [Geforce Kepler Patcher](https://github.com/chris1111/Geforce-Kepler-patcher) (only compatible with macOS Monterey).
		
## Build Info

### System Specs

Component     | Details                                                       
:------------:|-------------------------------------------------------------- 
**Board**     | Gigabyte Z490 Vision G. **BIOS**: F21. F5 or newer is required to disable `CFG Lock`. Otherwise enable Kernel Quirk `AppleXcpmCfgLock`
**CPU**       | Intel Core i9 10850K (Comet Lake)         
**RAM**       | 32 GB DDR4 2400 Crucial Ballistix Sport LT
**iGPU**      | Intel UHD 630 (Headless). Use this [Framebuffer Patch](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/Additional_Files/Intel_UHD_630_HDMI_DP_Framebuffer-Patch.plist) if you want to use it for driving a display.|
**GPU**       | Saphire RX580 Nitro+ (4 GB)
**Audio**     | Realtek® ALC1220-VB (Layout-id: `17`)
**Ethernet** (on-board)  | Intel I225-V 2.5GbE. Compatible with macOS 10.15.7 and newer. Requires [flashing a custom Firmware](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/I225-V_FIX.md) for macOS Monterey
**Ethernet** (PCIe)| Intel PRO/1000 PT Dual Port Server Adapter (any macOS)

### BIOS Settings

<details>
<summary><strong>Click to reveal</strong></summary>

![BIOS](https://user-images.githubusercontent.com/76865553/141654274-6d6a5803-e18e-407e-aa9f-767ab18094bc.jpg)

* **Tweaker [TAB]**
	* Extreme Memory Profile (XMP): Enabled (if supported by RAM)
	* Advanced CPU Settings
		* VT-d: Enabled
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
	* CFGLock: Disabled (only available on newer BIOS versions)
	* Windows 10 Features: Windows 10 
	* CSM: Disabled (to get rid of legacy code from `DSDT`)
</details>
	
### OpenCore Details

* **System Definition:** `iMac20,2`. SMBIOS data needs to be generated. I use [**OCAT**](https://github.com/ic005k/OCAuxiliaryTools#readme) for this.
* **macOS Compatibility**: 10.14 up to 13.0 beta (10.14 requires `iMac19,1` config)
* **OpenCanopy**: enabled
* **Chime**: no
* **FileVault**: `Optional`
* **SIP:** `Disabled`
* **SecureBootModel**: `Disabled`
* **USB Ports Mapped:** Yes, via ACPI. Details [here](https://github.com/5T33Z0/OC-Little-Translated/tree/main/01_Adding_missing_Devices_and_enabling_Features#readme)

### EFI Folder Structure

<details>
<summary><strong>Click to reveal</strong></summary>

```
EFI
├── BOOT
│   └── BOOTx64.efi
└── OC
    ├── ACPI
    │   ├── DMAR.aml
    │   ├── SSDT-AWAC-ARTC.aml
    │   ├── SSDT-DMAC.aml
    │   ├── SSDT-EC-USBX.aml
    │   ├── SSDT-FWHD.aml
    │   ├── SSDT-PLUG.aml
    │   ├── SSDT-PMC.aml
    │   ├── SSDT-PORTS.aml
    │   ├── SSDT-RX580.aml
    │   ├── SSDT-SBUS-MCHC.aml
    │   └── SSDT-XSPI.aml
    ├── Drivers
    │   ├── HfsPlus.efi
    │   ├── OpenCanopy.efi
    │   ├── OpenRuntime.efi
    │   └── ResetNvramEntry.efi
    ├── Kexts
    │   ├── AGPMInjector.kext
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
    │   │   ├── Blackosx
    │   │   │   └── BsxM1
    │   │   └── velickovicdj
    │   │  	└── EnterTwilight
    │   └── Label
    ├── config.plist
    └── config_iMac19,1.plist
```
</details>

### About included ACPI Tables
My EFI Folder contains additional ACPI Tables besides the usual, which you won't find in the OpenCore Install Guide. Some of them are board-specific, some of them are modified versions of the regular tables, some are just cosmetic. 

Here's what the extra tables do:

- **DMAR**: DMAR replacement table without Reserved Memory Regions so the I225-V LAN Card works in macOS Big Sur and Monterey.
- **SSDT-AWAC-ARTC**: Special variant of `SSDT-AWAC.` Disables AWAC Clock and enables RTC as ARTC instead. Also disables legacy `HPET` device.
- **SSDT-PORTS**: OS-agnostic USB Port Mapping Table for the Z490 Vision G. No additional USB Port kext or quirks are required. Since the USB ports are mapped via ACPI they will work in *any* macOS. See the "Additional Files" folder for a detailed list of available and mapped ports.
- **SSDT-DMAC** (optional): Adds a fake [Direct Memory Access Controller](https://electronicsdesk.com/dma-controller.html) to the device tree of I/O Registry. Disablked By default, since it's cosmetic.
- **SDT-FWHD** (optional): Adds fake Firmware Hub Device (FWHD) to I/O Registry. Used by almost every intel-based Mac. Disabled by default since it's cosmetic.
- **SSDT-PMC** (optional): Adds fake Apple-exclusive `PCMR` Device to ACPI. Required for 300-series mainboards but optional on 400-series and newer. Disabled.
- [**SDT-RX580**](https://github.com/5T33Z0/OC-Little-Translated/tree/main/01_Adding_missing_Devices_and_enabling_Features/AMD_Radeon_Tweaks#method-2-using-amd-radeon-patches-by-mattystonie) (disabled) &rarr; Improves Metal Performance on Radeon RX580 cards. See the test [results](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/tree/main/OC_Benchmarks#gpu-computing-benchmarks)
- **SSDT-XSPI** (optional, enabled): Adds PCH SPI Controller to IORegistry as `XSPI`. So it's not a fake device but probably only a cosmetic change.
 
**NOTE**: More info about additional ACPI Tables can be found on my [OC Little Repo](https://github.com/5T33Z0/OC-Little-Translated/tree/main/01_Adding_missing_Devices_and_enabling_Features)

### Disabled Kexts
The following Kexts are disabled by default since I don't know which CPU, GPU, Hard Disk and SMBIOS you will be using:

- `AGPMInjector.kext` &rarr; Injects Apple Graphics Power Management for the GPU into I/O Registry
- `CPUFriend.kext` and `CPUFriendDataProvider.kext`. Create your own DataProvider kext using [CPUFriendFriend](https://github.com/corpnewt/CPUFriendFriend), replace the existing one and enable both
- `NVMeFix.kext`: recommended for all 3rd party NVMe SSD drives
- `FeatureUnlock`: see comments for details
- `RestrictEvents`: Only required when using `MacPro7,1` SMBIOS. Needs `revpatch=auto` boot-arg to disable Warning about unpopulated RAM slots.

## Installation

### Installing macOS
**Coming from Windows/Linux**: If you are on Windows or Linux, follow the guide provided by [Dortania](https://dortania.github.io/OpenCore-Install-Guide/installer-guide/#making-the-installer). **NOTE**: No support from my end for issues related to UBS Installers created in Windows or Linux!

**Coming from macOS**: If you already have access to macOS, you can either download macOS from the App Store or use [**ANYmacOS**](https://www.sl-soft.de/en/anymacos/) instead. It's a hassle-free app than can download any macOS from High Sierra up to Monterey and create a USB Installer as well.

### EFI Install Guide for OpenCore 
	
1. Download latest OC EFI Release and unpack it
2. Select the config of your choice and rename it to `config.plist`
3. Open `config.plist` with [**OCAT**](https://github.com/ic005k/QtOpenCoreConfig/releases) and adjust the following parameters according to your hardware and software configuration:
	- Change `csr-active-config` based on the macOS version to disable SIP (when using GeForce Kepler Patcher, you _have_ to disable SIP):
		- For **Monterey**: `EF0F0000` (0xFEF)</br>
		- For **Big Sur**: `67080000` (0x867)
		- For **Mojave/Catalina**: `FF070000` (0x7FF)
		- For **High Sierra**: `FF030000` (0x3FF)
	- If you want to use the Intel UHD 630 integrated graphics to drive a display, do the following in `DeviceProperties` > `Add`:
		- Disable `PciRoot(0x0)/Pci(0x2,0x0)`(put `##` in front of it)
		- Enable `#PciRoot(0x0)/Pci(0x2,0x0)` (delete the `#`)
4. Getting the Intel(R) I225-V Ethernet Controller to work: read [this](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/I225_stock_vs_cstmfw.md#readme)
5. Generate SMBIOS data for [iMac20,1](https://everymac.com/ultimate-mac-lookup/?search_keywords=iMac20,1) or [iMac20,2](https://everymac.com/ultimate-mac-lookup/?search_keywords=iMac20,2).
6. Save the `config.plist`
6. Copy the EFI Folder to a FAT32 formatted USB Stick
7. Reboot from USB Stick
8. Perform an NVRAM Reset (hit Space Bar to reveal Tools)
9. Boot macOS
10. If your system boots successfully, mount your ESP and copy over the EFI Folder to you HDD/SSD and reboot.
11. Continue with Post-Install!

**IMPORTANT**:

- Using csr-active-config `EF0F0000` also disables incremental system updates on macOS 11 and newer. So everytime an update is available, the *full* installer will be downloaded. To avoid this, either enable SIP temporarily from the OpenCore GUI or use `67080000` instead.</br>
- AMD GPUs may require additional `boot-args`. Check [WhateverGreen's](https://github.com/acidanthera/WhateverGreen#boot-arguments) documentation for details.

## Post-Install

### Strengthen Security (recommended)
Once you got macOS running, you may want to change the following settings to make your system more secure:

1. Enable System Integrity Protection (SIP): change `csr-active-config` to `00000000`.</br> **NOTE**: If you need GeForce Kepler Patcher, SIP needs to be disabled!
2. Under `UEFI/APFS`, change `MinDate` and `MinVersion` from `-1` (disabled) to the correct values for the macOS version you are using. A list with the correct values for can be found [here](https://github.com/5T33Z0/OC-Little-Translated/tree/main/A_Config_Tips_and_Tricks#mindateminversion-settings-for-the-apfs-driver).</br>
3. Change `SecureBootModel` from `Disabled` to `j185f` (for iMac20,2) or `j185` (for iMac20,1). You should test these settings first booting from a USB flash drive since it can prevent the system from booting. Disable it for installing macOS Monterey if you have issues.

**IMPORTANT**

- Since SMBIOS `iMac20,x` is for an iMac with a T2 Security Chip, you won't be notfied about System Updates if `SecureBootModel` is set to `Disabled`. To workaround this either select the correct `SecureBootModel` for your SMBIOS or enable the following Patches in the config.plist:
	- Skip Board ID check (&rarr; Booter/Patch)
	- Reroute HW_BID to OC_BID (&rarr; Booter/Patch)
	- Force IOGetVMMPresent (&rarr; Kernel/Patch)
	- Reroute kern.hv_vmm_present patch (1) (&rarr; Kernel/Patch)
	- Reroute kern.hv_vmm_present patch (2) (&rarr; Kernel/Patch)
- `SecureBootModel` is only applicable to macOS Catalina and newer.
- If you have to use Geforce Kepler Patcher to get your GeForce Kepler Card working, you have to set `SecureBootModel` to `Disabled`

### Optimizing CPU Power Management
You can follow my [guide](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/Additional_Files/Optimizing_CPU_Power_%20Management.md) to use [CPUFriendFriend](https://github.com/corpnewt/CPUFriendFriend) to generate a `CPUFriendDataProvider.kext` alongside `CPUFriend.kext` to optimize the CPU Power Management for a more efficient performance. Have a look at the CPU behavior using Intel Power Gadget. The CPU idle frequency should be lower after adding the kexts:

<details><summary><strong>Screenshot</strong> (click to reveal)</summary>

![image](https://raw.githubusercontent.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/main/Pics/CPU_PM.png)
</details>

### Calculating Scan Policy (optional)
The items displayed in the Boot Picker Menu are based on a combination of bits representing supported devices (SATA, NVME, USB, etc.) and file systems (APFS, HFS, NTFS, etc.). There are 24 bits which can be turned on and off to modify what's displayed in the Boot Picker. The combination of selected bits create what's called the `ScanPolicy`. It's located under Misc > Security in the `config.plist.` The default value of my EFI is `0` (everything). Although this is great for compatibility, it will also display EFI Folders on drives which are not the boot drive as well.

To change the `ScanPolicy` to your liking, you can make use of this online calculator: https://oc-scanpolicy.vercel.app/. I am using `2687747` for example which hides EFI Folders and NTFS Drives. If I need windows I just boot it from the BIOS Boot Menu (F12).

**IMPORTANT**: Calculating a wrong `ScanPolicy` can lead to the Boot Picker being empty, so you can't boot into macOS. So make sure to test the value first by booting from FAT32 formatted USB Stick containing your EFI Folder with the new value for "Scan Policy".

### Changing Themes
Besides the 3 themes from Acidanthera which provide the standard macOS look and feel, I've added 2 additional themes: `BsxM1` (default) and `EnterTwilight`. To change them, do the following:

- Open `config.plist`
- Go to Misc > Boot and change `PickerVariant` to: `Blackosx\BsxM1` or `velickovicdj\EnterTwilight`
- Save and reboot

To revert these changes, enter `Acidanthera\GoldenGate` as `PickerVariant` and change the Flavour of the NVRAM Reset Tool back to `Auto`.

**NOTE**: For more config tips and tricks, you can check out [this](https://github.com/5T33Z0/OC-Little-Translated/tree/main/A_Config_Tips_and_Tricks).

### Patching-in Kepler Drivers

Apple removed Kepler support from macOS Monterey beta 7. So users of NVIDIA GeForce Cards from the Kepler family need to patch them back in post-install using [**Geforce-Kepler-Patcher**](https://github.com/chris1111/Geforce-Kepler-patcher). If you require this patch, you can no longer boot with SIP enabled, so you have to disable it!
</details>

## CPU Benchmark
![image](https://raw.githubusercontent.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/main/Pics/BigSur_Benchmark.png)</br>
[**SEE ALL RESULTS**](https://browser.geekbench.com/v5/cpu/5386949)

## Credits and Thank yous
- Acidanthera and Team for the [OpenCore Bootloader](https://github.com/acidanthera/OpenCorePkg)
- Dortania for [OpenCore Install Guide](https://dortania.github.io/OpenCore-Install-Guide/)
- [Corpnewt](https://github.com/corpnewt) for SSDTTime, GenSMBIOS and ProperTree
- daliansky for [OC Little ACPI Hotpatch Collection](https://github.com/5T33Z0/OC-Little-Translated) 
- [SL-Soft](https://www.sl-soft.de/software/) for Kext Updater and ANYmacOS
- Chris1111 for [GeForce Kepler Patcher](https://github.com/chris1111/Geforce-Kepler-patcher)
- jsassu20 for [MacDown](https://macdown.uranusjr.com/) Markdown Editor
