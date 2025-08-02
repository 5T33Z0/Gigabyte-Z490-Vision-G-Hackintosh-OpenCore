# About `USBPorts` Package 

The USBPorts kexts included in this package are optional since my EFI contains `SSDT-PORTS.aml` which handdles USB Port mapping via ACPI which is completely independent of the used macOS version - it just works!

The USBPorts kext is only included for users who want to change to USB Port mapping but don't know how to do it in ACPI.

## About included Files
- `USBPorts.kext` with USB Port Mappings matching various `models`. Compatible works with the following SMBIOSes:
	- iMac19,1
	- iMac20,1
	- iMac20,2
	- iMacPro1,1
	- MacPro7,1
- **Port Mapping List**: Shows the current mapping.

## Install Instructions

If you want to use the `USBPorts.kext` instead of `SSDT-PORTS.aml`, do the following:

1. Mount your ESP
2. Open your config with OCAT or ProperTree
4. Add USBPorts.kext to your EFI/OC/Kexts folder and config and enable it
5. Disable `ACPI/Add/SSDT-PORTS.aml`
6. Disabled `ACPI/Delete/Drop OEM USB Port Map (xh_cmsd4)`
7. Save and reboot

