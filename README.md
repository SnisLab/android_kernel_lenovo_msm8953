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

Initial project setup. No kernel source has been imported yet.

## Related repositories

- `SnisLab/android_recovery_lenovo_TB8704`
- `SnisLab/android_device_lenovo_TB8704`
- `SnisLab/linux_lenovo_TB8704`
