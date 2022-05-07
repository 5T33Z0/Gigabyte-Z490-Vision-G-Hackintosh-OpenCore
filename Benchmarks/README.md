# Geekbench 5 Test Results
## CPU Benchmarks
**CPU**: 10th Gen Intel Core i9-10850K (Comet Lake)</br>
**Geekbench**: 5.4.4</br>
**macOS**: macOS 11.6.3 (Big Sur)</br>
**OpenCore**: 0.7.8

### Results
SMBIOS             |  Single-Core Score | Multi-Core Score
-------------------|:------------------:|:----------------:
[iMac20,2](https://raw.githubusercontent.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/main/Benchmarks/iMac20%2C2.png) |1276|10379
[iMacPro1,1](https://raw.githubusercontent.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/main/Benchmarks/iMacPro1%2C1.png)|1280|**10421**
[iMacPro19,1](https://raw.githubusercontent.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/main/Benchmarks/iMac19%2C1_with_emuated_i9-9900K.png)|**1295**|10394

### Summary and Conclusion
1. **iMac20,2** (recomm. SMBIOS) is the slowest of the 3 tested SMBIOS configurations
2. **iMacPro1,1** has the highest multi-core Score
3. **iMac19,1** which runs as an i9-9900K (emulated) has the highest single-core score.

It's weird that the recommended SMBIOS which supposedly should work best for this CPU turns out to be the slowest overall with more than 40 points less in Multi-core score compared to iMacPro1,1 and about 20 points less in single core poerformances compared to iMac19,1. I guess you have to figure out for yourself which SMBIOS works best for your needs.

## GPU Computing Benchmarks
**GPU**: Saphire Radeon RX 580 Nitro+ (4 GB)</br>
**Geekbench**: 5.4.4</br>
**macOS**: macOS 11.6.5 (Big Sur)</br>
**OpenCore**: 0.8.1
**Clover: UEFI r5146

I did some GPU benchmark tests for Metal and OpenCL performance with two different configurations: "stock" and "tweaked". "Stock" uses the card as is, while "tweaked" makes use of [AMD Radeon Tweaks](https://www.tonymacx86.com/threads/amd-radeon-performance-enhanced-ssdt.296555/) by mattiestonnie.

**All Results compared**:

Method             |macOS      |  Score (stock) OC/Clover | Score (tweaked) (OC/Clover)
-------------------|:---------:|:------------------------:|----------------------------
Metal (iMac20,2)   |12.4 beta 4|                          | [50438](https://raw.githubusercontent.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/main/Benchmarks/macOS12.4_Metal_Tweaked_OpenCore.png) / **[51387](https://raw.githubusercontent.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/main/Benchmarks/macOS12.4_Metal_Tweaked_Clover.png)**
||
Metal (MacPro7,1)  |11.6.5     | [48394](https://raw.githubusercontent.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/main/Benchmarks/MacPro7%2C1_Metal_stock.png)                    | [51323](https://raw.githubusercontent.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/main/Benchmarks/MacPro7%2C1_Metal_Tweaked.png)
Metal (iMac20,2)   |"          | [50107](https://raw.githubusercontent.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/main/Benchmarks/RX580_Metal_notweaks.png)                    | [50683](https://raw.githubusercontent.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/main/Benchmarks/RX580_Metal_Tweaks.png) / **[51211](https://raw.githubusercontent.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/main/Benchmarks/iMac20.2_Metal_Tweaked_Clover.png)**
Metal (iMacPro1,1) |"          | [48230](https://raw.githubusercontent.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/main/Benchmarks/iMacPro1%2C1_Metal_stock.png)                    | [50032](https://raw.githubusercontent.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/main/Benchmarks/iMacPro1%2C1_Metal_tweaked.png)
||
OpenCL (MacPro7,1) |11.6.5     | [43304](https://raw.githubusercontent.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/main/Benchmarks/MacPro7%2C1_OpenCL_stock.png)                    | **[46056](https://raw.githubusercontent.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/main/Benchmarks/MacPro7%2C1_OpenCL_Tweaked.png)**
OpenCL (iMacPro1,1)|"          | [45230](https://raw.githubusercontent.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/main/Benchmarks/iMacPro1%2C1_OpenCL_stock.png)                    | [44929](https://raw.githubusercontent.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/main/Benchmarks/iMacPro1%2C1_OpenCL_tweaked.png)
OpenCL (iMac20,2)  |"          | [44762](https://raw.githubusercontent.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/main/Benchmarks/RX580_OpenCL_notweaks.png)                    | [44566](https://raw.githubusercontent.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/main/Benchmarks/RX580_OpenCL_Tweaks.png)

In contrast to the Metal, the OpenCL performance decreased when using the "tweaked" configuration.

### Conclusions
1. As expected, Metal is the best option when it comes to GPU-based computing tasks in macOS. 
2. With OpenCL, MacPro7,1 "tweaked" (which makes use of "SSDT-RX580.aml") comes out on top, while for iMac20,2 and iMacPro1,1 the "stock" version performs better.
3. Overall, the GPU performs best in Metal and OpenCL when using the "MacPro7,1 tweaked" configuration. But since CPU power Management is not as efficient when using MacPro7,1, I think `iMac20,2` tweaked is still the most suitable SMBIOS. But maybe it behaves different in 3D Benchmark test.
4. As far as macOS Monterey is concerned, Clover (r5146) achieves the highest Metal score overall and seems to have a clear advantage over OpenCore.
