## Apple ALC slimmed

### About
AppleALC kext for the Z490 Vision G Board using Realtek ALC 1220. It only included Layout-ID 17, which was routed for the Z490 Vision G specifically. It's only 86 kb. So a massive reduction in file size compared to the official build (3.7 mb)!

**Version**: 1.7.2

### How to

- Download and extract the .zip
- Replace existing AppleALC kext in EFI folder
- Change Layout-ID in boot-args or Device Properties to `17`   

### Notes

- If you update AppleALC with OCAT or similar, this kext will be overw
