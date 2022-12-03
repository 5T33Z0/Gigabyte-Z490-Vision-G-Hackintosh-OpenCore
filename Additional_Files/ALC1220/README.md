## Slimmed AppleALC Kext

### About
- AppleALC kext for the Z490 Vision G Board using Realtek ALC 1220.
- **Only 86 KB in size**! The official release is 3.7 mb.
- :warning: Only contains Layout-ID `17`, which was routed for the Z490 Vision G specifically. 

**Current Version**: 1.7.7

### Codec Structure
![](https://raw.githubusercontent.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/33f07e2f57463acdba1b733b73faa53423b3372f/Additional_Files/ALC1220/codec_dump_dec.txt.svg)

### PinConfig
![](https://raw.githubusercontent.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/main/Additional_Files/ALC1220/PinConfig_LayoutID_17.png)

### How to

- Download and extract my latest AppleACL build
- Replace the existing AppleALC.kext in your EFI folder
- Change Layout-ID in boot-args or Device Properties to `17`
- Save and reboot 

### Notes

- If you update AppleALC with OCAT or similar, this kext will be overwritten and you're back to stock.
- Don't use this kext with any other Layout-Id than 17 – it won't work!
