# Lenovo MSM8953 Android Kernel

Kernel development repository for Lenovo MSM8953 devices used by the TB-8704 project.

## Scope

This repository is intended for the Android-facing kernel work required by the Lenovo Tab 4 8 Plus TB-8704F project.

Early work will preserve the known-working legacy kernel path where needed for recovery bring-up. Modern mainline Linux work belongs in `SnisLab/linux_lenovo_TB8704` and should not be mixed into this repository without an explicit migration plan.

## Project principles

- Keep the legacy rescue path reproducible.
- Do not mix unrelated mainline experiments into recovery-critical branches.
- Preserve provenance for imported vendor/CAF/Lineage sources.
- Make compatibility backports and Android-specific changes explicit.
- Use small, reviewable commits.

## Status

The legacy TB8704 MSM8953 baseline is pinned to
`lenovo-devs/android_kernel_lenovo_msm8953` branch `lineage-17.1`, commit
`331772c6dd08d087cfa2e50ad69185f11bc5f47d`.

The source has been imported as the initial reproducible legacy baseline. No
local kernel modifications or build fixes have been applied yet.

## Related repositories

- `SnisLab/android_recovery_lenovo_TB8704`
- `SnisLab/android_device_lenovo_TB8704`
- `SnisLab/linux_lenovo_TB8704`
