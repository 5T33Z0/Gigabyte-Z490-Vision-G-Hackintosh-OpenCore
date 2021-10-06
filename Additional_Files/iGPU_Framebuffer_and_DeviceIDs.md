## iGPU Framebuffer & Device IDs

#### i9 10850K (default)
- **AAPL,ig-platform-id** (empty Framebuffer): `0300C89B`
- **AAPL,ig-platform-id** (for Display): `07009B3E` (recommended), `00009B3E` (default)
- **Device ID**: `C59B0000`
- **SystemProductName**: iMac20,2

#### i9 9900K (emulated) for running macOS < 10.15
- **AAPL,ig-platform-id** (empty Framebuffer): `0300913E`
- **AAPL,ig-platform-id** (for Display): `07009B3E` (recommended), `00009B3E` (default)
- **Device ID**: `983E0000` 
- **Cpuid1Data**: `EA060900 00000000 00000000 00000000`
- **Cpuid1Mask**: `FFFFFFFF 00000000 00000000 00000000`
- **SystemProductName**: iMac19,1





