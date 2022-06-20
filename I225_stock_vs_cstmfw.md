# Conditions for Intel I225-V and 3rd Party WiFi/LAN Cards to work
I noticed that some users have issues with getting 3rd party WiFi and Ethernet cards to work if they dropped the DMAR table and replaced it with a modified one with removed memory regions. The DMAR table of the Z490 Vision G contains 2 reserved memory regions – on is for the XCHI Controller. 

I've read in a support thread for the Aniqua 10 Gigabit Card that it worked when the memory region for the XHCI controller (PCI Path : 14,00) remained in the DMAR table. So I did some test with 3 tables: no memory regions included, and 2 more with either one or the other memory region included. It turns out: if you've flashed the custom firmware for the Intel I225-V Controller it doesn't matter if you drop, replace or keep the stock DMAR table as long as Vt-d is enabled and `DisableIoMapper` is set to `false`. On the stock firmware, it's a different story. Below you find the results of my tests and recommended settings for different scenarios.

:warning: Please use the latest [release](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/releases) of my EFI folder before reporting issues.

## Enabling the Intel I225-V Ethernet Controller

|macOS |Procedure|
|-------------|---------|
**12 and 13 beta**| You need to [flash a custom Firmware](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/I225-V_FIX.md) to make it work. After that, settings for macOS 11.4+ apply!
**11.4+**|1. Disable `PciRoot(0x0)/Pci(0x1C,0x1)/Pci(0x0,0x0)` (add `#` in front of it)</br>2. Go to `Kernel` > `Patch` and disable `I225-V Patch` </br> 3. Add boot-arg `dk.e1000=0`</br> 4. Save and reboot</br></br>**NOTE**: This is the default configuration. If you are running Big Sur 11.4 and newer you don’t have to change anything here!
**10.15 to 11.3**|1. Enable `#PciRoot(0x0)/Pci(0x1C,0x1)/Pci(0x0,0x0)` (delete `#`)</br> 2. Go to `Kernel/Patch` and enable `I225-V Patch`.</br> 3. Delete/disable boot-arg `dk.e1000=0`</br> 4. Save and reboot.
**10.14 and older**| Not compatible. You need a third party Ethernet Card.
	
### Config settings for Intel I225-V, custom firmware:

macOS         |Vt-D    |DisableIoMapper |DMAR (OEM) |DMAR (dropped/replaced) |I225-V / 3rd Party working|
:-------------|:------:|:--------------:|:---------:|:----------------------:|:--------------------------:
11.4 to 13.0  | **ON** |**OFF**         | **YES**   | **NO / NO**            | **YES / YES**
11.4 to 12.5  | ON     | OFF            | NO        | YES / YES              | YES/ NO
11.4 to 12.5  | OFF/ON |ON              | NO        | YES / YES              | NO / YES
10.15 to 11.3 | OFF/ON |ON              | YES       | NO / NO                | **YES / YES**

### Config settings for Intel I225, stock firmware:

macOS         |Vt-D|DisableIoMapper|DMAR (OEM)|DMAR (dropped/replaced)| I225-V / 3rd Party working|
:------------|----|:-------------:|:--------:|:---------------------:|:-----------------:
12.5+ | ON |**OFF**        | **YES**  | **NO / NO**           | **NO / YES**
11.4 to 11.6.7 | ON | ON            | NO       | YES / YES             | [YES / YES](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/issues/19#issuecomment-1153315826)
10.15 to 11.3 | OFF/ON|OFF/ON      | YES      | NO / NO               | **YES / NO**

## Intel I225-V and macOS Ventura
- In macOS Ventura, `AppleIntelI210Ethernet.kext` was removed from the `IONetworkingFamily.kext` located under /S/L/E/.
- Therefore, boot-args `dk.e1000=0` (Big Sur) and `e1000=0` (Monterey) which ensured that the I225 Controller connected to the `AppleIntelI210Ethernet.kext` will no longer work in macOS Ventura!
- Luckily for us, the .dext version  is still present under /S/L/DriverExtensionst:![](/Users/steezonics/Desktop/dexti225.png)
- Therefore you need to disable boot-args `dk.e1000=0` and `e1000=0` so that the I225 can connect to the .dect version of the driver instead.
- If you are using the modded firmware this works without issues.

If you are using the stock firmware, do the following:

- Add [AppleIntelI210Ethernet.kext](https://www.insanelymac.com/forum/topic/352281-intel-i225-v-on-ventura/?do=findComment&comment=2786214) to EFI/OC/Kext and config.plist
- Set `MinKernel`to 22.0.0 so it is only injected into macOS Ventura
- Enable `e1000=0` boot-arg so the I225-V uses the kext instead of the dext driver
- Save and reboot

## NOTES
- You can leave the Kernel Patch for macOS Catalina enabled since it it will only be applied up to Kernel 20.4. Big Sur, Monterey and Ventura will boot without issues. But you have to disable the device property `PciRoot(0x0)/Pci(0x1C,0x1)/Pci(0x0,0x0)` because the combination of the Kernel patch and the Device-ID will spoof the Intel I225V as I219 to macOS Catalina to make Internet work. But this isn't required for Bigsur 11.4+ and will result in Internet not working, so put an `#` in front of the mentioned PCI path to disable it. See this [**issue report**](https://github.com/dortania/bugtracker/issues/213) for further details.
- The provided info about WiFi is based on reports from this [**thread**](https://www.insanelymac.com/forum/topic/348493-discussion-intel-i225-v-on-macos-monterey/) since I don't use WiFi on my rig.
- Guide for [dropping/replacing the DMAR table](https://github.com/5T33Z0/OC-Little-Translated/tree/main/00_About_ACPI/ACPI_Dropping_Tables#method-2-dropping-tables-based-on-table-signature)
- If you've changed something in the `DeviceProperties` for testing and revert the settings later, Internet may not work. In this case reset the kernel cache by entering `sudo kextcache -i /` in Terminal.
