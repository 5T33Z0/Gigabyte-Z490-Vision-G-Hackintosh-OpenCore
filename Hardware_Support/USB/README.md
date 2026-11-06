# About `USBPorts` Package 

The USBPorts kexts included in this package are optional since my EFI contains `SSDT-XHUB.aml` which handdles USB Port mapping via ACPI which is completely independent of the used macOS version - it just works! 

The USBPorts kext is only preserved for documentational purposes.

> [!TIP]
> 
> I have created a python script which allows you to convert your existing USB

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
5. Disable `ACPI/Add/SSDT-XHUB.aml`
6. Save and reboot

