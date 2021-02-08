# Gigabyte Z490 Vision G Hackintosh OpenCore

## About
This is my EFI Folder for the Z490 Vision G Board I've worked on and refined since September 2020 using the OpenCore Install Guide by Dortania as well as Daliansky's repo with ACPI Hotpatches for OpenCore to create custom SSDTs to tweak and fine tune the config.

It's configured for and tested with High Sierra, Catalina, as well as the latest Version of Big Sur (11.2). It contains three configs – one for each OS. Enjoy.

## System Specs
* **BIOS**: F8c. F5 or higher is required to disable `CFG Lock`. Otherwise use Kernel Quirk `AppleXcpmCfgLock` to disable CFG Lock via `config.plist`
* **CPU**: Intel® i9 10850K (Codename Comet Lake)
* **RAM**: 32 GB DDR4 2400 Crucial Basllistix Sport LT
* **iGPU**: Intel® UHD 630. Configured `headless` for computational tasks only. If you need to drive a display [use this Framebuffer-Patch](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/Intel%20UHD%20630_HDMI_DP_Framebuffer-Patch.plist) instead
* **GPU**: Nvidia GeForce 760. AMD users need to add boot-arg `agdpmod=pikera`
* **On-board Audio**: Realtek® ALC1220-VB codec
* **LAN**: Intel® 2.5GbE LAN chip. Not working in High Sierra, since HS doesn't support it
 
## OpenCore Infos/Configuration
* **Version**: 0.6.7 Nightly (details in config.plist)
* **Compatible macOS**: 10.13.6 (High Sierra), 10.15.7 (Catalina) and 11.2 (Big Sur)
* **System Definition:** `iMac20,2` (SMBIOS Infos need to be added with [GenSMBIOS](https://github.com/corpnewt/GenSMBIOS))
* **ACPI Patches:** `SSDT-EC`, `SSDT-AWAC`, `SSDT-EC-USBX`, `SSDT-PLUG`, `SSDT-SBUS-MCHC`, `SSDT-DMAC`, `SSDT-PPMC`, `SSDT-MEM2`
* **OpenCanopy**: `yes`
* **Iconset**: `modern`
* **Chime**: `no`
* **FileVault**: `no`
* **Tools**: `OpenShell`, `CleanNVRAM`
* **USB Ports Mapped:** `yes`. Details [here](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/USB_Ports.zip?raw=true)
* **car-active-config:** HighSierra: `FF030000`, Catalina: `FF070000`, Big Sur: `FF0F0000`
* **Issues**: High Siera requires SMBIOS `iMac18,3` as well as a Fake CPU-ID in order to boot.

## Kexts
The following Kexts are disabled by default:

* `CPUFriend.kext`
* `CPUFriendDataProvider.kext` (create your own if you use a differen CPU moodel using [CPUFriendFriend](https://github.com/corpnewt/CPUFriendFriend), replace it and reenable the kext as well as `CPUFriend.kext`)
* `FakePCIID_Intel_HDMI_Audio.kext` If you use Audio over HDMI enable this

## Install Instructions
1. Download latest EFI Release and unpack it
2. Select the config of your choice (either High Sierra, Catalina or Big Sur) and rename it to `config.plist`
3. Users of AMD Graphics cards should add their required boot-args
4. Create or Copy over SMBIOS Infos for `iMac20,2` or `iMac18,3` (for High Sierra)
5. Copy the EFI Folder on a FAT32 formated USB Stick
6. Reboot from USB Stick
7. Perform NVRAM Reset
8. Reboot from USB Stick again
9. Start macOS
10. If your System boots, mount your Systems ESP and copy the EFI Folder over to you HDD

## Optimzing CPU Power Management
Use [CPUFriendFriend](https://github.com/corpnewt/CPUFriendFriend) to generate a `CPUFriendDataProvider.kext` to optimize the CPU Power Management of your CPU for a more efficent overall performance. You can [follow this Guide](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/Optimizing%20CPU%20Power%20Management_de.pdf) to create your own (currently in german, english version will follow soon.)

## Benchmark

![image](https://raw.githubusercontent.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/main/BigSur%20Benchmark.png)

[Source](https://browser.geekbench.com/v5/cpu/5386949)

## Credits and Thank you's
* Acidanthera and Team for the OpenCore Bootloader
* Dortantia for [OpenCore Install Guide](https://dortania.github.io/OpenCore-Install-Guide/)
* Daliansky for [OC Little Repo](https://github.com/daliansky/OC-little) containing all the ACPI Hotpatches for OpenCore
* [Corpnewt](https://github.com/corpnewt) for SSDTTime, GenSMBIOS and ProperTree
* jsassu20 for [MacDown](https://macdown.uranusjr.com/) Markdown Editor
* [Schmockloard](https://github.com/SchmockLord/Hackintosh-Intel-i9-10900k-Gigabyte-Z490-Vision-D) for FakePCIID.kext for LAN & HDMI Audio
* [Samuel211119](https://github.com/samuel21119/Intel-i9-10900-Gigabyte-Z490-Vision-G-Hackintosh) for initial EFI Folder
* Pavo-IM for [OC-Gen-X](https://github.com/Pavo-IM/OC-Gen-X)
