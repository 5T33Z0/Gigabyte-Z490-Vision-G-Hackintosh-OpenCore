# Slimmed AppleALC kext

**Mainboard**: Gigabyte Z490 Vision G</br>
**Codec**: Realtek ALC1200 </br>
**Included Layouts**: `17`</br>
**AppleALC Version**: 1.8.3 (86 kb) </br>

## Codec Structure
![](https://raw.githubusercontent.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/2ceb7d214d2e36d6cbc17b79fe91f9710a7137fe/Additional_Files/ALC1220/Codec_Dump/codec_dump_dec.txt.svg)

## PinConfig
![](https://raw.githubusercontent.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/main/Additional_Files/ALC1220/Codec_Dump/PinConfig_LayoutID_17.png)

## How to
- Download and extract the [**Latest AppleACL-Release**](https://github.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/tree/main/Additional_Files/ALC1220/Slimmed_AppleALC)
- Replace the existing AppleALC.kext in your `EFI/OC/Kexts` folder
- Change the Layout-ID in boot-args or Device Properties to `17`
- Save and Reboot 

## Notes

- If you update AppleALC with OCAT or similar, this kext will be overwritten and you're back to stock.
- Don't use this kext with any other Layout-Id than 17 – it won't work!
- If you want to compile your own slimmed down AppleALC kext, you can follow my tutorial [**here**](https://github.com/5T33Z0/AppleALC-Guides/tree/main/Slimming_AppleALC)
