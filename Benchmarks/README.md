# Geekbench 5 Test Results
## CPU Benchmarks
**CPU**: 10th Gen Intel Core i9-10850K (Comet Lake)</br>
**Geekbench**: 5.4.4</br>
**macOS**: macOS 11.6.3 (Big Sur)</br>
**OpenCore**: 0.7.8

### Results
#### iMac20,2 (recommended for this CPU)
![](https://raw.githubusercontent.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/main/Benchmarks/iMac20%2C2.png)

#### iMacPro1,1
![](https://raw.githubusercontent.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/main/Benchmarks/iMacPro1%2C1.png)

#### iMacPro19,1 (with emulated i9-9900K)
![](https://raw.githubusercontent.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/main/Benchmarks/iMac19%2C1_with_emuated_i9-9900K.png)

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

I did some GPU benchmark tests for Metal and OpenCL performance with two different configurations: "stock" and "tweaked". "Stock" uses the card as is, while "tweaked" makes use of [AMD Radeon Tweaks](https://www.tonymacx86.com/threads/amd-radeon-performance-enhanced-ssdt.296555/) by mattiestonnie.

### Results using `iMac20,2` SMBIOS
#### Metal Scores
1. **RX 580 Metal**, stock:
	![](https://raw.githubusercontent.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/main/Benchmarks/RX580_Metal_notweaks.png)
2. **RX 580 Metal**, tweaked:
	![](https://raw.githubusercontent.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/main/Benchmarks/RX580_Metal_Tweaks.png)

As you can see, the Metal score increased by apporx. 580 points when using the "tweaked" configuration.

#### Open CL Scores
1. **RX 580 OpenCL**, stock:
	![](https://raw.githubusercontent.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/main/Benchmarks/RX580_OpenCL_notweaks.png)
2. **RX 580 OpenCL**, tweaked:
	![](https://raw.githubusercontent.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/main/Benchmarks/RX580_OpenCL_Tweaks.png)

### Results using `iMacPro1,1` SMBIOS

#### Metal Scores
1. **RX 580 Metal**, stock:
![](https://raw.githubusercontent.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/main/Benchmarks/iMacPro1%2C1_Metal_stock.png)
2. **RX 580 Metal**, tweaked:
![](https://raw.githubusercontent.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/main/Benchmarks/iMacPro1%2C1_Metal_tweaked.png)

#### Open CL Scores
1. **RX 580 OpenCL**, stock:
![](https://raw.githubusercontent.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/main/Benchmarks/iMacPro1%2C1_OpenCL_stock.png)
2. **RX 580 OpenCL**, tweaked:
![](https://raw.githubusercontent.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/main/Benchmarks/iMacPro1%2C1_OpenCL_tweaked.png)

### Results using `MacPro7,1` SMBIOS
#### Metal Scores
1. **RX 580 Metal**, stock:
![](https://raw.githubusercontent.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/main/Benchmarks/MacPro7%2C1_Metal_stock.png)
2. **RX 580 Metal**, tweaked:
![](https://raw.githubusercontent.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/main/Benchmarks/MacPro7%2C1_Metal_Tweaked.png)

#### Open CL Scores
1. **RX 580 OpenCL**, stock:
![](https://raw.githubusercontent.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/main/Benchmarks/MacPro7%2C1_OpenCL_stock.png)
2. **RX 580 OpenCL**, tweaked:
![](https://raw.githubusercontent.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/main/Benchmarks/MacPro7%2C1_OpenCL_Tweaked.png)

**All Results compared**:

Method             | Score (stock) | Score (tweaked)
-------------------|---------------|----------------
Metal (MacPro7,1)  | 48394         | **51323**
Metal (iMac20,2)   | 50107         | 50683
Metal (iMacPro1,1) | 48230         | 50032
||
OpenCL (MacPro7,1) | 43304         | **46056**
OpenCL (iMacPro1,1)| 45230         | 44929
OpenCL (iMac20,2)  | 44762         | 44566

In contrast to the Metal, the OpenCL performance decreased when using the "tweaked" configuration.

### Conclusions
1. As expected, Metal is the best option when it comes to GPU-based computing tasks in macOS. 
2. With OpenCL, MacPro7,1 "tweaked" comes out on top, hich makes use of "SSDT-RX580.aml", while for iMac20,2 and iMacPro1,1 the "stock" version performs better.
3. Overall, the GPU performs best in Metal and OpenCL when using the "MacPro7,1 tweaked" configuration. But since CPU power Management is not as efficient when using MacPro7,1, I think `iMac20,2` tweaked is still the most suitable SMBIOS. But maybe it behaves different in 3D Benchmark test.
