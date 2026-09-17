# K2.4 First Legacy Kernel Build

## Result

- Phase: `K2.4`
- Host: Ubuntu 24.04 LTS, Linux `7.0.14-16-pve`, x86_64
- Kernel baseline: `4881b5d96ffd456e61b0e5df3582c715afd25446`
- Upstream baseline: `331772c6dd08d087cfa2e50ad69185f11bc5f47d`
- Kernel: `3.18.140`
- Architecture: `arm64`
- Defconfig: `lineageos_tb8704_defconfig`
- Build target: `Image.gz-dtb`
- Defconfig result: success
- Build result: success

## Toolchain and host compatibility

- Toolchain: AOSP `aarch64-linux-android-4.9`
- Toolchain repository: `platform/prebuilts/gcc/linux-x86/aarch64/aarch64-linux-android-4.9`
- Toolchain commit: `84fb09fafc92a3d9b4d160f049d46c3c784cc941`
- Cross compiler: `4.9.x 20150123 (prerelease)`
- Host compiler: `gcc (Ubuntu 13.3.0-6ubuntu2~24.04.1) 13.3.0`
- Host compatibility adjustment: `HOST_EXTRACFLAGS=-fcommon`, exported via
  the environment

Two Ubuntu 24.04 compatibility details were required:

1. The historical AOSP GCC wrapper requires `/usr/bin/python`; this was
   provided by the `python-is-python3` package.
2. Modern host GCC defaults to `-fno-common`; exporting
   `HOST_EXTRACFLAGS=-fcommon` restores the common-symbol behavior required by
   the legacy DTC sources.

`HOST_EXTRACFLAGS` was exported rather than passed on the `make` command line,
so the SELinux host Makefiles could append their required
`-Isecurity/selinux/include` path.

## Commands

The out-of-tree build used:

```text
export ARCH=arm64
export SUBARCH=arm64
export CROSS_COMPILE=/root/toolchains/aarch64-linux-android-4.9/bin/aarch64-linux-android-
export HOST_EXTRACFLAGS=-fcommon

make -C /root/src/android_kernel_lenovo_msm8953 \
  O=/root/build/tb8704-k2 ARCH=arm64 \
  CROSS_COMPILE=/root/toolchains/aarch64-linux-android-4.9/bin/aarch64-linux-android- \
  lineageos_tb8704_defconfig

make -C /root/src/android_kernel_lenovo_msm8953 \
  O=/root/build/tb8704-k2 ARCH=arm64 \
  CROSS_COMPILE=/root/toolchains/aarch64-linux-android-4.9/bin/aarch64-linux-android- \
  -j14 Image.gz-dtb
```

Logs remain outside the repository under
`/root/build/tb8704-k2-logs/`.

## Configuration

- `.config` SHA-256:
  `9dface24751bd67f4cbb0fc2c53f4500e02ed3aee8fd616efa69321df8c04eec`
- `CONFIG_ARM64=y`
- `CONFIG_ARCH_MSM8953=y`
- `CONFIG_BUILD_ARM64_APPENDED_DTB_IMAGE=y`
- `CONFIG_BUILD_ARM64_APPENDED_DTB_IMAGE_NAMES=""`
- `CONFIG_MACH_LENOVO_TB8704=y`
- `CONFIG_TOUCHSCREEN_GT9XX_TB8704=y`

## Artifacts

All artifacts are under `/root/build/tb8704-k2/arch/arm64/boot/`:

| Artifact | Size | SHA-256 |
| --- | ---: | --- |
| `Image` | 23867392 bytes | `aaebf0f912c98ffa634ea361b3803f304919bd8798db900d1c101be6fa46e7a8` |
| `Image.gz` | 9768204 bytes | `62d52a3195fd54d180b45e8de5ccb28f40721b39b74f68236844f9dab66e12b6` |
| `Image.gz-dtb` | 10282679 bytes | `c0b2011a09c5aa0e347069bc80730e8648ec4cd2687c4f5ceb8a40b400ad5240` |

The `Image.gz-dtb` size exceeds `Image.gz` by `514475` bytes. The resulting
kernel release is `3.18.140-lineageos-g4881b5d9`.

The embedded kernel string is:

```text
Linux version 3.18.140-lineageos-g4881b5d9 (root@OpenCode) (gcc version 4.9.x 20150123 (prerelease) (GCC) ) #1 SMP PREEMPT Thu Sep 17 19:54:27 UTC 2026
```

## Device-tree outputs

The appended-DTB build produced these individual DTBs:

- `arch/arm64/boot/dts/qcom/tb8704-apq8053-mtp.dtb`
- `arch/arm64/boot/dts/qcom/tb8704-msm8953-mtp.dtb`

They are included in `Image.gz-dtb` by the kernel build's appended-DTB step.

## Warnings and comparisons

The host build repeatedly emitted the upstream Android GCC deprecation warning:

```text
Android GCC has been deprecated in favor of Clang, and will be removed from
Android in 2020-01
```

The initial DTC `yylloc` multiple-definition error and subsequent SELinux
`classmap.h` error were resolved by the environment-only `HOST_EXTRACFLAGS`
handling described above. No kernel source, DTC source, defconfig, or DTS was
changed.

The built kernel release is distinct from the known running kernel
`3.18.140-lineageos-g217079fec494`; the recovery prebuilt is also a different
kernel, `3.18.71-perf-gdbc2759-dirty`, with size `10060180` bytes and SHA-256
`9ed23e2eae57b61350110faaccd2a4a6fa6a3651535788275dd7aa0db55dff0c`.

Kernel source modifications: none. Device access: none. Flash: none.
