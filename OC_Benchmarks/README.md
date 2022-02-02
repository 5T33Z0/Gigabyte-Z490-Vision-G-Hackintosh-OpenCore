# Geekbench CPU Benchmark Tests with various SMBIOSes

## About
**CPU**: 10th Gen Intel Core i9-10850K (Comet Lake)</br>
**Geekbench**: 5.4.4</br>
**macOS**: macOS 11.6.3 (Big Sur)

## Results
### iMac20,2 (recommended for this CPU)

![](https://raw.githubusercontent.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/main/OC_Benchmarks/iMac20%2C2.png)

### iMacPro1,1
![](https://raw.githubusercontent.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/main/OC_Benchmarks/iMacPro1%2C1.png)

### iMacPro19,1 (with emulated i9-9900K)
![](https://raw.githubusercontent.com/5T33Z0/Gigabyte-Z490-Vision-G-Hackintosh-OpenCore/main/OC_Benchmarks/iMac19%2C1_with_emuated_i9-9900K.png)

## Summary
1. **iMac20,2** is the slowest of the 3 tested SMBIOS configurations
2. **iMacPro1,1** has the highest Multicore Score
3. **iMac19,1** which uses an emulated i9-9900K and its framebuffer scores the highest single core score.

## Conclusions
It's weird that the recommended SMBIOS which supposedly should work best for this CPU turns out to be the slowest overall with more than 40 points less in Multi-core score compared to iMacPro1,1 and about 20 points less in single core poerformances compared to iMac19,1. I guess you have to figure out for yourself which SMBIOS works best for your needs.
 