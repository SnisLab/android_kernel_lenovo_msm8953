# TB-8704F Legacy Kernel Baseline Investigation

Status: Phase K0, investigation only. No kernel source has been imported.

## Scope

This document covers the legacy Android 3.18 kernel used by TB-8704-family
devices. It does not cover the mainline kernel, recovery configuration, or
Android userspace.

## HighwayStar source

Repository: `HighwayStar/android_kernel_lenovo_tb8704`

The relevant branch is `cm-14.1`. Its current branch tip is
`b9e4174976786b27d82d374b1e1bdf4fb4f0154c` (2020-03-16), but that tip is not
the right F-only provenance anchor: it adds a V configuration and V overlay,
and its Makefile reports 3.18.31.

The first explicit F/X/N/V-family device work is shared. The F-specific
Lineage configuration was added by:

* Commit: `cfffe19df9e92c60a9f15608bb917d7685785b0b`
* Date: 2019-07-13 author date, 2019-07-19 committer date
* Message: `arch: arm64: Add tb8704 lineageos config`
* Defconfig: `arch/arm64/configs/lineageos_tb8704_defconfig`
* Parent: `193d37df591a6bbd2fad7398e876dd357d6c4163`

That commit describes the configuration as based on
`msmcortex-perf_defconfig`, enables `CONFIG_ARCH_MSM8953`, appended DTB
images, and adds TB-8704-specific touchscreen, audio, hall, fingerprint,
TUSB422, WLAN and pstore settings. It is therefore a source/configuration
anchor, not proof that every later TB-8704 build used exactly that tree.

The source import provenance is unusually clear: commit
`8d0f91e56436c7aebe33b4645af8d3a5a4167d0f` says that it imported
`lenovo_tab4_8plus_osc_tab8704f_8704x.tar.bz2` over CAF tag
`LA.UM.5.6.r1-02900-89xx.0`. The DT work was not present in that Lenovo
tarball; commit `193d37df591a6bbd2fad7398e876dd357d6c4163` documents that the
TB-8704 DT was reverse engineered from TB-8703/X703 material and stock
boot.img/DTB data. This separates original Lenovo source from later device
specific reconstruction.

At this anchor the source Makefile reports Linux 3.18.139. Later commits in
the public branch include device-specific fixes and a V config. The reported
running release `3.18.140-lineageos-g217079fec494` is not reproducibly mapped
to a public HighwayStar commit yet.

## Device-tree structure

The source uses the legacy ARM DT location through the arm64 symlink:

`arch/arm/boot/dts/qcom/`

The relevant files include the common MSM8953 files, two TB-8704 board DTS
files (`tb8704-apq8053-mtp.dts` and `tb8704-msm8953-mtp.dts`), and the
`arch/arm/boot/dts/qcom/tb8704/` include directory. That directory contains
the APQ/MSM base includes, PMIC, audio, camera, pinctrl, regulator, panel and
overlay files. The branch also contains `tb8704v-msm8953-mtp.dts` and
`tb8704v-overlay.dtsi`.

The F defconfig is intentionally family-level rather than F-only. Device
selection is made by DT and the Android device tree. HighwayStar's device
`BoardConfig.mk` accepts TB-8704X/F/N/V and selects
`lineageos_tb8704_defconfig`, `TARGET_KERNEL_ARCH := arm64`, and
`BOARD_KERNEL_IMAGE_NAME := Image.gz-dtb`.

LineageOS device trees preserve this family model. The public LineageOS
`lineage-16.0` and `lineage-17.1` branches still select
`lineageos_tb8704_defconfig`; their board asserts include F, X, N and V.
This is evidence of shared source/configuration, not evidence that one
variant's hardware support proves another variant's support.

Other public search results include `LineageOS/android_kernel_lenovo_msm8953`
and several Lenovo MSM8953 forks. They are useful comparison sources only;
the repository names and SoC match do not establish TB-8704F provenance.
The HighwayStar import and its explicit TB-8704 DT commits remain the
preferred device-specific source trail. No separate official Lenovo Git
repository containing the complete TB-8704 DT history was established in K0;
the public evidence points to the Lenovo source tarball described above.

## TWRP prebuilt analysis

Source: `brianreboot/twrp_device_lenovo_tb_8704f`, branch `android-7.1`,
device-tree commit `508409d8dcdf2084a5a165e07babe013d1854494`.

The committed `kernel` is 10,060,180 bytes and has SHA-256
`9ed23e2eae57b61350110faaccd2a4a6fa6a3651535788275dd7aa0db55dff0c`.
Static inspection found:

* The file starts with gzip magic `1f 8b 08`.
* It is one gzip stream; decompression produces 23,088,128 bytes.
* The decompressed payload has the arm64 Linux Image header marker `ARMd`.
* The embedded version string is
  `Linux version 3.18.71-perf-gdbc2759-dirty`.
* The build string contains `ubuntu@201server` and GCC
  `4.9.x 20150123 (prerelease) (GCC)`.
* The build date is `Wed Oct 9 15:40:05 CST 2019`.
* No kernel command line was established from the static payload.
* No valid flattened-device-tree header was found in the decompressed
  payload. The supplied file is therefore not proven to contain an appended
  DTB/DTB table, despite the Android board naming convention.

Certain: gzip format, arm64 Image marker, version/build strings, size and
hash. Likely: a vendor/Lenovo-derived MSM8953 Android kernel image. Not
established: exact source repository, exact commit, defconfig, DTB identity,
or equivalence to HighwayStar.

Conclusion: the prebuilt cannot currently be traced with high probability to
a known public source commit. Its `3.18.71-perf-gdbc2759-dirty` identity is
not the running kernel identity and is not a match for the HighwayStar F
configuration anchor.

## Running kernel identity

The suffix `g217079fec494` was searched in the available HighwayStar source,
the public LineageOS device lineage, and indexed public code search. No
complete, authoritative Git commit was found. It remains **unresolved**.
The suffix must not be treated as a Git SHA or matched by resemblance.

## Reproducible build candidate

The conservative candidate for a first source reproduction is the exact
HighwayStar `cm-14.1` tree at the F-config commit
`cfffe19df9e92c60a9f15608bb917d7685785b0b`, using:

* `ARCH=arm64`
* `lineageos_tb8704_defconfig`
* Android GCC 4.9, preferably the Android 7/8-era
  `aarch64-linux-android-4.9` toolchain
* matching Android binutils from the same toolchain release
* `Image.gz-dtb` as the expected image target

An initial build command, to be run only after source import is explicitly
approved, is:

```sh
export ARCH=arm64
export CROSS_COMPILE=/path/to/aarch64-linux-android-4.9/bin/aarch64-linux-android-
make lineageos_tb8704_defconfig
make -j$(nproc) Image.gz-dtb
```

This is a reproducibility candidate, not a claim that it recreates the
TWRP prebuilt or the currently running 3.18.140 kernel. A GCC 4.9 build is
prioritized over current GCC/Clang because both the prebuilt compiler string
and the original Android 3.18 build environment point to that generation.

## Open provenance gaps

* Identify the exact source of `3.18.71-perf-gdbc2759-dirty`.
* Identify which DT data, if any, was supplied to the prebuilt at boot-image
  packaging time.
* Resolve `g217079fec494` using a complete boot image, kernel config,
  `/proc/version`, or a matching public repository history.
* Perform a clean build of the candidate with the historical toolchain and
  compare output metadata before any functional change or source import.

No Source Import is authorized by this K0 result.
