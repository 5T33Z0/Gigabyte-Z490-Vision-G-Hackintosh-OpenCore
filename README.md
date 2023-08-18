# Gigabyte Z490 Vision G Hackintosh OpenCore
[![BIOS](https://img.shields.io/badge/BIOS-F21-important.svg)](https://www.gigabyte.com/Motherboard/Z490-VISION-G-rev-1x/support#support-dl-bios) [![OpenCore Version](https://img.shields.io/badge/OpenCore-0.9.4-cyan.svg)](https://dortania.github.io/builds/?product=OpenCorePkg&viewall=true) ![MacOS](https://img.shields.io/badge/macOS-10.13–14-purple.svg) [![Release](https://img.shields.io/badge/Download-Latest_Release-success.svg)](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/releases/latest)</br>![15161753](https://user-images.githubusercontent.com/76865553/173877386-1dd1b451-5e50-46b7-9f1e-554485b3a48a.png)

**TABLE of CONTENTS**

- [About](#about)
- [Hardware Info](#hardware-info)
	- [System Configuration](#system-configuration)
	- [BIOS Settings](#bios-settings)
- [OpenCore Details](#opencore-details)
	- [General Information](#general-information)
	- [EFI Folder Structure (OpenCore)](#efi-folder-structure-opencore)
- [Installing/Upgrading macOS](#installingupgrading-macos)
- [Deployment](#deployment)
	- [Preparing the `config.plist`](#preparing-the-configplist)
	- [Testing the EFI](#testing-the-efi)
- [Post-Install](#post-install)
	- [Strengthen Security (recommended)](#strengthen-security-recommended)
	- [Optimizing CPU Power Management (recommended)](#optimizing-cpu-power-management-recommended)
	- [Calculate a Scan Policy (optional)](#calculate-a-scan-policy-optional)
	- [Changing Themes](#changing-themes)
- [Alternate GPU Configurations](#alternate-gpu-configurations)
	- [iGPU Optimizations](#igpu-optimizations)
	- [Enabling Resizable BAR (optional)](#enabling-resizable-bar-optional)
	- [AMD GPUs and different SMBIOSes](#amd-gpus-and-different-smbioses)
		- [Addressing DRM issues with AMD GPUs in macOS 11 and newer](#addressing-drm-issues-with-amd-gpus-in-macos-11-and-newer)
	- [Using NVIDIA Kepler Cards in macOS 12 and newer](#using-nvidia-kepler-cards-in-macos-12-and-newer)
		- [Preparation](#preparation)
- [CPU Benchmark](#cpu-benchmark)
- [Credits and Thank yous](#credits-and-thank-yous)

## About
EFI folder and config.plist for the Gigabyte Z490 Vision G mainboard I've been working on and refining since September 2020. It's based on Dortania's OpenCore Install Guide and analysis of an `.ioreg` file from a real iMac20,1. I've dumped the system `DSDT`, analyzed it and added missing components and features via `SSDT` hotpatches from Daliansky's "OC-Little" Repo to get it as close to a real Mac as possible. USB Ports are mapped via `ACPI`, so no USB Port kext is required. I think this is the most sophisticated Z490 Vision G EFI folder available on Github. 

This is a *genuine* Z490 Vision G EFI that has been created from scratch. Unlike many other Z490 Vision G EFIs found on forums and repositories, which are either derived from generic ones by Olarila/MaLDon or from SchmockLord's EFI for the Z490 Vision D, it does not contain any unnecessary properties for devices that are not present on the Vision G, such as Thunderbolt, an I219 Ethernet controller, and on-board WiFi/BT.

Tested successfully with macOS 10.14 to 14 beta. For best results, read and follow the install instruction carefully and thoroughly.

|:warning: Important Updates|
|:----------------------------------------------------|
| The Intel I225-V Ethernet Controller finally got a dedicated kext called [**AppleIGC**](https://github.com/SongXiaoXi/AppleIGC) so the [**previous fixes**](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/I225-V_FIX.md) are now obsolete!
|600/700-series Nvidia Cards require root patching with [**OpenCore Legacy Patcher**](https://github.com/dortania/OpenCore-Legacy-Patcher/releases) to reinstall Nvidia drivers.

## Hardware Info

### System Configuration

Component     | Details                                                       
-------------:|-------------------------------------------------------------- 
**Board**     | Gigabyte Z490 Vision G. **BIOS**: F21. F5 or newer is required to disable `CFG Lock`. Otherwise enable Kernel Quirk `AppleXcpmCfgLock`
**CPU**       | Intel Core i9 10850K (Comet Lake)      
**RAM**       | 32 GB DDR4 2400 Crucial Ballistix Sport LT
**iGPU**      | Intel UHD 630 (Headless). Use this [**Framebuffer Patch**](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/Additional_Files/Graphics/Intel_UHD_630_HDMI_DP_Framebuffer-Patch.plist) if you want to use it for driving a display.
**GPU**       | Saphire RX580 Nitro+ (4 GB)
**Audio**     | Realtek® ALC1220-VB (Layout-id: `17`)
**Ethernet** (on-board) | Intel I225-V 2.5GbE. Compatible with macOS 10.15.7 and newer.
**Ethernet** (PCIe)| Intel PRO/1000 PT Dual Port Server Adapter (any macOS). Currently unused.

**Intel® 400 Series Chipset Datasheets** (PDF): [**Vol. 1**](https://cdrdv2.intel.com/v1/dl/getContent/615170) [**Vol. 2**](https://cdrdv2.intel.com/v1/dl/getContent/615146) [**Specs Update**](https://cdrdv2.intel.com/v1/dl/getContent/615296)

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
		* Re-Size BAR Support: Disabled (Enable if your GPU supports it)
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

## OpenCore Details

### General Information

Parameter | Details                                                       
---------:|-------------------------------------------------------------- 
**SMBIOS** | `iMac20,2`. For i5/i7 CPUs, use `iMac20,1`. SMBIOS data needs to be generated. I use [**OCAT**](https://github.com/ic005k/OCAuxiliaryTools#readme) for this
**Supported macOS** | macOS 10.14 up to 14.x (10.14 requires `iMac19,1` config)
**OpenCanopy** | Enabled
**Boot Chime**| No
**FileVault** |`Optional`
**SIP**| `Enabled`. Adjust `csr-active-config` based on your specific needs.
**SecureBootModel**| `j185f`. For `iMac20,1`, use `j185`. :warning: Set to `Disabled` if you are using a Kepler GPU and want to install/run macOS Monterey and newer
**USB Port Mapping**| Yes, via ACPI. Details [**here**](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/Additional_Files/USB/USB_Ports_List.pdf).

### EFI Folder Structure (OpenCore)

<details>
<summary><strong>Click to reveal</strong></summary>

```
EFI
├── BOOT
│   └── BOOTx64.efi
└── OC
    ├── ACPI
    │   ├── SSDT-AWAC-ARTC.aml
    │   ├── SSDT-EC-USBX.aml
    │   ├── SSDT-MCHC.aml
    │   ├── SSDT-PLUG.aml
    │   └── SSDT-PORTS.aml
    ├── Drivers
    │   ├── HfsPlus.efi
    │   ├── OpenCanopy.efi
    │   ├── OpenRuntime.efi
    │   └── ResetNvramEntry.efi
    ├── Kexts
    │   ├── AppleALC.kext
    │   ├── AppleIGC.kext
    │   ├── CPUFriend.kext
    │   ├── CPUFriendDataProvider.kext
    │   ├── Lilu.kext
    │   ├── NVMeFix.kext
    │   ├── RestrictEvents
    │   ├── SMCProcessor.kext
    │   ├── SMCSuperIO.kext
    │   ├── VirtualSMC.kext
    │   └── WhateverGreen.kext
    ├── OpenCore.efi
    ├── Resources (NOTE: removed files in sub-folders from tree view to reduce clutter)
    │   ├── Font
    │   ├── Image
    │   │   ├── Acidanthera
    │   │   │   ├── Chardonnay
    │   │   │   ├── GoldenGate
    │   │   │   └── Syrah
    │   │   ├── Blackosx
    │   │   │   └── BsxM1
    │   │   └── velickovicdj
    │   │  		└── EnterTwilight
    │   └── Label
    ├── config_Catalina.plist
    ├── config_iMac19,1.plist
    ├── config_MacPro7,1.plist
    └── config.plist
```
</details>

## Installing/Upgrading macOS
**Coming from Windows/Linux**: If you are on Windows or Linux, follow the guide provided by [**Dortania**](https://dortania.github.io/OpenCore-Install-Guide/installer-guide/#making-the-installer). I provide no support for issues with UBS Installers created in Windows or Linux or when installing macOS into a VM!

**Coming from macOS**: If you already have access to macOS, you can either download macOS from the App Store, via the [**OpenCore Legacy Patcher**](https://github.com/dortania/OpenCore-Legacy-Patcher) App or use [**ANYmacOS**](https://www.sl-soft.de/en/anymacos/). Botch can download macOS High Sierra to Ventura and create a USB Installer as well.

## Deployment
Please read the following explanations carefully and thoroughly and follow the given instructions. In order to boot your PC with this EFI successfully, adjustments to the `config.plist` and used kexts may be necessary to adapt the config to your system configuration and the macOS version you want to install/run. 

### Preparing the `config.plist`
Download my latest EFI Folder from the [**Releases**](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/releases) section and unpack it. Make sure to check the included `Changelog.md` as well, since it contains important background information and useful explanations. 

Select the config of your choice and rename it to `config.plist`. Open it with [**OCAT**](https://github.com/ic005k/QtOpenCoreConfig/releases) or a ProperTree and check the following sections/settings and adjust them to your needs:

1. **ACPI/Add** Section. Add/Enable/Disable SSDTs as needed
	- [**DMAR**](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/Additional_Files/ACPI/DMAR.dsl) (optional): [DMAR replacement table](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/Additional_Files/ACPI/DMAR.dsl) with specific Reserved Memory Region removed. Required for 3rd party LAN/Wifi/BT cards which won't work when VT-D and the Intel I225-V controller are enabled (macOS Big Sur and newer). **NOTE**: Removed from latest version since OpenCore 0.9.2 introduced a new Quirk called `DisableIoMapperMapping` which handles this automatically, so it's no longer necessary to drop and replace the DMAR table in macOS 13.3+. Refer to OpenCore's Documentation for details.
	- **SSDT-AWAC-ARTC**: Custom variant of `SSDT-AWAC.` Disables AWAC Clock and enables `RTC` as `ARTC` instead. Also disables legacy `HPET` device.
	- **SSDT-PORTS**: OS-agnostic USB Port Mapping Table for the Z490 Vision G. No additional USB Port kext or quirks are required. Since the USB ports are mapped via ACPI they will work in *any* version of macOS. Check [**this pdf**](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/Additional_Files/USB/USB_Ports_List.pdf) for a detailed list of mapped ports. **NOTE**: macOS does not support USB 3.2 via the USB protocol. It requires Thunderbold 3 or newer instead to support speeds greater than 5 Gbit. So there's no speed benefit when using the red USB ports over the blue ones when running macOS!
	- **SSDT-PLUG.aml**: Not required on macOS 12 and newer. Also not needed when using `CPUFriend.kext` and `CPUFriendDataProvider.kext`.
	- [**SSDT-XSPI**](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/Additional_Files/ACPI/SSDT-XSPI.dsl) (optional): Adds PCH SPI Controller to IORegistry as `XSPI`. Cosmetic only.

	**NOTE**: Additional info about these ACPI Tables can be found on my [**OC Little Repo**](https://github.com/5T33Z0/OC-Little-Translated/tree/main/01_Adding_missing_Devices_and_enabling_Features)

2. **ACPI/Delete** Section
	- **Drop OEM DMAR Table** &rarr; Only enable if you need to use the DMAR replacement table. OpenCore 0.9.2 introduced a new Quirk called `DisableIoMapperMapping`. It works independently of `DisableIoMapper` and addresses reoccurring connectivity issues in macOS 13.3+ which weren't there before. If your configuration required to drop/replace the DMAR table before it still does now!
	- **Drop OEM USB Port Map (xh_cmsd4)** &rarr; Drops the original USB Port mapping so `SSDT-PORTS.aml` can replace it.
	- **Drop HPET Table** &rarr; Drops the table for the High Precision Event Timer which is now a legacy device.

3. **Booter** Section
	- **Booter/MMIOWhitelist**
		- I added these memory regions after analyzing the bootlog. Since I don't know if these are used by all systems, I disabled them.
		- To figure out which ones can be whitelisted, [**follow this guide**](https://github.com/5T33Z0/OC-Little-Translated/tree/main/12_MMIO_Whitelist)
		- This is not a necessity, just some fine-tuning.
	- **Booter/Patch**: OpenCore patches used to skip the board-id check as part of a [**workaround**](https://github.com/5T33Z0/OC-Little-Translated/tree/main/S_System_Updates) to fix System Update Notifications. Only needed when using an NVIDIA Kepler Card which requires disabling `SecureBootModel` and `SIP` in oder to install and load the GPU drivers.
	-  **Booter/Quirks**: If your GPU supports resizable BAR (enable Above 4G Decoding and Resizable BAR in BIOS), you can set `ResizeAppleGpuBars` to `0`. But leave `UEFI/Quirks` at `-1` to not affect the BAR size in other OSes.

4. **DeviceProperties**
	- ~~`#PciRoot(0x0)/Pci(0x1C,0x1)/Pci(0x0,0x0)` &rarr; Disabled device-id spoof for the Intel I-225V. &rarr; Only required when running macOS Catalina! Delete the `#` to enable it. Requires `Kernel/Patch` as well. [**Read this**](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/I225_stock_vs_cstmfw.md#readme) for getting the Intel(R) I225-V Ethernet Controller to work on different versions of macOS.~~ No longer required.
	- `#PciRoot(0x0)/Pci(0x2,0x0)` &rarr; Disabled Framebuffer for using the iGPU for driving a monitor. Delete the `#` to enable it.
	- `PciRoot(0x0)/Pci(0x1F,0x3)` &rarr; Settings for on-board audio. Also contains the Layout-id
	- `PciRoot(0x0)/Pci(0x2,0x0)` &rarr; Headless Framebuffer for using the iGPU for computational tasks only. 
		- Disable this entry when using the `iMacPro1,1` or `MacPro7,1` SMBIOS.
		- Delete/Disable the `enable-metal` property when using a AMD (Big) Navi Card, since these cards support Metal 3.

5. **Kernel/Add** Section. The following Kexts are disabled by default since I don't know which CPU, GPU, Hard Disk and SMBIOS you will be using:

	- `CPUFriend.kext` and `CPUFriendDataProvider.kext`. Create your own CPUFriendDataProvider in Post-Install with [**CPUFriendFriend**](https://github.com/corpnewt/CPUFriendFriend) to optimize CPU Power Management [**as explained here**](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/Additional_Files/CPU_Pwr/README.md). It is recommended to create a new Data Provider kext after switching the SMBIOS and/or updating/upgrading macOS.
	- `NVMeFix.kext`: recommended for all 3rd party NVMe SSD drives
	- `RestrictEvents`: 
		- Required when using `MacPro7,1` SMBIOS &rarr; Disables warnings about unpopulated RAM slots.
		- Also required for enabling the `VMM-x86_64` Board-id so OTA updates work when `SIP` and `SecureBootModel` are disabled (necessary when using NVIDIA Kepler Cards in macOS 12+).
	- `AppleALC.kext`: Slimmed version of AppleALC I compiled myself. It only contains Layout `17` and is only 86 kB in size. If you want to use a different Layout, you need to use the regular version of AppleALC or [compile your own](https://github.com/5T33Z0/AppleALC-Guides/tree/main/Slimming_AppleALC).

6. **Kernel/Quirks**: 
	- If your BIOS does not provide the option to disable CFG Lock (requires BIOS Update), enable the `AppleXcpmCfgLock` Quirk instead.
	- If you don't use Windows on your system, you can disable `CustomSMBIOSGuid` (prohibits injecting SMBIOS data into Windows)
	- OpenCore 0.9.2 introduced a new Quirk for macOS 13.3+ called `DisableIoMapperMapping`. It eliminates the need for using a modified DMAR Table with removed Reserved Memory regions to "resolve compatibility issues with Wi-Fi, Ethernet and Thunderbolt devices when `AppleVTD` is enabled". So you can try this quirk instead of replacing the DMAR table to address issues with 3rd party WiFi/BT cards.

7. **Misc/Security**: 
	- `SecureBootModel`: 
		- Set to to `j185` (for iMac20,1) or `j185f` (for iMac20,2).
		- Set to `Disabled` if you are using an NVIDIA Kepler Card. Otherwise the system will crash on boot. In this case you also need to enable `RestrictEvents.kext` to enable the `VMM-x86_64` board-id so OTA updates will work. More details [**here**](https://github.com/5T33Z0/OC-Little-Translated/tree/main/S_System_Updates). 

8. **NVRAM/Add**
	- `4D1FDA02-38C7-4A6A-9CC6-4BCCA8B30102`: 
		- revpatch:sbvmm &rarr; Setting for RestrictEvents.kext to enable the board-id VMM spoof. Only required if you have to disable `SIP` and `SecureBootModel` in order to boot with patched in NVIDIA Drivers for Kepler GPUs.
		- Boot-args: based on the [fix](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/I225-V_FIX.md#option-1-using-a-ssdt-with-corrected-header-description) you are using to get the Intel I225-V working in macOS 12 and newer, you might need `dk.e1000=0` (macOS Big Sur) and/or `e1000=0` (macOS Monterey+). 
		- OCLP-Settings: `-allow_amfi` &rarr; Required for OpenCore Legacy Patcher so Kepler Drivers can be installed
	- `7C436110-AB2A-4BBB-A880-FE41995C9F82`: 
		- Change `csr-active-config` to disable SIP. When using OCLP or GeForce Kepler Patcher, you _have_ to disable SIP):
			- **Big Sur** and newer: `03080000` (0x803) or `EF0F0000` (0xFEF) if you need to completely disable SIP (required for GeForce Kepler Patcher)
			- **Mojave/Catalina**: `EF070000` (0x7EF)
			- **High Sierra**: `FF030000` (0x3FF)
	
		> **Warning:** <ul><li> AMD GPUs may require additional `boot-args`. Check WhateverGreen's [**documentation**](https://github.com/acidanthera/WhateverGreen#boot-arguments) for details. <li> Applying root patches with OCLP results in a broken security seal which affects System Updates: incremental (or delta) system updates won't work after that. Instead, the *full* macOS installer (about 12 GB) will be downloaded every time a system update is available.</br>

9. **PlatfotmInfo/Generic** Section: 
	- Generate SMBIOS data for `iMac20,1` (for Core i9) or `iMac20,2` (for Core i5/i7)
	- If you're not using Windows on your system, change `UpdateSMBIOSMode` from `Custom` to `Create`

10. Save the `config.plist`

### Testing the EFI
1. Copy the EFI Folder to a FAT32 formatted USB flash drive for testing
2. Reboot
3. Perform an NVRAM Reset (hit Space Bar to reveal Tools)
4. Boot macOS from the USB flash drive
5. If your system boots successfully, mount your ESP and copy over the EFI Folder to you HDD/SSD and reboot.
6. Continue with Post-Install!

## Post-Install

### Strengthen Security (recommended)
Once you got macOS running, you should change the following settings to make your system more secure:

1. Change `csr-active-config` to `00000000` to enable System Integrity Protection (SIP)
2. Under `UEFI/APFS`, change `MinDate` and `MinVersion` from `-1` (disabled) to the correct values for the macOS version you are using. A list with correct values can be found [**here**](https://github.com/5T33Z0/OC-Little-Translated/tree/main/A_Config_Tips_and_Tricks#mindateminversion-settings-for-the-apfs-driver).</br>
3. Change `SecureBootModel` from `Disabled` to `j185` (for iMac20,1) or `j185f` (for iMac20,2). 

> **:Warning:** You should have a working backup of your EFI folder on a FAT32 formatted USB flash drive since changing these settings can prevent the system from booting. You may have to disable them for installing macOS Monterey if you have issues.

**NOTES**

- `SecureBootModel` is only applicable to macOS Catalina and newer.
- Since SMBIOS `iMac20,x` is for an iMac with a T2 Security Chip, you won't be notified about System Updates if `SIP` and `SecureBootModel` are `Disabled`. To workaround this either select the correct `SecureBootModel` for your SMBIOS or enable the following settings in the `config.plist`:
	- **Booter/Patch**: 
		- Enable `Skip Board ID`
		- Enable `Reroute HW_BID to OC_BID`
	- **Kernel/Add**: Enable `RestrictEvents.kext`
- `SIP` and `SecureBootModel` must stay disabled for booting macOS 12 or newer with patched-in NVIDIA Kepler drivers!

### Optimizing CPU Power Management (recommended)
Follow my [**guide**](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/Additional_Files/CPU_Pwr/README.md) to generate a `CPUFriendDataProvider.kext` which works alongside `CPUFriend.kext` to optimize CPU Power Management for a more efficient performance. Have a look at the CPU behavior using Intel Power Gadget. The CPU idle frequency should be lower after adding the kexts:

<details><summary><strong>Screenshot</strong> (click to reveal)</summary>

![image](https://raw.githubusercontent.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/main/Pics/CPU_PM.png)
</details>

### Calculate a Scan Policy (optional)
The items displayed in OpenCore's Boot Picker menu are based on a combination of bits representing supported devices (SATA, NVME, USB, etc.) and file systems (APFS, HFS, NTFS, etc.). There are 24 bits which can be turned on and off to modify what's displayed in the Boot Picker. The combination of selected bits create what's called the `ScanPolicy`. It's located under in the `config.plist` under `Misc/Security`. The default value of my EFI is `0` (everything). Although this is great for compatibility, it will also display EFI Folders on drives which are not the boot drive as well.

To change the `ScanPolicy` to your liking, you can use the [**OpenCore ScanPolicy Generator**](https://oc-scanpolicy.vercel.app/). I am using `2687747` for example which hides EFI Folders and NTFS Drives. To add a custom entry for a Windows Disk to OpenCore's Boot Picker [**follow my guide**](https://github.com/5T33Z0/OC-Little-Translated/blob/main/I_Windows/Custom_Entries.md). Otherwise you can just boot Windows from the BIOS Boot Menu (F12) which also bypasses all the OpenCore injections.

:warning: **IMPORTANT**: Calculating an incorrect `ScanPolicy` can lead to a BootPicker without entries, so you can't select any OS. So make sure you have a working Backup of your EFI folder!

### Changing Themes
Besides the included themes from Acidanthera which provide the standard macOS look and feel, I've added 2 additional ones: `BsxM1` (default) and `EnterTwilight`. To change them, do the following:

- Open `config.plist`
- Go to Misc/Boot and change `PickerVariant` to: `Blackosx\BsxM1` or `velickovicdj\EnterTwilight`
- Save and reboot

To revert the changes, enter `Acidanthera\GoldenGate` as `PickerVariant` and change the Flavour of the NVRAM Reset Tool back to `Auto`.

**NOTE**: For more config tips and tricks, you can check out [**this**](https://github.com/5T33Z0/OC-Little-Translated/tree/main/A_Config_Tips_and_Tricks).

## Alternate GPU Configurations

### iGPU Optimizations

Listed below, you find results of benchmark tests conducted with Geekbench 5. I was testing Metal 3 performance of the iGPU for 3 different cases: default configuration vs. using Apples GUC Firmware vs. using RPS Control.

**iGPU**: UHD 630 in "offline/headless" configuration</br>
**AAPL,ig-platform-id:** 0300C89B</br>
**SMBIOS**: iMac20,2 </br>
**macOS**: 13.2</br>
**Geekbench**: 5.5.0

Test #| Added Properties | Compute Score | Notes
:----:|------------------|:-------------:|-------
1| `enable-metal`|[4671](https://browser.geekbench.com/v5/compute/6259341)| <ul><li> Default <li> Mandatory to enable Metal 3 support in macOS 13 if your dGPU doesn't support it <li> iGPU Freq.: 0,33 GHz </li> 
2| `enable-metal` </br> `igfxfw=2` |[**4808**](https://browser.geekbench.com/v5/compute/6259442)| <ul><li> `igfxfw=2` forces loading of Apple Graphics Unit Control (GUC) firmware <li> Slightly higher score <li> GUI feels snappier <li>Slightly faster boot <li> iGPU Freq.: 1,2 GHz <li> Can cause system crashes </li>
3| `enable-metal` </br> `igfxfw=2` </br> `rebuild-device-tree` | [4796](https://browser.geekbench.com/v5/compute/6259548) |<ul><li>Rebuilding Device Tree doesn't make a difference <li>iGPU Freq.: 1,2 GHz
4| `enable-metal` </br> `rps-control`| [4754](https://browser.geekbench.com/v5/compute/6259487) | <ul><li> Uses RPS control patch (whatever that is) <li> Slightly lower score compared to using `igfxfw=2` <li> iGPU Freq: 1,2 GHz
5| `enable-metal`</br>`igfxfw=2`</br> `rebuild-device-tree` </br>`rps-control`| [4798](https://browser.geekbench.com/v5/compute/6259133) | <ul> <li> Same result as Test #3 <li> iGPU Freq.: 1,2 GHz

**Conclusions**: 

- Test #2 produced the best result. But it's not really a significant increase in performance compared to the default configuration used in test #1. It basically runs the iGPU at a higher base frequency so the energy consumption is higher as well. It also seems that this setting is uncapped in terms of max frequency. This resulted in my system crashing sometimes when watching YouTube because the iGPU frequency went through the roof. So you might consider not using `igfxfw=2`.
- Results of Tests #3 and #5 are virtually identical. That's because `igfxfw=2` takes precedence over `rps-control`, so you shouldn't combine these two properties!

### Enabling Resizable BAR (optional)

- Check if your GPU supports resizable BAR – you can use GPU-Z in Windows for that. The RX 580 I am using does support it
- In your config, change the following settings:
	- `Booter/Quirks`: change `ResizeAppleGpuBars` from `-1` to `0` which will update GPU registers to their defaults when booting macOS
	- `UEFI/Quirks`: Leave `ResizeGpuBars` at `-1` so other OSes are not affected by this change
- Save your config and reboot
- Enter the BIOS 
- Change the following Options:
	- Enable `Above 4G Decoding` (otherwise the Resizable BAR option is not available)
	- Change Resizable BAR mode to: `Auto`

**NOTES**: 

- Enabling resizable BAR slightly increases performance (approx. 10 %). 
- [About Resizable BAR](https://github.com/5T33Z0/OC-Little-Translated/tree/main/11_Graphics/GPU/GPU-BAR_Size)

### AMD GPUs and different SMBIOSes
If you have an AMD GPU and want to benefit from improved performance of Polaris, Vega and (Big) Navi and cards, you can switch to SMBIOS `iMacPro1,1` or `MacPro7,1` instead. Since these Macs don't have an iGPU, tasks like  Quick Sync Video and HEVC encoding are then handled by the GPU instead. More details about choosing the right SMBIOS can be found [**here**](https://caizhiyuan.gitee.io/opencore-install-guide/extras/smbios-support.html#how-to-decide)

**Mind the following**:

- Additional [config edits](https://github.com/5T33Z0/OC-Little-Translated/tree/main/11_Graphics/GPU/AMD_Radeon_Tweaks#config-edits) are required when switching the SMBIOS, so that the card works as intended.
- If you are using CPUFriend, you have to generate a new `CpuFriendDataprovider.kext` with CPUFriendFriend and replace the previously used on tp adapt CPU Power Management to the new SMBIOS.

#### Addressing DRM issues with AMD GPUs in macOS 11 and newer
The `shikigva` boot-arg previously used to [**address DRM issues**](https://github.com/acidanthera/WhateverGreen/blob/master/Manual/FAQ.Chart.md) is no longer supported in macOS Monterey and newer. 

Instead, **'unfairgva=x'** (x = number from 1 to 7) must be used now. It's a bitmask containing 3 bits (1, 2 and 4) which can be combined to enable different features (and combinations thereof) as [**explained here**](https://www.insanelymac.com/forum/topic/351752-amd-gpu-unfairgva-drm-sidecar-featureunlock-and-gb5-compute-help/)

### Using NVIDIA Kepler Cards in macOS 12 and newer
Apple removed support for NVIDIA GeForce Cards from macOS Monterey beta 7 onward. So users with NVIDIA Cards of the Kepler family (GTX 700, etc.) need to reinstall them in post-install using [**OpenCore Legacy Patcher**](https://github.com/dortania/OpenCore-Legacy-Patcher).

#### Preparation
Change the following settings in config.plist:

- `Misc/Security/SecureBootModel` Set to `Disabled` &rarr; Required to load NVIDIA drivers – otherwise the system would crash
- `Kernel/Add`: Enable `RestrictEvents.kext` &rarr; This enables the VMM board-id spoof. Required for [**OTA System Updates**](https://github.com/5T33Z0/OC-Little-Translated/tree/main/S_System_Updates) to work with SIP disabled
- `csr-active-config` to `03080000` for installing the drivers

**NOTE**: This process breaks incremental (delta) updates. So each time a System Update is available, it will download the full Installer (12+ GB)! And once the installation has finished, you will have to re-install the NVIDIA drivers again. OCLP will ask you if you want to patch them in again.

## CPU Benchmark
![image](https://raw.githubusercontent.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/main/Pics/BigSur_Benchmark.png)</br>
[**SEE ALL RESULTS**](https://browser.geekbench.com/v5/cpu/5386949)

## Credits and Thank yous
- Acidanthera for [OpenCore Boot Manager](https://github.com/acidanthera/OpenCorePkg) and kexts
- Chris1111 for [GeForce Kepler Patcher](https://github.com/chris1111/Geforce-Kepler-patcher)
- [Corpnewt](https://github.com/corpnewt) for ProperTree, SSDTTime and CPUFriendFriend
- daliansky for [OC Little ACPI Hotpatch Collection](https://github.com/5T33Z0/OC-Little-Translated) 
- Dortania for [OpenCore Install Guide](https://dortania.github.io/OpenCore-Install-Guide/) and [OpenCore Legacy Patcher](https://github.com/dortania/OpenCore-Legacy-Patcher)
- jsassu20 for [MacDown](https://macdown.uranusjr.com/) Markdown Editor
- [SL-Soft](https://www.sl-soft.de/en/software/) for Kext Updater and ANYmacOS
