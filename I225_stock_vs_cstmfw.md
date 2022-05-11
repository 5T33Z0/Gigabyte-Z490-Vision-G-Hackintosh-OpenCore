## Conditions for Intel I225-V and 3rd Party WiFi/LAN Cards to work

### Settings for Intel I225, stock firmware:

macOS            |DisableIoMapper|DMAR (OEM) |DMAR dropped / replaced | I225-V / 3rd Party working|
:---------------:|:-------------:|:---------:|:----------------------:|:-------------------------:|
10.15 to 11.3    | ON            | YES       | NO / NO                | YES / YES
10.11.4 to 12.4+ | OFF           | NO        | YES / YS               | YES / YES
10.11.4 to 12.4+ | OFF           | NO        | YES / YES              | **NO / NO**
10.11.4 to 12.4+ | ON            | NO        | YES / YES              | **NO / YES**


#### Procedure for enabling the Intel I225-V Ethernet Controller (stock firmware)

|macOS Version|Procedure|
|-------------|---------|
**macOS 12.0.1+**| You need to [flash a custom Firmware](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/I225-V_FIX.md) to make it work again.
**macOS 11.4+**|	1. Disable `PciRoot(0x0)/Pci(0x1C,0x1)/Pci(0x0,0x0)` (add `#` in front of it)</br>2. Go to `Kernel` > `Patch` and disable `I225-V Patch` </br> 3. Add boot-arg `dk.e1000=0`</br> 4. Save and reboot</br></br>**NOTE**: This is the default configuration. If you are running Big Sur 11.4 and newer you don’t have to change anything here!
**macOS 10.15 to 11.3**|1. Enable `#PciRoot(0x0)/Pci(0x1C,0x1)/Pci(0x0,0x0)` (delete `#`)</br> 2. Go to `Kernel/Patch` and enable `I225-V Patch`.</br> 3. Delete/disable boot-arg `dk.e1000=0`</br> 4. Save and reboot</br></br>**IMPORTANT**: Once you flash the custom firmware, the I225 no longer works in macOS Catalina! Once enable the DeviceProperty, the system crashes on boot.
**macOS 10.14 and older**| Not compatible. You need a third party Ethernet Card.
	
**NOTE**: You could just leave the Device Property, Kernel Patch (since it will only apply up to Kernel 20.4 anyway) and boot-arg enabled for macOS Catalina, Big Sur/Monterey without causing issues. But I think it's cleaner to just enable what's necessary for each OS, unless you have a multiboot system with Catalina and Big Sur/Monterey installed. See this [**issue report**](https://github.com/dortania/bugtracker/issues/213) for further details.

### Settings for Intel I225-V, custom firmware:

macOS            |DisableIoMapper|DMAR (OEM)|DMAR (dropped / replaced)| I225-V / 3rd Party working|
:---------------:|:-------------:|:--------:|:-----------------------:|:-------------------------:
10.15 to 11.3    | ON            | YES      | NO / NO                 | **NO / NO**
10.11.4 to 12.4+ | OFF           | NO       | YES / YES               | YES/ NO
10.11.4 to 12.4+ | ON            | NO       | YES / YES               | NO / YES
10.11.4 to 12.4+ | **OFF**       | **YES**  | **NO / NO**             | **YES / YES**

#### Requirements for Big Sur 11.4+ and macOS Monterey
- Enabled Vt-d in BIOS
- [**Flash custom firmware**](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/blob/main/I225-V_FIX.md) for Intel I225 Controller
- Disable Kernel/Quirks `DisableIoMapper`
- Delete or disable DeviceProptery enty `PciRoot(0x0)/Pci(0x1C,0x1)/Pci(0x0,0x0)`
- Delete/disable boot-args `dk.e1000=0` and `e1000=0`

### I225 custom Firmware and macOS Catalina

Once you flash the custom firmware, the I225-V does no longer works in macOS Catalina. macOS doesn't have drivers for the 2,5 Gbit I225-V controller. The trick was to use a combination of Device Properties and a Kernel Patch to make it look like 1 gigabit Intel I219-LM to macOS Catalina, so it would use the I219 driver instead. This works fine when using the stock firmware but with the custom one it doesnt.

Maybe there is a way, but I couldn't figure it out yet. As soon as you activate the rquired DeviceProperties to make macOS Catalina detect it as an 1 gigabit Intel I219-LM, the system crashes on boot.
