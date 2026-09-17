# K4 Recovery Kernel Handoff

Phase: `K4`

## Purpose

Provide the verified K2.4 kernel artifact to the Recovery workstream.

## Kernel artifact

- Artifact: `Image.gz-dtb`
- Path: `/root/build/tb8704-k2/arch/arm64/boot/Image.gz-dtb`
- Kernel release: `3.18.140-lineageos-g4881b5d9`
- Size: `10282679` bytes
- SHA-256: `c0b2011a09c5aa0e347069bc80730e8648ec4cd2687c4f5ceb8a40b400ad5240`
- Kernel source: `SnisLab/android_kernel_lenovo_msm8953`
- Kernel baseline: `4881b5d96ffd456e61b0e5df3582c715afd25446`
- Configuration: `lineageos_tb8704_defconfig`
- Architecture: `arm64`
- Packaging: gzip-compressed arm64 `Image` plus two appended TB8704 MTP DTBs

The size and SHA-256 were reverified before this handoff. Kernel source
modifications: none.

Recovery integration performed: no. Device access: no. Flash: no.

## Dependency request

Dependency request:
Repository: SnisLab/android_recovery_lenovo_TB8704
Required change: Build a temporary TWRP test image using the verified K2.4 Image.gz-dtb kernel while keeping the known-good 1Q Recovery userspace/configuration otherwise unchanged.
Reason: The legacy kernel baseline now builds reproducibly and must be hardware-tested independently from Recovery userspace changes.
Expected interface/result: A non-flashed Recovery image suitable for one controlled fastboot boot test, with the replacement kernel SHA256 c0b2011a09c5aa0e347069bc80730e8648ec4cd2687c4f5ceb8a40b400ad5240.
