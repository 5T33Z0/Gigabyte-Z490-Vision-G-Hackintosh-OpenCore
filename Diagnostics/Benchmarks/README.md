## Geekbench 5 Test Results

### CPU Benchmarks

**CPU**: 10th Gen Intel Core i9-10850K (Comet Lake)  
**Geekbench**: 5.4.4  
**macOS**: macOS 11.6.3 (Big Sur)  
**OpenCore**: 0.7.8

#### Results

| SMBIOS | Single-Core Score | Multi-Core Score |
|:---|:---:|:---:|
| [iMac20,2](Diagnostics/Benchmarks/Images/iMac20,2.png) | 1276 | 10379 |
| [iMacPro1,1](Diagnostics/Benchmarks/Images/iMacPro1,1.png) | 1280 | **10421** |
| [iMac19,1](Diagnostics/Benchmarks/Images/iMac19,1_with_emuated_i9-9900K.png) | **1295** | 10394 |

### Summary and Conclusion

1. **iMac20,2** (recommended SMBIOS) is the slowest of the three tested SMBIOS configurations.
2. **iMacPro1,1** has the highest multi-core score.
3. **iMac19,1**, which runs as an emulated i9-9900K, has the highest single-core score.

It's surprising that the recommended SMBIOS, which supposedly should work best for this CPU, turns out to be the slowest overall, with more than 40 points less in multi-core performance compared to iMacPro1,1 and about 20 points less in single-core performance compared to iMac19,1. Ultimately, you have to decide which SMBIOS works best for your needs.

## GPU Computing Benchmarks

I ran GPU benchmark tests for Metal and OpenCL performance using two different configurations: "Stock" and "Tweaked". **Stock** uses the card as is, while **Tweaked** makes use of [AMD Radeon Tweaks](https://www.tonymacx86.com/threads/amd-radeon-performance-enhanced-ssdt.296555/) by mattiestonnie.

**GPU**: Sapphire Radeon RX 580 Nitro+ (4 GB)  
**Geekbench**: 5.4.4  
**macOS**: macOS 11.6.5 (Big Sur)  
**OpenCore**: 0.8.1  
**Clover**: r5146

### Test Results

| API (SMBIOS) | macOS | Score Stock (OC/Clover) | Score Tweaked (OC/Clover) |
|:---|:---:|:---:|:---:|
| Metal (iMac20,2) | 12.4 beta 4 | — | [50438](Diagnostics/Benchmarks/Images/macOS12.4_Metal_Tweaked_OpenCore.png) / [**51387**](Diagnostics/Benchmarks/Images/macOS12.4_Metal_Tweaked_Clover.png) |
| Metal (MacPro7,1) | 11.6.5 | [48394](Diagnostics/Benchmarks/Images/MacPro7,1_Metal_stock.png) | [51323](Diagnostics/Benchmarks/Images/MacPro7,1_Metal_Tweaked.png) |
| Metal (iMac20,2) | " | [50107](Diagnostics/Benchmarks/Images/RX580_Metal_notweaks.png) / [**50402**](https://browser.geekbench.com/v5/compute/4785319) | [50683](Diagnostics/Benchmarks/Images/RX580_Metal_Tweaks.png) / [**51211**](Diagnostics/Benchmarks/Images/iMac20.2_Metal_Tweaked_Clover.png) |
| Metal (iMacPro1,1) | " | [48230](Diagnostics/Benchmarks/Images/iMacPro1,1_Metal_stock.png) | [50032](Diagnostics/Benchmarks/Images/iMacPro1,1_Metal_tweaked.png) |
| OpenCL (MacPro7,1) | 11.6.5 | [43304](Diagnostics/Benchmarks/Images/MacPro7,1_OpenCL_stock.png) | [**46056**](Diagnostics/Benchmarks/Images/MacPro7,1_OpenCL_Tweaked.png) |
| OpenCL (iMacPro1,1) | " | [45230](Diagnostics/Benchmarks/Images/iMacPro1,1_OpenCL_stock.png) | [44929](Diagnostics/Benchmarks/Images/iMacPro1,1_OpenCL_tweaked.png) |
| OpenCL (iMac20,2) | " | [44762](Diagnostics/Benchmarks/Images/RX580_OpenCL_notweaks.png) / [**45178**](https://browser.geekbench.com/v5/compute/4785396) | [44566](Diagnostics/Benchmarks/Images/RX580_OpenCL_Tweaks.png) / [**45576**](https://browser.geekbench.com/v5/compute/4785422) |

In contrast to Metal, OpenCL performance decreased when using the "Tweaked" configuration.

### Summary and Conclusions

1. As expected, Metal is the better option for GPU-based computing tasks in macOS.
2. With OpenCL, MacPro7,1 "Tweaked" (using `SSDT-RX580.aml`) comes out on top, while for iMac20,2 and iMacPro1,1 the "Stock" version performs better.
3. Overall, the GPU performs best in Metal and OpenCL with the "MacPro7,1 Tweaked" configuration. However, since CPU power management is not as efficient when using MacPro7,1, I think `iMac20,2` Tweaked is still the most suitable SMBIOS. Results might differ in 3D benchmark tests.
4. In test scenarios where Clover was also used, Clover consistently outperformed OpenCore.