## Conditions for Intel I225-V and 3rd Party WiFi/LAN Cards to work
I noticed that some users have issues with getting 3rd party WiFi and Ethernet cards to work if they dropped the DMAR table and replaced it with a modified one with removed memory regions. The DMAR table of the Z490 Vision G contains 2 reserved memory regions – on is for the XCHI Controller. 

I've read in a support thread for the Aniqua 10 Gigabit Card that it worked when the memory region for the XHCI controller (PCI Path : 14,00) remained in the DMAR table. So I did some test with 3 tables: no memory regions included, and 2 more with either one or the other memory region included. It turns out: if you've flashed the custom firmware for the Intel I225-V Controller it doesn't matter if you drop, replace or keep the stock DMAR table as long as Vt-d is enabled and `DisableIoMapper` is set to `false`. On the stock firmware, it's a different story. Below you find the results of my tests and recommended settings for different scenarios.

:warning: Always use the latest [release](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/releases) of my EFI folder! Don't report issues if you don't my EFI and Config. I will close them immediately!

### Enabling the Intel I225-V Ethernet Controller

|macOS |Procedure|
|-------------|---------|
**12 and 13 beta**| You need to [flash a custom Firmware](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/I225-V_FIX.md) to make it work. After that, settings for macOS 11.4+ apply!
**11.4+**|	1. Disable `PciRoot(0x0)/Pci(0x1C,0x1)/Pci(0x0,0x0)` (add `#` in front of it)</br>2. Go to `Kernel` > `Patch` and disable `I225-V Patch` </br> 3. Add boot-arg `dk.e1000=0`</br> 4. Save and reboot</br></br>**NOTE**: This is the default configuration. If you are running Big Sur 11.4 and newer you don’t have to change anything here!
**10.15 to 11.3**|1. Enable `#PciRoot(0x0)/Pci(0x1C,0x1)/Pci(0x0,0x0)` (delete `#`)</br> 2. Go to `Kernel/Patch` and enable `I225-V Patch`.</br> 3. Delete/disable boot-arg `dk.e1000=0`</br> 4. Save and reboot.
**10.14 and older**| Not compatible. You need a third party Ethernet Card.
	
#### Config settings for Intel I225-V, custom firmware:

macOS            |DisableIoMapper|DMAR (OEM)|DMAR (dropped / replaced)| I225-V / 3rd Party working|
:---------------:|:-------------:|:--------:|:-----------------------:|:-------------------------:
10.11.4 to 12.5+ | **OFF**       | **YES**  | **NO / NO**             | **YES / YES**
10.11.4 to 12.5+ | ON            | NO       | YES / YES               | NO / YES
10.11.4 to 12.5+ | OFF           | NO       | YES / YES               | YES/ NO
10.15 to 11.3    | OFF/ON (doesn't matter)  | YES      | NO / NO                 | **NO / NO**

#### Config settings for Intel I225, stock firmware:

macOS   |DisableIoMapper|DMAR (OEM) |DMAR dropped / replaced | I225-V / 3rd Party working|
------|:-------------:|:---------:|:----------------------:|:-------------------------:|
10.11.4 to 12.4+ | ON            | NO        | YES / YES              | **NO / YES**
10.11.4 to 12.4+ | OFF           | NO        | YES / YES              | **NO / NO**
10.11.4 to 12.4+ | OFF           | NO        | YES / YES              | YES / YES
10.15 to 11.3    | ON            | YES       | NO / NO                | YES / YES

### NOTES
- You can leave the Kernel Patch for macOS Catalina enabled since it it will only be applied up to Kernel 20.4. Big Sur, Monterey and Ventura will boot without issues. But you have to disable the device property `PciRoot(0x0)/Pci(0x1C,0x1)/Pci(0x0,0x0)` because the combination of the Kernel patch and the Device-ID used in the Device Property will spoof the Intel I225V as I219 to macOS Catalina to make Internet work. But this isn't required for Bigsur 11.4+ and will result in Internet not working, so put an `#` in front of the mentioned PCI path to disable it. See this [**issue report**](https://github.com/dortania/bugtracker/issues/213) for further details.
- The provided info about WiFi is based on reports from this [**thread**](https://www.insanelymac.com/forum/topic/348493-discussion-intel-i225-v-on-macos-monterey/) since I don't use WiFi on my rig.
