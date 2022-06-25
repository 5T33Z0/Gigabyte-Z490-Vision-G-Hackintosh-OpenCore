# Where's Clover?

I ditched the Clover EFI (for now) since Clover r5146 is incapable of booting macOS Ventura on Cometlake Systems. 

## And now?
Unless this issue is resolved I won't provide any Clover EFI and config. I just can't be bothered with this.

## Anything else?
There have been reports on insanelymac, that r5137 works with Cometlake. So I tested it and it turns out that r5137 – a build which is more than a year old – for some odd reasons manages to boot a brand new macOS beta version while r5146 fails. 

So if you want to use clover, get r5137 (all the binaries have been deleted from the Clover repo, btw…), replace CloverX64.efi, Drivers and disable the following keys: 

- /Quirks/ResizeAppleGpuBars
- /RtVariables/HWTarget
- /SMBIOS/ExtendedFirmwareFeatures
- /SMBIOS/ExtendedFirmwareFeaturesMask