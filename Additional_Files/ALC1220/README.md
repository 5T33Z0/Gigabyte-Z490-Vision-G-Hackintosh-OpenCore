## Slimmed Apple ALC Kext

### About
AppleALC kext for the Z490 Vision G Board using Realtek ALC 1220. It only includes Layout-ID 17, which was routed for the Z490 Vision G specifically. It's only 86 KB in size. So a massive reduction in file size compared to the official build (3.7 mb)!

**Version**: 1.7.4

### Codec Structure
![](https://raw.githubusercontent.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/33f07e2f57463acdba1b733b73faa53423b3372f/Additional_Files/ALC1220/codec_dump_dec.txt.svg)

### PinConfig
![](https://raw.githubusercontent.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/main/Additional_Files/ALC1220/PinConfig_LayoutID_17.png)

### How to

- Download and extract the .zip
- Replace existing AppleALC kext in EFI folder
- Change Layout-ID in boot-args or Device Properties to `17`   

### Notes

- If you update AppleALC with OCAT or similar, this kext will be overwritten and you're back to stock.
- Don't use this kext with any other Layout-Id than 17 – it's won't work!
