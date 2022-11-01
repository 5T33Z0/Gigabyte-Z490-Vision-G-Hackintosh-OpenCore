# iGPU Framebuffer & Device IDs

## i9 10850K (default)
- **AAPL,ig-platform-id** 
  - Empty Framebuffer: `0300C89B`
  - For Display: `07009B3E` (recommended), `00009B3E` (default)
- **Device ID**: `C59B0000`
- **SMBIOS**: `iMac20,2`

## i9 9900K (emulated) for running macOS < 10.15
- **AAPL,ig-platform-id** 
  - Empty Framebuffer: `0300913E`
  - For Display: `07009B3E` (recommended), `00009B3E` (default)
- **Device ID**: `983E0000` 
- **Kernel/Emulate**:
  - **Cpuid1Data**: `EA060900 00000000 00000000 00000000`
  - **Cpuid1Mask**: `FFFFFFFF 00000000 00000000 00000000`
- **SystemProductName**: iMac19,1
