# Intel Arc A370M Graphics Driver Installation

## Installation Summary

This document describes the Intel Arc graphics drivers and tools installed on this Ubuntu 24.04 system.

## Date
2025-11-26

## Installed Components

### Intel Graphics Drivers
- **intel-opencl-icd** (24.39.31294.20-1032~24.04) - Intel OpenCL runtime for compute workloads
- **intel-level-zero-gpu** (1.3.29735.27-914~24.04) - Level Zero GPU runtime for oneAPI
- **intel-media-va-driver-non-free** (24.3.4-1018~24.04) - VA-API driver for hardware video acceleration
- **intel-gpu-tools** (1.28-1ubuntu2) - Tools for debugging and development

### Graphics Libraries
- **libigc1** (1.0.17791.16-1032~24.04) - Intel Graphics Compiler
- **libigc-dev** (1.0.17791.16-1032~24.04) - Intel Graphics Compiler development files
- **libigdfcl1** (1.0.17791.16-1032~24.04) - Intel Graphics Device Function Call Library
- **libigdfcl-dev** (1.0.17791.16-1032~24.04) - Development files for IGDFCL
- **libigdgmm12** (22.5.2-1018~24.04) - Intel Graphics Memory Management Library
- **libigdgmm-dev** (22.5.2-1018~24.04) - Development files for IGDGMM
- **libze1** (1.17.44.0-1022~24.04) - oneAPI Level Zero Loader
- **mesa-vulkan-drivers** (25.0.7-0ubuntu0.24.04.2) - Mesa Vulkan graphics drivers

### Firmware
- **linux-firmware** (20240318.git3b128b60-0ubuntu2.21) - Linux kernel firmware including Intel graphics firmware

## Repository Added
- Intel GPU drivers repository: https://repositories.intel.com/gpu/ubuntu

## Features Enabled

### For Intel Arc A370M
- **OpenCL Support**: Compute acceleration for parallel processing
- **Level Zero**: Low-level API for compute workloads (required for AI/ML frameworks)
- **Vulkan**: Modern graphics API support via Mesa drivers
- **VA-API**: Hardware-accelerated video encoding/decoding
- **Media Processing**: Full media engine support

## Verification Commands

To verify the installation, you can use these commands:

```bash
# Check OpenCL platforms
clinfo

# Check Level Zero devices
sycl-ls

# Check VA-API support
vainfo

# Check Vulkan support
vulkaninfo

# Check Intel GPU tools
intel_gpu_top
```

## Notes
- These drivers support Intel Arc Alchemist GPUs (including A370M)
- Level Zero runtime enables AI/ML workloads on Intel Arc GPUs
- For full functionality, a system reboot may be required
- Make sure your kernel is recent enough (5.15+ recommended for Arc support)

## Next Steps
1. Reboot the system to load the new drivers and firmware
2. Verify GPU detection with `intel_gpu_top` or similar tools
3. Test hardware acceleration with applications

## References
- Intel Graphics Installation Guide: https://dgpu-docs.intel.com/
- Intel Arc Graphics: https://www.intel.com/arc
