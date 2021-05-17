# Gigabyte Z490 Vision G Hackintosh OpenCore

**5T4TU5**: Completed 100%. Added Clover EFI as well. 

[![Board](https://img.shields.io/badge/Gigabyte-Z490_Vision_G-informational.svg)](https://www.gigabyte.com/Motherboard/Z490-VISION-G-rev-1x/support#support-dl-bios)
[![OpenCore Version](https://img.shields.io/badge/OpenCore-0.7.0-important.svg)](https://github.com/acidanthera/OpenCorePkg/releases/latest)
[![Clover Version](https://img.shields.io/badge/Clover-r5135-important.svg)](https://github.com/CloverHackyColor/CloverBootloader/releases/tag/5134)
[![MacOS Catalina](https://img.shields.io/badge/macOS-10.15.7-white.svg)](https://www.apple.com/li/macos/catalina/)
[![MacOS Big Sur](https://img.shields.io/badge/macOS-11.3.1+-white.svg)](https://www.apple.com/macos/big-sur/)
[![release](https://img.shields.io/badge/Download-latest-success.svg)](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/releases)
![](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/Pics/BootPicker.png)

## Introduction

This is my EFI Folder for the Z490 Vision G Board I've been working on and constantly refining since September 2020, based on Dortania's OpenCore Install Guide, ACPI Hotpatches from Daliansky's "OC-Little" Repo and my own research. I dumped the system `DSDT`, analyzed it and added missing components to tweak and fine tune the config and build. This system is used as a workstation for music production and sound design exclusively. No funny business like taking Schmocklords Vision D EFI, relabeling and reposting it as a "Vision G EFI" going on here – unlike in some Forums, Githubs and the Hackindrom App especially. 

My EFI doesn't require `FakePCIID.kext` to get the Intel I225 2.5 Gigabit Ethernet Controller working. It also doesn't require `SSDT-USBW.aml` – which doesn't do anything besides turning the screen back on with one instead of two mouse/keyboard inputs anyway. This is without a doubt the most sophisticated Z490 Vision G EFI you can find on Github to date! And just for fun, I added Clover, too.

Sucessfully tested with macOS High Sierra, Catalina and Big Sur (11.3.1). It contains 2 configs: one for Catalina/BigSur, one for HighSierra/Legacy OSes.

**NOTE**: For more Post-Install goodies, check out my small collection of [Config Tweaks](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/Additional%20Files/OpenCore_Config_Tweaks_EN.md)

## Build Info

<details>
<summary><strong>Hardware Components</strong></summary>

| Component           | Details                                     |
| :------------------ | :------------------------------------------ |
| Mainboard           | Gigabyte Z490 Vision G                      |
| BIOS		      | F20. F5 or higher is required to disable `CFG Lock`. Otherwise use Kernel Quirk `AppleXcpmCfgLock`|
| CPU                 | Intel® Core i9 10850K (Codename Comet Lake) |
| RAM                 | 32 GB DDR4 2400 Crucial Basllistix Sport LT |
| iGPU		      | Intel® UHD 630. Configured `headless` for computational tasks only. If you need to drive a display [use this Framebuffer-Patch](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/Additional%20Files/Intel%20UHD%20630_HDMI_DP_Framebuffer-Patch.plist) instead      |
| GPU                 | Nvidia GeForce 760	                    |
| Audio               | Realtek® ALC1220-VB (Layout-id: `28`)       |
| Ethernet            | Intel® 2.5GbE LAN chip. Incompatible with macOS High Sierra |
</details>
<details>
<summary><strong>OpenCore and Config Details</strong></summary>
	
### OpenCore Details

* **Version**: 0.7.0 Nightly (details in config.plist)
* **Compatible macOS**: ~~10.13.6 (High Sierra)~~, 10.15.7 (Catalina) and 11.3+ (Big Sur)
* **System Definition:** `iMac20,2` (SMBIOS Infos need to be added with [GenSMBIOS](https://github.com/corpnewt/GenSMBIOS)). Using a divergent SMBIOS rather than `iMac20,2` may require remapping of USB Ports, since the `info.plist` inside the `USBPorts.kext` refers to `iMac20,2` as the `model` which the port mappings are for.
* **ACPI Patches:** `SSDT-AWAC`, `SSDT-EC-USBX`, `SSDT-PLUG`, `SSDT-SBUS-MCHC`, `SSDT-DMAC`, `SSDT-PPMC`
* **OpenCanopy Enabled**: `yes`
* **Iconset**: `modern`
* **Chime**: `no`
* **FileVault**: `no`
* **SecureBootModel**: `j185f`(change it to `Disabled` if your system won't boot)
* **USB Ports Mapped:** `yes`. Details [here](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/Additional%20Files/USB_Ports.zip)
* **car-active-config:** HighSierra: `FF030000`, Catalina: `FF070000`, Big Sur: `67080000`
* **Issues**: High Siera requires SMBIOS `iMac18,3` as well as a Fake CPU-ID in order to boot. High Sierra has been scrapped, since it seems to break the Big Sur seal: after using High Sierra if you reboot you can no longer start Big Sur without `-no_compat_check` boot-arg. You get a stop sign instead.
* **Config Validation**: ![](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/Pics/ConfigValidation.png)

### Note about Kexts
The following Kexts are disabled by default since I don't know which CPU, GPU and Audio/Video Setup you are using:
- `CPUFriend.kext` and `CPUFriendDataProvider.kext` 
	- If you use a different CPU model, create your own DataProviderKext using [CPUFriendFriend](https://github.com/corpnewt/CPUFriendFriend), replace it and reenable the kext as well as `CPUFriend.kext`)
* `AGPMInjector.kext`disabled, Kext not present. Generate it or delete entry. See Section "Enable AGPM"
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

### EFI Guide
	
1. Download latest EFI Release and unpack it
2. Select the config of your choice and rename it to `config.plist`
3. choose `csr-active-config` based on macOS version to disable SIP: `67080000` for Big Sur, `FF070000` for Catalina/Mojave, `FF030000` for High Sierra
4. Users of AMD GPUs may have to add additional `boot-args`
5. Create or Copy over SMBIOS Infos for `iMac20,2` or `iMac18,3` (for High Sierra)
6. Copy the EFI Folder on a FAT32 formated USB Stick
7. Reboot from USB Stick
8. Perform NVRAM Reset
9. Start macOS
10. If your System boots, mount your ESP and copy the EFI Folder over to you HDD and reboot.
11. Continue with Post-Install

</details>
<details>
<summary><strong>Post-Install Tweaks</strong></summary>
	
### Optimizing CPU Power Management
Use [CPUFriendFriend](https://github.com/corpnewt/CPUFriendFriend) to generate a `CPUFriendDataProvider.kext` to optimize the CPU Power Management of your CPU for a more efficent overall performance. You can [follow this Guide](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/Additional%20Files/Optimizing%20CPU%20Power%20Management_de.pdf) to create your own (currently in german, english version will follow at some point).
When you're done, reboot. Have a look at the CPU behavior using Intel Power Gadget. You can see, that the CPU idle frequency should be lower now:

![image](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/Pics/CPU_PM.png)

### Enabling Apple Graphics Power Management (`AGPM`) for dedicated GPUs (Intel and AMD)
- Generate `AGPMInjector.kext` for your GPU using [AGPMInjector](https://github.com/Pavo-IM/AGPMInjector) and 
- Copy it to `EFI\OC\Kexts`
- Enable the entry in the config.plist
- Save and reboot.
- Open [IORegistryExplorer](https://github.com/utopia-team/IORegistryExplorer/releases) and search for`PR00`. If it look like this, CPU Power Management and AGPM are working correctly:

![](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/Pics/AGPMEnabler.png)
</details>

## CPU Benchmark
![image](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/Pics/BigSur%20Benchmark.png)

[SEE ALL RESULTS](https://browser.geekbench.com/v5/cpu/5386949)

## Credits
* Acidanthera and Team for the OpenCore Bootloader
* Dortantia for [OpenCore Install Guide](https://dortania.github.io/OpenCore-Install-Guide/)
* Daliansky for [OC Little Repo](https://github.com/5T33Z0/OC-Little-Translated) containing all the ACPI Hotpatches for OpenCore
* [Corpnewt](https://github.com/corpnewt) for SSDTTime, GenSMBIOS and ProperTree
* [Pavo-IM](https://github.com/Pavo-IM/) for APGM Enabler
* jsassu20 for [MacDown](https://macdown.uranusjr.com/) Markdown Editor
