# Gigabyte Z490 Vision G Hackintosh OpenCore

**Status: Completed**

[![](https://img.shields.io/badge/Gigabyte_Z490-Vision_G-informational.svg)](https://www.gigabyte.com/Motherboard/Z490-VISION-G-rev-1x/support#support-dl-driver)
[![OpenCore](https://img.shields.io/badge/OpenCore-0.6.8-white.svg)](https://github.com/acidanthera/OpenCorePkg/releases/latest)
[![MacOS Catalina](https://img.shields.io/badge/macOS-10.15.7-white.svg)](https://www.apple.com/macos/catalina/) [![MacOS Big Sur](https://img.shields.io/badge/macOS-11.2.3-white.svg)](https://www.apple.com/macos/big-sur/)
[![release](https://img.shields.io/badge/Download-latest-success.svg)](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/releases)

## Introduction
This is my EFI Folder for the Z490 Vision G Board I've worked on and refined since September 2020 using the OpenCore Install Guide by Dortania as well as Daliansky's repo with ACPI Hotpatches for OpenCore to create custom SSDTs to tweak and fine tune the config.

Sucessfully tested with macOS High Sierra, Catalina, as well as the latest build of Big Sur (11.2.3). It contains three configs – one for each OS. Enjoy.

<details>
<summary><strong>Build Info</strong></summary>

## Build Info

| Specifications      | Details                                     |
| :------------------ | :------------------------------------------ |
| Mainboard           | Gibabyte Z490 Vision G                      |
| BIOS					  | F20b. F5 or higher is required to disable `CFG Lock`. Otherwise use Kernel Quirk `AppleXcpmCfgLock` to disable CFG Lock via `config.plist`|
| CPU                 | Intel® Core i9 10850K (Codename Comet Lake) |
| RAM                 | 32 GB DDR4 2400 Crucial Basllistix Sport LT |
| iGPU					  | Intel® UHD 630. Configured `headless` for computational tasks only. If you need to drive a display [use this Framebuffer-Patch](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/Intel%20UHD%20630_HDMI_DP_Framebuffer-Patch.plist) instead          |
| GPU                 | Nvidia GeForce 760.oot-args                 |
| Audio               | Realtek® ALC1220-VB (Layout-id: `28`)       |
| Ethernet            | Intel® 2.5GbE LAN chip. Incompatible with macOS High Sierra |
</details>
<details>
<summary><strong>OpenCore and Config Details</strong></summary>

## OpenCore Details
* **Version**: 0.6.8 Nightly (details in config.plist)
* **Compatible macOS**: 10.13.6 (High Sierra), 10.15.7 (Catalina) and 11.2.2 (Big Sur)
* **System Definition:** `iMac20,2` (SMBIOS Infos need to be added with [GenSMBIOS](https://github.com/corpnewt/GenSMBIOS))
* **ACPI Patches:** `SSDT-EC`, `SSDT-AWAC`, `SSDT-EC-USBX`, `SSDT-PLUG`, `SSDT-SBUS-MCHC`, `SSDT-DMAC`, `SSDT-PPMC`, `SSDT-MEM2`
* **OpenCanopy Enabled**: `yes`
* **Iconset**: `modern`
* **Chime**: `no`
* **FileVault**: `no`
* **SecureBootModel**: `j185f` (Set to `Disabled` if system won't boot)
* **USB Ports Mapped:** `yes`. Details [here](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/USB_Ports.zip?raw=true)
* **car-active-config:** HighSierra: `FF030000`, Catalina: `FF070000`, Big Sur: `67080000`
* **Issues**: High Siera requires SMBIOS `iMac18,3` as well as a Fake CPU-ID in order to boot.

## Note about Kexts
The following Kexts are disabled by default since I don't know which CPU, GPU and Audio/Video Setup you are using:

- `CPUFriend.kext` and `CPUFriendDataProvider.kext` 
	- If you use a different CPU model, create your own DataProvider Kext using [CPUFriendFriend](https://github.com/corpnewt/CPUFriendFriend), replace it and reenable the kext as well as `CPUFriend.kext`)
* `FakePCIID_Intel_HDMI_Audio.kext` – If you use Audio over HDMI, enable this
* `AGPMInjector.kext`disabled, Kext not present. Generate it or delete entry. See Section "Enable AGPM"
</details>
<details>
<summary><strong>EFI Install Instructions</strong></summary>

## EFI Install Instructions
1. Download latest EFI Release and unpack it
2. Select the config of your choice (either High Sierra, Catalina or Big Sur) and rename it to `config.plist`
3. Users of AMD Graphics cards should add their required boot-args
4. Create or Copy over SMBIOS Infos for `iMac20,2` or `iMac18,3` (for High Sierra)
5. Copy the EFI Folder on a FAT32 formated USB Stick
6. Reboot from USB Stick
7. Perform NVRAM Reset
8. Start macOS
9. If your System boots, mount your Systems ESP and copy the EFI Folder over to you HDD
</details>
<details>
<summary><strong>Post-Install Tweaks</strong></summary>

## Post-Install Tweaks

### Optimizing CPU Power Management
Use [CPUFriendFriend](https://github.com/corpnewt/CPUFriendFriend) to generate a `CPUFriendDataProvider.kext` to optimize the CPU Power Management of your CPU for a more efficent overall performance. You can [follow this Guide](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/Optimizing%20CPU%20Power%20Management_de.pdf) to create your own (currently in german, english version will follow soon.)

### Enabling Apple Graphics Power Management (`AGPM`) for dedicated GPUs (Intel and AMD)
- Generate `AGPMInjector.kext` for your GPU using [AGPMInjector](https://github.com/Pavo-IM/AGPMInjector) and 
- Copy it to `EFI\OC\Kexts`
- Enable the entry in the config.plist
- Save and reboot.
- Open [IORegistryExplorer](https://github.com/utopia-team/IORegistryExplorer/releases) and search for`PR00`. If it look like this, CPU Power Management and AGPM are working correctly:

![](https://raw.githubusercontent.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/main/AGPMEnabler.png)
</details>
<details>
<summary><strong>CPU Benchmark</strong></summary>

## Geekbench CPU Benchmark

![image](https://raw.githubusercontent.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/main/BigSur%20Benchmark.png)
[Source](https://browser.geekbench.com/v5/cpu/5386949)
</details>

## Credits
* Acidanthera and Team for the OpenCore Bootloader
* Dortantia for [OpenCore Install Guide](https://dortania.github.io/OpenCore-Install-Guide/)
* Daliansky for [OC Little Repo](https://github.com/daliansky/OC-little) containing all the ACPI Hotpatches for OpenCore
* [Corpnewt](https://github.com/corpnewt) for SSDTTime, GenSMBIOS and ProperTree
* jsassu20 for [MacDown](https://macdown.uranusjr.com/) Markdown Editor
* [Schmockloard](https://github.com/SchmockLord/Hackintosh-Intel-i9-10900k-Gigabyte-Z490-Vision-D) for FakePCIID.kext for LAN & HDMI Audio
* [Samuel211119](https://github.com/samuel21119/Intel-i9-10900-Gigabyte-Z490-Vision-G-Hackintosh) for initial EFI Folder
* Pavo-IM for [OC-Gen-X](https://github.com/Pavo-IM/OC-Gen-X)
