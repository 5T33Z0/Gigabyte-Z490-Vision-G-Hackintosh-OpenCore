# Geekbench 5 Test Results
## CPU Benchmarks
**CPU**: 10th Gen Intel Core i9-10850K (Comet Lake)</br>
**Geekbench**: 5.4.4</br>
**macOS**: macOS 11.6.3 (Big Sur)</br>
**OpenCore**: 0.7.8

### Results
#### iMac20,2 (recommended for this CPU)
![](https://raw.githubusercontent.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/main/OC_Benchmarks/iMac20%2C2.png)

#### iMacPro1,1
![](https://raw.githubusercontent.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/main/OC_Benchmarks/iMacPro1%2C1.png)

#### iMacPro19,1 (with emulated i9-9900K)
![](https://raw.githubusercontent.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/main/OC_Benchmarks/iMac19%2C1_with_emuated_i9-9900K.png)

### Summary and Conclusion
1. **iMac20,2** is the slowest of the 3 tested SMBIOS configurations
2. **iMacPro1,1** has the highest Multicore Score
3. **iMac19,1** which uses an emulated i9-9900K and its framebuffer scores the highest single core score.

Ut's weird that the recommended SMBIOS which supposedly should work best for this CPU turns out to be the slowest overall with more than 40 points less in Multi-core score compared to iMacPro1,1 and about 20 points less in single core poerformances compared to iMac19,1. I guess you have to figure out for yourself which SMBIOS works best for your needs.

## GPU Computing Benchmarks
**GPU**: Saphire Radeon RX 580 Nitro+ (4 GB)</br>
**Geekbench**: 5.4.4</br>
**macOS**: macOS 11.6.5 (Big Sur)</br>
**SMBIOS**: iMac20,2</br>
**OpenCore**: 0.8.1

HI made some GPU benchmark tests for Metal and OpenCL performance with two different configurations: "stock" and "tweaked". "Stock" uses the card as is, while "tweaked" makes use of [AMD Radeon Tweaks](https://www.tonymacx86.com/threads/amd-radeon-performance-enhanced-ssdt.296555/) by mattiestonnie.

### Results
#### Metal
1. **RX 580 Metal**, stock:
	![](https://raw.githubusercontent.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/main/OC_Benchmarks/RX580_Metal_notweaks.png)
2. **RX 580 Metal**, tweaked:
	![](https://raw.githubusercontent.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/main/OC_Benchmarks/RX580_Metal_Tweaks.png)

As you can see, the Metal score increased by apporx. 580 points when using the "tweaked" configuration.

#### Open CL
1. **RX 580 OpenCL**, stock:
	![](https://raw.githubusercontent.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/main/OC_Benchmarks/RX580_OpenCL_notweaks.png)
2. **RX 580 OpenCL**, tweaked
	![](https://raw.githubusercontent.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/main/OC_Benchmarks/RX580_OpenCL_Tweaks.png)

In contrast to the Metal, the OpenCL performance decreased when using the "tweaked" configuration.

### Conclusion
As expected, Metal is the best option when it comes to GPU base computational tasks in macOS. Unless I made a mistake it's kind of weird, that the "stock" configuration performs better in OpenCL than the "tweaked" setup.