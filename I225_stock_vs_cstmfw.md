# Conditions for Intel I225-V and 3rd party WiFi/LAN cards to work

I noticed that some users have issues with getting both 3rd party WiFi and Ethernet cards to work if they dropped the `DMAR` table and replaced it with a modified one without reserved memory regions. The DMAR table of the Z490 Vision G contains 2 reserved memory regions – one of theme is for the XHCI Controller which handles USB. 

I've read in a support thread for the Aquantia 10 Gigabit Ethernet Card that it worked when the memory region for the XHCI controller (**PCI Path: 14,00**) remained in the DMAR table. So I conducted some tests with 3 variants of the DMAR table: one without memory regions and two more with either one or the other memory region removed. It turns out that: 

- If you've flashed the custom firmware for the Intel I225-V Controller, it doesn't matter if you drop, replace or keep the stock `DMAR` table – it will work, as long as `Vt-d` is enabled and `DisableIoMapper` is set to `false`. 3rd Party Wifi/Ethernet cards may still require aforementioned memory regions, so it's best to not drop/replace the DMAR table!
- When using the stock firmware, it's a different story. 

Below you find the results of my tests and recommended settings for different scenarios.

:warning: Please use the latest [release](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/releases) of my EFI folder before reporting any issues.

## Enabling the Intel I225-V Ethernet Controller

|macOS |Procedure|
|-------------|---------|
**12 and 13 beta**| **Prerequisites**: [Fix the Controller](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/I225-V_FIX.md). Once that's done, change the following settings:<ol><li>**ACPI/Add** &rarr; Disable `DMAR.aml` <li> **ACPI/Delete** &rarr; Disable `Drop OEM DMAR Table`<li>**DeviceProperties** &rarr; Disable `PciRoot(0x0)/Pci(0x1C,0x1)/Pci(0x0,0x0)` (put a hash symbol `#` in front of it)<li>**Kernel/Quirks** &rarr; Uncheck `DisableIOMapper`<li>**Boot-args** &rarr; Disable/Delete `dk.e1000=0` and/or `e1000=0` (put `#` in front of them)</ol>**NOTE**: This is the default configuration. If you are using the modded firmware and my config, you don't have to change anything here!
**11.4+**|<ol><li>**DeviceProperties** &rarr; Disable `PciRoot(0x0)/Pci(0x1C,0x1)/Pci(0x0,0x0)`(put `#` in front of it)<li>**Boot-args** &rarr; add `dk.e1000=0`<li>Save and reboot
**10.15 to 11.3**|<ol><li>Enable `#PciRoot(0x0)/Pci(0x1C,0x1)/Pci(0x0,0x0)` (delete `#`)<li> Go to `Kernel/Patch` and enable `I225-V Patch`.<li> Delete/disable boot-arg `dk.e1000=0`<li>Save and reboot.
**10.14 and older**| Not compatible. You need a third party Ethernet Card!

### Settings combinations, custom firmware:

macOS|Vt-D|DisableIoMapper |DMAR (OEM) |DMAR (dropped/replaced) |I225-V / 3rd party LAN/WiFi|
:----|:------:|:--------------:|:---------:|:--------------:|:---------------:
**11.4 to 13.0**  | **ON** |**OFF**| **YES**| **NO / NO**| **YES / YES**
**11.4 to 12.5**  | ON| OFF| NO| YES / YES| YES/ NO
**11.4 to 12.5**  | OFF/ON |ON| NO| YES / YES| NO / YES
**10.15 to 11.3** | OFF/ON |ON| YES| NO / NO| **YES / YES**

**NOTES**:

- On macOS 12 and newer, [dropping DMAR table](https://github.com/5T33Z0/OC-Little-Translated/tree/main/00_ACPI/ACPI_Dropping_Tables#example-1-dropping-the-dmar-table) and [replacing it](https://github.com/5T33Z0/OC-Little-Translated/tree/main/00_ACPI/ACPI_Dropping_Tables#example-2-replacing-the-dmar-table-by-a-modified-one) by a modified one without Reserved Memory Regions may be required for Intel WiFi cards to work.

### Settings combinations, stock firmware

macOS|Vt-D|DisableIoMapper|DMAR (OEM)|DMAR (dropped/replaced)| I225-V / 3rd Party party LAN/WiFi
:-----|----|:-------------:|:--------:|:---------------------:|:-----------------:
**12.5+** (Fix [Opt. 1](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/I225-V_FIX.md#option-1-using-a-ssdt-with-corrected-header-description))| ON |**OFF**| **YES**| **NO / NO**| **YES / YES**
**12.5+** (stock fw) | ON |**OFF**| **YES**  | **NO / NO**| **NO / YES**
**11.4 to 11.6.7**| ON | ON [^1]| NO| YES / YES| [**YES / YES**](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/issues/19#issuecomment-1153315826)
**10.15 to 11.3** | OFF/ON|OFF/ON| YES | NO / NO | **YES / NO**

If you can't access the Internet after applying the settings, remove the following files via Terminal and reboot:

- `sudo rm /Library/Preferences/SystemConfiguration/NetworkInterfaces.plist`
- `sudo rm /Library/Preferences/SystemConfiguration/preferences.plist` 

[^1]: Combining `Vt-D` and `DisableIOMapper` makes no sense to me in regards to getting the I225-V working in macOS but that's what the user reported as working.

## Intel I225-V and macOS Ventura
In macOS Ventura, `AppleIntelI210Ethernet.kext` was removed from the `IONetworkingFamily.kext` located under /S/L/E/ because there are no Apple devices which have a 2.5 Gigabit port. Therefore, boot-args `dk.e1000=0` (Big Sur) and `e1000=0` (Monterey) which ensured that the I225 Controller could connect to the `AppleIntelI210Ethernet.kext` will no longer work. But there's a fix.

### Stock Firmware Users
Follow these [**Instructions**](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/I225-V_FIX.md#option-1-using-a-ssdt-with-corrected-header-description) to enable the I225-V Controller in macOS Ventura.

### Custom firmware users
- Luckily for us, the .dext version of the AppleIntelI210Ethernet driver is still present under /S/L/DriverExtensions
- Therefore you need to disable boot-args `dk.e1000=0` and `e1000=0` so that the I225 can connect to the .dext driver.

This way. I have working Internet from macOS Catalina all the way up to Ventura.

## NOTES
- You can leave the Kernel Patch for macOS Catalina enabled since it will only be applied up to Kernel 20.4 so it won't affect Big Sur and newer. But you have to disable the device property `PciRoot(0x0)/Pci(0x1C,0x1)/Pci(0x0,0x0)` since the included Device-ID will spoof the I225-V as I219 which will result in Internet not working on Big Sur 11.4 and newer. So put `#` in front of the mentioned PCI path to disable it. See this [**issue report**](https://github.com/dortania/bugtracker/issues/213) for further details.
- The provided info about WiFi is based on issue reports discussed here and in the [I225-V thread for macOS Monterey](https://www.insanelymac.com/forum/topic/348493-discussion-intel-i225-v-on-macos-monterey/) since I don't use WiFi on my rig. There's also a support thread for [I225-V on macOS Ventura](https://www.insanelymac.com/forum/topic/352281-intel-i225-v-on-ventura/#comment-2786429)
- Guide for [dropping/replacing the DMAR table](https://github.com/5T33Z0/OC-Little-Translated/tree/main/00_About_ACPI/ACPI_Dropping_Tables#method-2-dropping-tables-based-on-table-signature)
- If you've changed something in the `DeviceProperties` for testing and revert the settings later, Internet may not work. In this case reset the kernel cache by entering `sudo kextcache -i /` in Terminal.
