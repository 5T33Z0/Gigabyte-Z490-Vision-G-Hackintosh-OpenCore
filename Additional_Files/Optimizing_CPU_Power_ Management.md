# Optimizing CPU Power Management

For 10th gen Intel CPUs, the `iMac20,2` SMBIOS is best suited, since [these models](https://everymac.com/ultimate-mac-lookup/?search_keywords=iMac20,2) are equipped with i7/i9 CPUs. If you are using an i5, you should use [iMac20,1](https://everymac.com/ultimate-mac-lookup/?search_keywords=iMac20,1) instead.

Since I have an i9-10850K, I am using SMBIOS `iMac20,2`. The system runs smoother when using iMac20.2 instead of iMac19.1. The performance is also better and the idle frequency is lower. But it can be lowered even further with the help of [CPUFriendFriend](https://github.com/corpnewt/CPUFriendFriend). (Click on »Code« and select »Download zip«). 

However, this optimization has to be performed on the each machine individually (unless you are using an i9 10850K as well). To do so, we first verify the current board ID by executing the following command in terminal:

`ioreg -l | grep -i board-id`

The resulting terminal output should be: 

`"board-id" = <"Mac-AF89B6D9451A490B">` (for iMac20,2)</br>
`"board-id" = <»MMac-CFF7D910A743CAAF">`(for iMac20,1)

If your result differs, check if `PlatformInfo` > `Generic` > `SystemProductName` is set to `iMac20,2` or `iMac20,1, correct it, save the `config.plist` and reboot to apply the new SMBIOS.

<<<<<<< Updated upstream
## Using CPUFriendFriend
=======
## Generating a data provider kext using `CPUFriendFriend` 
>>>>>>> Stashed changes
1. Download and unpack `CPUFriendFriend-master.zip`
2. Doubleclick `CPUFriendFriend.command` to run it. You should see the following prompt:</br>
	![LFM](https://user-images.githubusercontent.com/76865553/151773085-f181f1d2-e8f3-4f97-8b29-5c8e741b2765.png)
3. Enter a hex value for the `Low Frequency Mode`. It's the lowest possible frquency the CPU should clock down to without crashing. I am using `08` for 800 MHz. Add your value and hit `Enter`.
4. Next, adjust the `Energy Performance Preference (EPP)`:</br>
	![EPP](https://user-images.githubusercontent.com/76865553/151773160-38aa587d-93e7-414d-9fbe-50c0eee1c437.png)</br>
This This describes how fast the CPUS scales from the lowest to the highest Turbo frequency, which has an impact on the overall power consumption. I use `00`.
5. Next, you have to specify the `Performamce Bias Range`, which is used to set the general bias of the system between performance and energy efficiency. The scale ranges from `00` (maximum performance) to `15` (maximum power saving). Since this is more relevant for notebooks than an i9 workstation, I am using `01`:</br>
	![BIAS](https://user-images.githubusercontent.com/76865553/151773244-f1bd7d7c-182e-468d-86ec-5702283dad13.png)</br>
6. Next, you can apply Additional Energy Savings Options from the MacBook Air SMBIOS (optional):</br>![mba](https://user-images.githubusercontent.com/76865553/151773342-8ac88574-9926-4efb-af9d-7e4599f57e40.png)</br>Make your choice and hit `Enter`
7. Finally, the `CPUFrienDataProver.kext` and additional Files are created:</br>![files](https://user-images.githubusercontent.com/76865553/151773395-212d209b-0e6b-43ca-b105-ccf0172f90e7.png)
8. Copy `CPUFrienDataProver.kext` together with [`CPUFriend.kext`](https://github.com/acidanthera/CPUFriend/releases) into the kext folder and add it to your `config.plist`
9. Save and reboot

## Testing

- Download, install and run [IntelPowerGadget](https://www.intel.com/content/www/us/en/developer/articles/tool/power-gadget.html).
- Observe the frequency graph:</br>![](https://raw.githubusercontent.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/main/Pics/CPU_PM.png)

### Explanation 
Both, `CoreMax` (top line) and `CoreAvg` (jagged blue line) should drop if the computer is idling, whereby the upper line can only drop as low as the solid straight line (= base frequency – in my case 3.6 GHz), which means that the CPU is not boosting. 

At the lower end of the scale, `CoreMin` should be below 1 gHz, since we entered 800 MHz as the LFM value. In this case it is even lower – 700 MHz. Thus, the CPU power management is working fine.

If the frequency is never below the base frequency or permanently above it, something is probably wrong with the CPU power management – unless you’ve modified the CPU parameters in the BIOS by disabling speed step or overclocking all cores, etc.

	 
