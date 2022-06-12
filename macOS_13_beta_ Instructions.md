## macOS Ventura beta Install Instructions

- Download my latest EFI [Release](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/releases) and unzip it
- Open config.plis and do the following:
	- In **Booter/Patch**, disable… 
		- `Skip Board ID check`
		- `Reroute HW_BID to OC_BID`
	- In **Kernel/Patch**, disable…
		- `Force IOGetVMMPresent`
		- `Reroute kern.hv_vmm_present patch (1)`
		- `Reroute kern.hv_vmm_present patch (2)`
		- `Disable Library Validation Enforcement`
	- ~~Disable `AvoidRuntimeDefrag` Kernel Quirk &rarr; Otherwise the Installer won't start~~. Latest OpenCore release fixes the `AvoidRuntimeDefrag` Quirk, so enable it.
	- Add `-lilubeta` and `-v` to boot-args (optional)
- Save the config.plist
- Prepare a New Partition for Installing macOS Ventura in Disk Utility. Don't install this over your perfectly working OS!
- Download [InstallAssistant.pkg](https://www.insanelymac.com/forum/topic/351969-pre-release-macos-ventura/?do=findComment&comment=2783763) for macOS 13
- Run it &rarr; creates "Install macOS 13 beta" App in Programs folder
- Start the Install macOS 13 beta" App
- Select the new Partition as Target drive
- Install
- It reboots a couple of times
- Enjoy macOS 13 beta