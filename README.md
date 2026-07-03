# Gigabyte Z490 Vision G Hackintosh OpenCore

[![BIOS](https://img.shields.io/badge/BIOS-F24a-important.svg)](https://www.gigabyte.com/Motherboard/Z490-VISION-G-rev-1x/support#support-dl-bios) [![OpenCore](https://img.shields.io/badge/OpenCore-1.0.8-cyan.svg)](https://dortania.github.io/builds/?product=OpenCorePkg&viewall=true) ![macOS](https://img.shields.io/badge/macOS-10.13–26.x-purple.svg) [![Release](https://img.shields.io/badge/Download-Latest_Release-success.svg)](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/releases/latest)

![15161753](https://user-images.githubusercontent.com/76865553/173877386-1dd1b451-5e50-46b7-9f1e-554485b3a48a.png)

## About
OpenCore EFI folder for the Gigabyte Z490 Vision G motherboard, built and maintained since September 2020. The configuration is build based on Dortania’s OpenCore Install Guide and improved by analysis of the OpenCore debug boot log and the `.ioreg` file from a real `iMac20,1`. USB ports are mapped via ACPI, so no USB kext is required. It also support SATA Hotplug (needs to be enabled per SATA port in UEFI Settings).

---

## ⚠️ Important Notes

**Before you start:**
- Use the latest BIOS version (F24a)
- **macOS Tahoe (26.x)**: AMD Polaris GPUs require disabling WhateverGreen during install. Re-enable after installation.
- **macOS 14.4+**: Disable `SecureBootModel` if upgrading from 14.3.1 causes kernel panic ([details](https://github.com/5T33Z0/OC-Little-Translated/blob/main/W_Workarounds/macOS14.4.md))
- **NVIDIA Kepler GPUs**: Require root patching with [OpenCore Legacy Patcher](https://github.com/dortania/OpenCore-Legacy-Patcher/releases) in macOS 12+

---

## 🖥️ My System Configuration

| Component | Details |
|-----------|---------|
| **Motherboard** | Gigabyte Z490 Vision G (BIOS F25) |
| **CPU** | Intel Core i9 10850K (Comet Lake) |
| **RAM** | 32 GB DDR4 2400 Crucial Ballistix Sport LT |
| **GPU** | Sapphire Radeon RX580 Nitro+ (4 GB) |
| **iGPU** | Intel UHD 630 (Headless mode) |
| **Audio** | Realtek ALC1220-VB (Layout-id: `17`) |
| **Ethernet** | Intel I225-V 2.5GbE (on-board) |
| **SMBIOS** | iMac20,2 (use iMac20,1 for i5/i7) |

> [!NOTE]
>
> If your hardware differs, you'll need to adjust the configuration accordingly.

---

## 🚀 Quick Start Guide

### 1. Download & Prepare

1. Download the latest EFI from [Releases](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/releases)
2. Extract and review the included `Changelog.md`
3. Open `config.plist` with [OCAT](https://github.com/ic005k/QtOpenCoreConfig/releases) or ProperTree

### 2. Configure Your EFI

#### Add SMBIOS Data

Navigate to `PlatformInfo/Generic`, and generate SMBIOS Ddta. Fill in:

- Model: `iMac20,2` (i9) or `iMac20,1` (i5/i7)
- Serial Number
- MLB (Board Serial)
- ROM
- UUID

> [!TIP]
>  
> If you are using ProperTree to edit the config.plist, you can use [GenSMBIOS](https://github.com/corpnewt/GenSMBIOS) to generate SMBIOS data 

#### Adjust Settings for Your Hardware

**If using a different CPU:**

- Disable `CPUFriend.kext` and `CPUFriendDataProvider.kext` initially
- Generate your own after installation (see Post-Install section) with [CPUFriendFriend](https://github.com/corpnewt/CPUFriendFriend)

**If using NVIDIA Kepler GPU (GTX 600/700):**

- Change `Misc/Security/SecureBootModel` to `Disabled`
- Enable `RestrictEvents.kext`
- Change `csr-active-config` to `03080000`

**If your BIOS doesn't support CFG Lock disable:**

- Enable `Kernel/Quirks/AppleXcpmCfgLock`

**If not dual-booting Windows:**

- You can disable `Kernel/Quirks/CustomSMBIOSGuid`

### 3. Configure BIOS Settings

**Required settings:**

| Setting | Value |
|---------|-------|
| **Tweaker Tab** | |
| Extreme Memory Profile (XMP) | Enabled (if supported) |
| VT-d | Enabled |
| Intel Speed Shift | Enabled |
| **Settings Tab** | |
| Platform Power Management | Disabled |
| ErP | Enabled |
| Internal Graphics | Enabled |
| Above 4G Decoding | Enabled |
| Re-Size BAR Support | Disabled* |
| IOAPIC 24-119 Entries | Enabled |
| Serial Port | Disabled |
| Legacy USB Support | Disabled |
| XHCI Hand-off | Enabled |
| Network Stack | Disabled |
| **Boot Tab** | |
| CFG Lock | Disabled (if available) |
| Windows 10 Features | Windows 10 or Other OS |
| CSM | Disabled |

*Enable Re-Size BAR if your GPU supports it and set `Booter/Quirks/ResizeAppleGpuBars` to `0`

### 4. Install macOS

**From macOS:** Download from App Store, [OpenCore Legacy Patcher](https://github.com/dortania/OpenCore-Legacy-Patcher), or [ANYmacOS](https://www.sl-soft.de/en/anymacos/)

**From Windows/Linux:** Follow [Dortania's guide](https://dortania.github.io/OpenCore-Install-Guide/installer-guide/)

### 5. Test Your EFI

1. Copy EFI folder to a FAT32 USB drive
2. Reboot and perform NVRAM reset (press Space in boot menu)
3. Boot from USB
4. If successful, copy EFI to your drive's ESP partition

---

## 🔧 Post-Installation

### 1. Strengthen Security

Once macOS is running, enhance security:

```
SecureBootModel: j185f (iMac20,2) or j185 (iMac20,1)
csr-active-config: 00000000 (enables SIP)
```

Update `UEFI/APFS` MinDate/MinVersion to match your macOS version ([reference](https://github.com/5T33Z0/OC-Little-Translated/tree/main/A_Config_Tips_and_Tricks#mindateminversion-settings-for-the-apfs-driver))

> [!WARNING]
>
> Have a USB backup EFI ready—these changes can prevent booting if misconfigured.

### 2. Optimize CPU Power Management

Generate a custom `CPUFriendDataProvider.kext`:

1. Download [CPUFriendFriend](https://github.com/corpnewt/CPUFriendFriend)
2. Run and follow prompts to generate the kext
3. Replace existing `CPUFriendDataProvider.kext`
4. Enable both `CPUFriend.kext` and `CPUFriendDataProvider.kext`

Full guide: [CPU Power Management](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/Additional_Files/CPU_Pwr/README.md)

### 3. Optional Tweaks

**Calculate ScanPolicy** to control boot menu items using [this generator](https://oc-scanpolicy.vercel.app/)

**Change theme** in `Misc/Boot/PickerVariant`:
- `HJebbour\GoldenGateExt` (New default theme with 144 Icons)
- `Blackosx\BsxM1` (mac M1 style)

---

## 💾 What's Included

### ACPI Tables (SSDTs)

- `SSDT-AWAC-ARTC` — Enables RTC clock
- `SSDT-EC` — Fake embedded controller
- `SSDT-PLUG` — CPU power management (not needed for macOS 12+)
- `SSDT-XHUB` — USB port mapping via ACPI
- `SSDT-USBX` — USB power properties
- `DMAR` (optional) — For VT-d compatibility with certain Wifi/BT cards

### Kexts

- **VirtualSMC** + sensors — SMC emulation
- **Lilu** — Patching engine
- **WhateverGreen** — Graphics patches
- **AppleALC** — Audio (slimmed to layout 17 only)
- **AppleIGC** — Intel I225-V ethernet
- **CPUFriend** + DataProvider — CPU power management (disabled by default)
- **RestrictEvents** — Addresses RAM warnings and fixes OTA updates (disabled by default)
- **iBridged.kext** – New kext which enables proper OTA updates when using SMBIOS from T2 Macs

---

## 🎮 GPU Configurations

### Using iGPU for Display

To use Intel UHD 630 for driving displays:

1. Enable the framebuffer in `DeviceProperties`
2. Delete the `#` from `#PciRoot(0x0)/Pci(0x2,0x0)`
3. Disable the headless framebuffer entry

### AMD GPU with iMacPro/MacPro SMBIOS

For better AMD GPU performance:

1. Switch SMBIOS to `iMacPro1,1` or `MacPro7,1`
2. Apply [required config edits](https://github.com/5T33Z0/OC-Little-Translated/tree/main/11_Graphics/GPU/AMD_Radeon_Tweaks#config-edits)
3. Regenerate CPUFriendDataProvider for new SMBIOS
4. Disable headless iGPU framebuffer

### NVIDIA Kepler Cards (GTX 600/700)

For macOS 12+:

1. Set `SecureBootModel` to `Disabled`
2. Enable `RestrictEvents.kext`
3. Set `csr-active-config` to `03080000`
4. Install drivers using [OpenCore Legacy Patcher](https://github.com/dortania/OpenCore-Legacy-Patcher)

> [!NOTE]
>
> Applying Root Patches with OpenCore Legacy Patcher modifies files on the sealed system volume, breaking Apple's cryptographic seal (Signed System Volume, SSV). Once the seal is broken, macOS can no longer verify that the system volume exactly matches the version distributed by Apple. As a result, a delta update may still be offered and downloaded, but the installation process ultimately fails because incremental updates require an unmodified baseline. After this failure, macOS downloads the full operating system installer (~18 GB), which can rebuild the system volume from a complete, trusted image rather than patching an unknown state.

## 📊 Performance

![Benchmark](https://raw.githubusercontent.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/main/Pics/BigSur_Benchmark.png)

[View full Geekbench results](https://browser.geekbench.com/v5/cpu/5386949)

---

## 🆘 Troubleshooting

### Common Issues

**Boot failure with black screen:**

- Check `SecureBootModel` setting
- Verify BIOS settings (especially CSM disabled)
- Test with `-v` boot argument for verbose mode

**No ethernet connection:**

- Ensure `AppleIGC.kext` is enabled
- Check `DisableIoMapperMapping` quirk is enabled
- For macOS Catalina, enable the I225-V device-id spoof

**No audio:**

- Check selected Audio Devices in System Settings
- Verify Layout-id is `17` in DeviceProperties
- Check `AppleALC.kext` is enabled
- For Tahoe, apply OCLP audio patch

**USB issues:**

- SSDT-XHUB handles all USB mapping
- No additional kexts needed
- See [USB port list](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/Additional_Files/USB/USB_Ports_List.pdf) for details

---

## 📚 Additional Resources

- [OpenCore Install Guide](https://dortania.github.io/OpenCore-Install-Guide/) (Dortania)
- [OC-Little Translated](https://github.com/5T33Z0/OC-Little-Translated) (ACPI hotpatches)
- [Intel 400 Series Chipset Documentation](https://cdrdv2.intel.com/v1/dl/getContent/615170)
- [System Update Workarounds](https://github.com/5T33Z0/OC-Little-Translated/tree/main/S_System_Updates)

---

## 🙏 Credits

- [Acidanthera](https://github.com/acidanthera) — OpenCore and kexts
- [Dortania](https://dortania.github.io) — OpenCore guides and OCLP
- [Corpnewt](https://github.com/corpnewt) — ProperTree, SSDTTime, CPUFriendFriend
- [daliansky](https://github.com/daliansky) — OC-Little collection
- [SL-Soft](https://www.sl-soft.de/en/software/) — Kext Updater and ANYmacOS

---

## ⚖️ Disclaimer

This configuration is provided as-is. Always backup your data before installation. Hackintosh systems are not officially supported by Apple and may have compatibility issues.
