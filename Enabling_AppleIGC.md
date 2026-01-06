# Enabling AppleIGC

In order to make use of the `AppleIGC.kext` for Intel I225/I226 NICs, the following config settings are required – otherwise Ethernet won't work:

- **ACPI**
	- "Add" Section: enable `DMAR.aml`
	- "Delete" Sction: enable `Drop OEM HPET Table` rule
- **Kernel**
	- "Add" Section: enable `AppleIGC.kext`
	- "Quirks" Section: enable `DisableIoMapperMapping` quirk

Tested with macOS Sonoma to Tahoe. But it should work with macOS Catalina and onward.
