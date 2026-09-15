# Agent instructions

This repository is the **legacy Android kernel workstream** for the Lenovo Tab 4 8 Plus TB-8704F modernization project.

## Scope

You own:
- the known-working Lenovo / Qualcomm Android kernel line for MSM8953
- recovery-kernel compatibility
- build fixes needed to keep the legacy kernel usable with newer recovery / Android trees
- kernel configuration, compiler compatibility and device-specific kernel debugging

You do **not** own:
- Linux mainline 6.x porting (`SnisLab/linux_lenovo_TB8704`)
- recovery UI / fstab / TWRP configuration (`SnisLab/android_recovery_lenovo_TB8704`)
- Android / Lineage userspace and HAL device-tree work (`SnisLab/android_device_lenovo_TB8704`)

Do not try to turn this repository into the mainline-kernel project. Mainline development belongs in `linux_lenovo_TB8704`.

If another repository needs a change, document:

```text
Dependency request:
Repository: <repo>
Required change: <precise change>
Reason: <why it is needed>
Expected interface/result: <what this repo expects afterwards>
```

## Working model

- Keep the first goal conservative: reproduce and understand the last known-good TB-8704F kernel before modernizing anything.
- Work in small, reviewable commits.
- Separate source import, build-system fixes, config changes and functional changes.
- Never claim hardware support from another MSM8953 device proves TB-8704F support.
- Prefer original Lenovo, LineageOS and device-specific sources.
- Record provenance of imported patches.

## Safety

- Kernel work must not modify bootloader or firmware partitions.
- Do not change partition layouts from kernel assumptions alone.
- Avoid large rebases or patch bombs until the baseline kernel can be reproduced.

## Commit discipline

Examples:
- `kernel: import verified TB8704 legacy baseline`
- `defconfig: document recovery-required options`
- `build: fix clang compatibility for recovery kernel`

Avoid commits such as `update kernel` that mix unrelated changes.

## Initial milestone

Identify and reproduce the exact kernel source/configuration used by the most reliable existing TB-8704F Lineage/TWRP builds, and document how recovery consumes the resulting kernel image.
