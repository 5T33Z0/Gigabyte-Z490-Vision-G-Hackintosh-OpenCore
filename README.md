# Gigabyte Z490 Vision G Hackintosh OpenCore
[![BIOS](https://img.shields.io/badge/BIOS-F21-important.svg)](https://www.gigabyte.com/Motherboard/Z490-VISION-G-rev-1x/support#support-dl-bios) [![OpenCore Version](https://img.shields.io/badge/OpenCore-0.8.8-cyan.svg)](https://github.com/acidanthera/OpenCorePkg/releases/latest) [![macOS Catalina](https://img.shields.io/badge/macOS-10.15.7-white.svg)](https://www.apple.com/li/macos/catalina/) [![macOS Big Sur](https://img.shields.io/badge/macOS-11.7.2-white.svg)](https://www.apple.com/macos/big-sur/) [![macOS Monterey](https://img.shields.io/badge/macOS-12.6.3-white.svg)](https://www.apple.com/macos/monterey/) [![macOS Ventura](https://img.shields.io/badge/macOS-13.2_beta-white.svg)](https://www.apple.com/macos/ventura/) [![Release](https://img.shields.io/badge/Download-latest-success.svg)](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/releases/latest)</br>
![15161753](https://user-images.githubusercontent.com/76865553/173877386-1dd1b451-5e50-46b7-9f1e-554485b3a48a.png)

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
	- [Optimizing CPU Power Management (recommended)](#optimizing-cpu-power-management-recommended)
	- [Strengthen Security (recommended)](#strengthen-security-recommended)
	- [Addressing issues with DRM on AMD Cards in macOS 11+](#addressing-issues-with-drm-on-amd-cards-in-macos-11)
	- [Patching-in NVIDIA Kepler Drivers (macOS 12+ only)](#patching-in-nvidia-kepler-drivers-macos-12-only)
	- [Calculate a Scan Policy (optional)](#calculate-a-scan-policy-optional)
	- [Changing Themes](#changing-themes)
- [CPU Benchmark](#cpu-benchmark)
- [Credits and Thank yous](#credits-and-thank-yous)

## About
EFI folder and config.plist for the Gigabyte Z490 Vision G mainboard I've been working on and refining since September 2020. It's based on Dortania's OpenCore Install Guide and analysis of an `.ioreg` file from a real iMac20,1. I've dumped the system `DSDT`, analyzed it and added missing components and features via `SSDT` Hotpatches from Daliansky's "OC-Little" Repo to get it as close to a real Mac as possible. USB Ports are mapped via `ACPI`, so no USB Port kext is required. I think this is the most sophisticated Z490 Vision G EFI folder available on Github. 

This is a *genuine* Z490 Vision G EFI built from scratch. Unlike most so-called Z490 Vision G EFIs posted on Forums and Repos, which are either based on generic SSDTs by Olarila/MaLDon or on SchmockLords EFI for the Z490 Vision D, which contains unnecessary Device Properties for Tunderbolt, an I219 Ethernet Controller and on-board WiFi/BT.

Tested successfully with macOS 10.14 to 13.2.

**NOTE**: For best results, read and follow the install instruction carefully and thoroughly.

| :warning: Issues related to macOS Monterey/Ventura|
|:--------------------------------------------------|
|The Intel I225-V Ethernet Controller doesn't work in macOS 12+ by default. You need a [fix](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/I225-V_FIX.md).
|600/700-series Nvidia Cards require [OpenCore Legacy Patcher](https://github.com/dortania/OpenCore-Legacy-Patcher/releases) or [Geforce Kepler Patcher](https://github.com/chris1111/Geforce-Kepler-patcher) to reinstall Nvidia drivers.

## Hardware Info

### System Configuration

Component     | Details                                                       
-------------:|-------------------------------------------------------------- 
**Board**     | Gigabyte Z490 Vision G. **BIOS**: F21. F5 or newer is required to disable `CFG Lock`. Otherwise enable Kernel Quirk `AppleXcpmCfgLock`
**CPU**       | Intel Core i9 10850K (Comet Lake)      
**RAM**       | 32 GB DDR4 2400 Crucial Ballistix Sport LT
**iGPU**      | Intel UHD 630 (Headless). Use this [Framebuffer Patch](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/Additional_Files/Intel_UHD_630_HDMI_DP_Framebuffer-Patch.plist) if you want to use it for driving a display.
**GPU**       | Saphire RX580 Nitro+ (4 GB)
**Audio**     | Realtek® ALC1220-VB (Layout-id: `17`)
**Ethernet** (on-board) | Intel I225-V 2.5GbE. Compatible with macOS 10.15.7 and newer. Requires [flashing a custom Firmware](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/I225-V_FIX.md) for macOS Monterey
**Ethernet** (PCIe)| Intel PRO/1000 PT Dual Port Server Adapter (any macOS). Currently unused.

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

## OpenCore Details

### General Information

Parameter | Details                                                       
----------:|-------------------------------------------------------------- 
**SMBIOS** | `iMac20,2`. For i5/i7 CPUs, use `iMac20,1`. SMBIOS data needs to be generated. I use [**OCAT**](https://github.com/ic005k/OCAuxiliaryTools#readme) for this
**Supported macOS** | macOS 10.14 up to 13.x (10.14 requires `iMac19,1` config)
**OpenCanopy** | Enabled
**Boot Chime**| No
**FileVault** |`Optional`
**SIP**| `Disabled`. Adjust `csr-active-config` based on used macOS version.
**SecureBootModel**| `j185f`. For `iMac20,1`, use `j185`. :warning: Set to `Disabled` if you are usting a Kepler GPU and want to install/run macOS Monterey
**USB Port Mapping**| Yes, via ACPI. Details [**here**](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/Additional_Files/USB_Ports_List.pdf).

### EFI Folder Structure (OpenCore)

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
    │   ├── SSDT-EC-USBX.aml
    │   ├── SSDT-I225V.aml
    │   ├── SSDT-PLUG.aml
    │   ├── SSDT-PORTS.aml
    │   ├── SSDT-MCHC.aml
    │   └── SSDT-XSPI.aml
    ├── Drivers
    │   ├── HfsPlus.efi
    │   ├── OpenCanopy.efi
    │   ├── OpenRuntime.efi
    │   ├── ResetNvramEntry.efi
    │   └── ToggleSipEntry.efi
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
    │   │   ├── Blackosx
    │   │   │   └── BsxM1
    │   │   └── velickovicdj
    │   │  	└── EnterTwilight
    │   └── Label
    ├── config.plist
    └── config_iMac19,1.plist
```
</details>

## Installing/Upgrading macOS
**Coming from Windows/Linux**: If you are on Windows or Linux, follow the guide provided by [Dortania](https://dortania.github.io/OpenCore-Install-Guide/installer-guide/#making-the-installer). No support from my end for issues related to UBS Installers created in Windows or Linux.

**Coming from macOS**: If you already have access to macOS, you can either download macOS from the App Store, via the [**OpemCore Legacy Patcher**](https://github.com/dortania/OpenCore-Legacy-Patcher) App or use [**ANYmacOS**](https://www.sl-soft.de/en/anymacos/). Botch can download macOS High Sierra to Ventura and create a USB Installer as well.

## Deployment
Please read the following explanations carefully and follow the given instructions. In order to boot macOS with this EFI successfully, adjustments to the `config.plist` and used kexts may be necessary to adapt the config to your system configuration and the macOS version you want to install/run. 

### Preparing the `config.plist`
Download the EFI Folder from the [**Releases**](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/releases) section and unpack it. Make sure to check the included `Changelog.md` as well, since it contains useful explanations. 

Select the config of your choice and rename it to `config.plist`. Open it with [**OCAT**](https://github.com/ic005k/QtOpenCoreConfig/releases) or a Plist Editor of your choice and check the following sections amd settings and adjust them to your needs:

1. **ACPI/Add** Section 
	- Enable/Disable ACPI tables as needed
	- Some Notes about some of the extra tables:
		- **DMAR** (optional): DMAR replacement table without Reserved Memory Regions. Useful in cases where 3rd party LAN/Wifi/BT cards won't work when the Intel I225-V controller is enabled (macOS Big Sur and newer).
		- **SSDT-AWAC-ARTC**: Special variant of `SSDT-AWAC.` Disables AWAC Clock and enables RTC as ARTC instead. Also disables legacy `HPET` device.
		- **SSDT-PORTS**: OS-agnostic USB Port Mapping Table for the Z490 Vision G. No additional USB Port kext or quirks are required. Since the USB ports are mapped via ACPI they will work in *any* version of macOS. Check [this pdf](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/Additional_Files/USB_Ports_List.pdf) for a detailed list of mapped ports.
		- **SSDT-PLUG.aml**: Not required on macOS 12 and newer. Also not needed when using CPUFriend.kext and the CPUFriendDataProvider.kext
		- **SSDT-XSPI** (optional, enabled): Adds PCH SPI Controller to IORegistry as `XSPI`. So it's not a fake device but probably only a cosmetic change.

	**NOTE**: More info about additional ACPI Tables can be found on my [**OC Little Repo**](https://github.com/5T33Z0/OC-Little-Translated/tree/main/01_Adding_missing_Devices_and_enabling_Features)

2. **ACPI/Delete** Section
	- Drop OEM DMAR Table &rarr; Only enable if you need to use the DMAR replacement table
	- Drop OEM USB Port Map (xh_cmsd4) &rarr; Drops the original USB Port map so SSDT-PORTS.aml can replace it
	- Drop HPET Table &rarr; Drops the table for the legacy High Precision Event Timer

3. **Booter** Section
	- **Booter/MMIOWhitelist**
		- I added these memory regions after analyzing the bootlog. Since I don't know if these are used by all systems, I disabled them and the corresponding `DevirtualiseMmio` Quirk.
		- To figure out which ones your system uses, you can [follow this guide](https://github.com/5T33Z0/OC-Little-Translated/tree/main/12_MMIO_Whitelist)
		- This is not a necessity, just some fine-tuning.
	- **Booter/Patch**: These patches are used in OpenCore to skip the board-id check as part of a [workaround](https://github.com/5T33Z0/OC-Little-Translated/tree/main/S_System_Updates) if System Update Notifications are not working. Only needed when using NVIDIA Kepler Card which requires disabling `SecureBootModel` and `SIP` which.

4. **DeviceProperties**
	- `#PciRoot(0x0)/Pci(0x1C,0x1)/Pci(0x0,0x0)` &rarr; Disabled device-id spoof for the Intel I-225V. &rarr; Only required when running macOS Catalina! Delete the `#` to enable it. Requires `Kernel/Patch` as well. [Read this](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/I225_stock_vs_cstmfw.md#readme) for getting the Intel(R) I225-V Ethernet Controller to work on different versions of macOS.
	- `#PciRoot(0x0)/Pci(0x2,0x0)` &rarr; Disabled Framebuffer for using the iGPU for driving a monitor. Delete the `#` to enable it.
	- `PciRoot(0x0)/Pci(0x1F,0x3)` &rarr; Settings for on-board audio. Also contains the Layout-id
	- `PciRoot(0x0)/Pci(0x2,0x0)` &rarr; Headless Framebuffer for using a discrete GPU for display and the iGPU for computational tasks.

5. **Kernel/Add** Section. The following Kexts are disabled by default since I don't know which CPU, GPU, Hard Disk and SMBIOS you will be using:

	- `CPUFriend.kext` and `CPUFriendDataProvider.kext`. Create your own CPUFriendDataProvider with [**CPUFriendFriend**](https://github.com/corpnewt/CPUFriendFriend) as explained [**here**](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/Additional_Files/Optimizing_CPU_Power_%20Management.md), replace the existing one and enable both
	- `NVMeFix.kext`: recommended for all 3rd party NVMe SSD drives
	- `FeatureUnlock`: see comments for details
	- `RestrictEvents`: 
		- Required when using `MacPro7,1` SMBIOS. Needs `revpatch=auto` boot-arg &rarr; Disables warnings about unpopulated RAM slots.
		- Required to enable the `VMM-x86_64` so OTA updates work when `SecureBootModel` is  set to `Disabled` (necessary when using NVIDIA Kepler Cards in macOS 12+).
	- `AppleALC.kext`: Slimmed version which only contains Layout `17`

6. **Kernel/Quirks**: If your BIOS does not provide the option to disable CFG Lock, either update it or enable `AppleXcpmCfgLock` instead. In this case you also need to enable RestrictEvents.kext to the 

7. **Misc/Security**: 
	- `SecureBootModel`: 
		- Set to to `j185` (for iMac20,1) or `j185f` (for iMac20,2).
		- Set to `Disabled` if you are using an NVIDIA Kepler Card. Otherwise the system will crash on boot. In this case you also need to enable `RestrictEvents.kext` to enable the `VMM-x86_64` board-id so OTA updates will work. More details [**here**](https://github.com/5T33Z0/OC-Little-Translated/tree/main/S_System_Updates). 

8. **NVRAM/Add**
	- `4D1FDA02-38C7-4A6A-9CC6-4BCCA8B30102`: 
		- revpatch:sbvmm &rarr; Setting for RestrictEvents.kext to enable the board-id VMM spoof.
		- OCLP-Settings: -allow_amfi &rarr; Required for OpenCore Legacy Patcher so Kepler Drivers can be installed
	- `7C436110-AB2A-4BBB-A880-FE41995C9F82`: 
		- Change `csr-active-config` to disable SIP. When using OCL or GeForce Kepler Patcher, you _have_ to disable SIP):
			- **Big Sur** and newer: `67080000` (0x867) or `EF0F0000` (0xFEF) if you need to completely disable SIP (required for GeForce Kepler Patcher)
			- **Mojave/Catalina**: `EF070000` (0x7EF)
			- **High Sierra**: `FF030000` (0x3FF)
	
		:warning: **IMPORTANT**

		- Using csr-active-config `EF0F0000` also disables incremental system updates on macOS 11 and newer. So every time an update is available, the *full* installer will be downloaded. To avoid this, either enable SIP temporarily from the OpenCore GUI or use `67080000` instead.</br>
		- AMD GPUs may require additional `boot-args`. Check [WhateverGreen's](https://github.com/acidanthera/WhateverGreen#boot-arguments) documentation for details.

9. **PlatfotmInfo/Generic** Section: 
	- Generate SMBIOS data for `iMac20,1` (for Core i9) or `iMac20,2` (for Core i5/i7)
10. Save the `config.plist`

### Testing the EFI
1. Copy the EFI Folder to a FAT32 formatted USB flash drive for testing
2. Reboot
3. Perform an NVRAM Reset (hit Space Bar to reveal Tools)
4. Boot macOS from the USB flash drive
5. If your system boots successfully, mount your ESP and copy over the EFI Folder to you HDD/SSD and reboot.
6. Continue with Post-Install!

## Post-Install

### Optimizing CPU Power Management (recommended)
You can follow my [guide](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/Additional_Files/Optimizing_CPU_Power_%20Management.md) to use [CPUFriendFriend](https://github.com/corpnewt/CPUFriendFriend) to generate a `CPUFriendDataProvider.kext` which works alongside `CPUFriend.kext` to optimize CPU Power Management for a more efficient performance. Have a look at the CPU behavior using Intel Power Gadget. The CPU idle frequency should be lower after adding the kexts:

<details><summary><strong>Screenshot</strong> (click to reveal)</summary>

![image](https://raw.githubusercontent.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/main/Pics/CPU_PM.png)
</details>

### Strengthen Security (recommended)
Once you got macOS running, you should change the following settings to make your system more secure:

1. Change `csr-active-config` to `00000000` to enable System Integrity Protection (SIP)
2. Under `UEFI/APFS`, change `MinDate` and `MinVersion` from `-1` (disabled) to the correct values for the macOS version you are using. A list with the correct values for can be found [here](https://github.com/5T33Z0/OC-Little-Translated/tree/main/A_Config_Tips_and_Tricks#mindateminversion-settings-for-the-apfs-driver).</br>
3. Change `SecureBootModel` from `Disabled` to `j185` (for iMac20,1) or `j185f` (for iMac20,2). 

:warning: You should have a working backup of your EFI folder on a FAT32 formatted USB flash drive since changing these settings can prevent the system from booting. You may have to disable them for installing macOS Monterey if you have issues.

**NOTES**

- `SecureBootModel` is only applicable to macOS Catalina and newer.
- `SIP` must stay disabled when pathing in NVDIA Kepler drivers!
- Since SMBIOS `iMac20,x` is for an iMac with a T2 Security Chip, you won't be notfied about System Updates if `SecureBootModel` is `Disabled`. To workaround this either select the correct `SecureBootModel` for your SMBIOS or enable the following in the `config.plist`:
	- **Booter/Patch**: 
		- Enable `Skip Board ID`
		- Enable `Reroute HW_BID to OC_BID`
	- **Kernel/Add**: Enable `RestrictEvents.kext`

### Addressing issues with DRM on AMD Cards in macOS 11+
The `shikigva` boot-arg previously used to [**address DRM issues**](https://github.com/acidanthera/WhateverGreen/blob/master/Manual/FAQ.Chart.md) is no longer supported in macOS 11 and newer. 

Instead **'unfairgva=x'** (x = number from 1 to 7) must be used now. It's a bitmask containing 3 bits (1, 2 and 4) which can be combined to enable different features (and combinations thereof) as [explained here](https://www.insanelymac.com/forum/topic/351752-amd-gpu-unfairgva-drm-sidecar-featureunlock-and-gb5-compute-help/)

### Patching-in NVIDIA Kepler Drivers (macOS 12+ only)
Apple removed support for NVIDIA GeForce Cards from macOS Monterey beta 7. So users of with NVIDIA cards Kepler family (GTX 700, etc) need to reinstall them in post-install using [**OpenCore Legacy Patcher**](https://github.com/dortania/OpenCore-Legacy-Patcher) (recommended) or [**Geforce-Kepler-Patcher**](https://github.com/chris1111/Geforce-Kepler-patcher).

**Requirements**

- Enable both `Booter/Patches`
- Set `Misc/Security/SecureBootModel` to `Disabled`
- Enabled `RestrictEvents.kext` &rarr; this enables the VMM board-id spoof. Required so System Updates work afterwards. Details [here](https://github.com/5T33Z0/OC-Little-Translated/tree/main/S_System_Updates)
- You may have to change `csr-active-config` to `EF0F0000` for installing NVDIA drivers. Afterwards you can revert it back to `67080000`

### Calculate a Scan Policy (optional)
The items displayed in the Boot Picker menu are based on a combination of bits representing supported devices (SATA, NVME, USB, etc.) and file systems (APFS, HFS, NTFS, etc.). There are 24 bits which can be turned on and off to modify what's displayed in the Boot Picker. The combination of selected bits create what's called the `ScanPolicy`. It's located under in the `config.plist` under `Misc/Security`. The default value of my EFI is `0` (everything). Although this is great for compatibility, it will also display EFI Folders on drives which are not the boot drive as well.

To change the `ScanPolicy` to your liking, you can use the [**OpenCore ScanPolicy Generator**](https://oc-scanpolicy.vercel.app/). I am using `2687747` for example which hides EFI Folders and NTFS Drives. To add a custom entry for a Windows Disk to OpenCore's Boot Picker [follow my guide](https://github.com/5T33Z0/OC-Little-Translated/blob/main/I_Windows/Custom_Entries.md). Otherwise you can just boot Windows from the BIOS Boot Menu (F12) which also bypasses all the OpenCore injections.

:warning: **IMPORTANT**: Calculating a wrong `ScanPolicy` can lead to the Boot Picker being empty, so you can't boot into macOS. So make sure you have a working Backup of your EFI folder.

### Changing Themes
Besides the included themes from Acidanthera which provide the standard macOS look and feel, I've added 2 additional ones: `BsxM1` (default) and `EnterTwilight`. To change them, do the following:

- Open `config.plist`
- Go to Misc/Boot and change `PickerVariant` to: `Blackosx\BsxM1` or `velickovicdj\EnterTwilight`
- Save and reboot

To revert the changes, enter `Acidanthera\GoldenGate` as `PickerVariant` and change the Flavour of the NVRAM Reset Tool back to `Auto`.

**NOTE**: For more config tips and tricks, you can check out [this](https://github.com/5T33Z0/OC-Little-Translated/tree/main/A_Config_Tips_and_Tricks).

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
