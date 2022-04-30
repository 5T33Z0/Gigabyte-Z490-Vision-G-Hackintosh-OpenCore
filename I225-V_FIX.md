# Gigabyte Z490 Vision G Intel I225-V fix for macOS Monterey
 
> **Disclaimer**: This fix requires flashing a custom firmware onto the EEPROM of the Intel I225-V Ethernet Controller. I am not responsible for any hardware failures that might occur during the process – execute the following guide at your own risk!

## About
On the Z490 Vision G, the I225-V Controller stopped working shortly after the first betas of macOS Monterey were released. Various tricks were tried to fix it: assigning IP addresses and settings manually, dropping tables, changing BIOS and Quirks settigs and – the scariest trick of them all – replacing network kexts of previously working builds, which breaks the seal of the snapshot partition and could corrupt macOS, leaving it in an unbootable state. On top of that, this method only worked temporarily until the next beta was released. There's a lengthy thread about the issue on [insanelymac](https://www.insanelymac.com/forum/topic/348493-discussion-intel-i225-v-on-macos-monterey/).

Until now, the only reliable option was to just buy a third party network card supported by macOS 12. Fortunately, a new method to get the I225-V working again was discovered. But it requires flashing a modified firmware onto the EEPROM  so macOS can detect and attach it to the `com.apple.DriverKit-AppleEthernetE1000.dext` driver successfully.

## Technical Backgroud
On the Intel I225-V Controller of this and possible other Gigabyre Boards, the Subsystem-ID and Subsystem Vendor-ID were incorrect. The Vendor-ID (`8086` for Intel) was also used as Subsystem-Vendor-ID and the Subsystem-ID only contained zeros instead of the correct value `E000`. 

Following shows the file header of the I225MOD binary in hex code. The values highlighted in green are the ones that were changed to make the controller work again:

<img width="554" alt="I225VEE" src="https://user-images.githubusercontent.com/76865553/166050133-ff5ec23e-68af-439f-af07-81c32f7ebe76.png">

## Preparations

- **BIOS**: enable `VT-d` if it isn't already
- Save and reboot into macOS
- Open **Network Settings**: set Ethernet > IPv4 to `DHCP` and Advanced… > Hardware > Configuration to `Automatic`.
- **OpenCore**:
	- Mount EFI
	- Add `OpenShell` to OC/Tools and `config.plist`
	- Disable/remove `DeviceProperties` for `PciRoot(0x0)/Pci(0x1C,0x1)/Pci(0x0,0x0)`
	- Disable/remove boot-arg `dk.e1000=0`
	- Unselect Kernel > Quirks > `DisableIOMapper`
	- Drop `DMAR`table (already implemented in my OC/Clover configs)
	- Save your `config.plist`
- Download `I225-Vmod.zip` from [here](https://www.hackintosh-forum.de/forum/thread/56123-l%C3%B6sung-f%C3%BCr-i225-v-v2-problem-auf-z490-plattform-vornehmlich-gigabyte-boards-unte/) and extract it
- Copy `eeupdate64.efi` and `I225MOD`to the root folder of a FAT32 formatted USB Flash Drive.
- Restart the system

## Flashing the I225-V EEPROM via OpenShell	
- From the OpenCore GUI, select `OpenShell`
- Type `fs0:` and hit `Enter` to change the working drive (`fs:0` is most likely your USB flash drive)
- Type `ls` to list the content of the drive. In this case `ls0` is correct drive letter:</br>![06143142](https://user-images.githubusercontent.com/76865553/162021483-39a7d188-5b96-4607-a1cd-a550dd1560d5.png)
- Next, type `eeupdate64e /gui` and hit `Enter` to run the tool.
- Select the "Intel(R) Ethernet Controler I225-V" with the arrow keys and hit `Enter`:</br>![06143155](https://user-images.githubusercontent.com/76865553/162020889-a98abf45-6f58-4c96-a7d3-ffb743895b16.png)
- In the next screen, select "Raw EEPROM - Extended":</br>![06143203](https://user-images.githubusercontent.com/76865553/162020929-65ff5300-0838-4b6f-a26c-2401274b6b10.png)
- Next, press `F3` to dump the original EEPROM to your Flash Drive
- Enter a name for the backup file and confirm it with "OK". There won't be a any confirmation dialog, though:</br>![06143217_01](https://user-images.githubusercontent.com/76865553/162021033-ec75129f-4f4b-48f6-8403-2fc37f75446d.png)
- Next, press `F4` to load the new EEPROM:</br>![06143217_02](https://user-images.githubusercontent.com/76865553/162021068-d4102c40-94e8-42f5-bc83-85605019ae0c.png)
- Now type `I225MOD`, press `ENTER` and confirm loading the file. :warning: Keep the original MAC Address when importing the EEPROM (you will be asked).
-  Press `ESC` to exit and confirm saving.
-  Reboot the system into macOS Monterey. 

If everything was done correct, you should now have working Internet connectivity. You may need to remove and add the network adapter again under System Preferences > Network.

For me, the I225-V worked immediately after flashing the EEPROM and rebooting. The device is also recognized by Hackintool as "Ethernet Controller I225-V". If you are still facing issues afterwards, you could try attaching the I225-V to  `com.apple.driver.AppleIntelI210Ethernet.kext` by using boot boot-arg `e1000=0`.

## Credits and Resources
[**Original Guide**](https://www.hackintosh-forum.de/forum/thread/56123-l%C3%B6sung-f%C3%BCr-i225-v-v2-problem-auf-z490-plattform-vornehmlich-gigabyte-boards-unte/) by badbrain. Translated from german and modified by me.
