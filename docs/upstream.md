# Legacy kernel provenance

## Practical build baseline

- Upstream URL: `https://github.com/lenovo-devs/android_kernel_lenovo_msm8953.git`
- Repository: `lenovo-devs/android_kernel_lenovo_msm8953`
- Branch: `lineage-17.1`
- Commit: `331772c6dd08d087cfa2e50ad69185f11bc5f47d`
- Kernel: `3.18.140`
- Defconfig: `arch/arm64/configs/lineageos_tb8704_defconfig`
- Expected output: `arch/arm64/boot/Image.gz-dtb`
- Import date: `2026-09-17`
- Import method: cloned upstream into a separate temporary directory, checked out
  the exact commit in detached HEAD, and copied the snapshot into this project
  root with `rsync`, excluding `.git`, project files, and `docs/`.

The project `README.md`, `AGENTS.md`, and existing `docs/` files were preserved.
The upstream README was retained as `docs/upstream-readme.txt`.

## TB8704 device-tree paths

- `arch/arm/boot/dts/qcom/tb8704/`
- `arch/arm/boot/dts/qcom/tb8704-msm8953-mtp.dts`
- `arch/arm/boot/dts/qcom/tb8704-apq8053-mtp.dts`
- `arch/arm64/configs/lineageos_tb8704_defconfig`

## Historical provenance baseline

- Repository: `HighwayStar/android_kernel_lenovo_tb8704`
- Branch: `cm-14.1`
- Commit: `cfffe19df9e92c60a9f15608bb917d7685785b0b`

This remains the historical provenance and reconstruction reference. It is not
the preferred practical build baseline.

## Running device kernel

- Kernel: `3.18.140-lineageos-g217079fec494`
- Status: `unresolved`

The imported upstream commit is not claimed to be identical to the running
device kernel.
