# TB-8704F Legacy Kernel Baseline Investigation

Status: Phase K0 investigation record. Later K1/K2 source import and build
results are documented separately.

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

## Lineage 17.1 fork provenance

The additional practical source is:

* Repository: `lenovo-devs/android_kernel_lenovo_msm8953`
* Branch: `lineage-17.1`
* Branch head: `331772c6dd08d087cfa2e50ad69185f11bc5f47d`

This repository is a fork of `LineageOS/android_kernel_lenovo_msm8953`. The
two histories share the common ancestor
`cd20e13ccabd8fd1181eff9408ec0580f53ef3ec`. The official LineageOS
`lineage-17.1` history contains, among others,
`arch/arm64/configs/lineageos_kuntao_defconfig`, but does not contain
`lineageos_tb8704_defconfig`. The TB8704-specific configuration is introduced
in the lenovo-devs fork by:

* `81b7e18da83deec6d6cf48ddb8dd69ef43cf229c`:
  `arch: arm64: Add tb8704 lineageos config`

This establishes the relationship as official common MSM8953 kernel history
followed by fork-specific TB8704 additions. It must not be described as
evidence that the official LineageOS repository itself supports TB8704.

The fork's TB8704 device and recovery-relevant history includes:

* `2717db3ec882956e53e98d78fe20bb60f6969eae`:
  `arch: arm: boot: dts: reverse engineered tb8704 dts code`; this adds the
  TB8704 DTS files and explicitly reconstructs them from TB8703/X703 and stock
  boot image/DTB data.
* `7d2feae5daa60b7e6521f16eadf04e24989ff8f4`:
  `arch: arm: boot: dts: tb8704: Fix reboot to recovery`; this removes the
  `qcom,store-hard-reset-reason` property from the PMIC DTS path.
* `594b80ea42c45e6e078c734499e653eacfe4bde2` changes the TB8704 DTS to the
  CAF hall driver, and `3ad0010702ea36e4f186a680b7767a58c368b4c0` fixes a
  TB8704 DTS typo affecting OTG.
* `c870a3a1f8822e609e6482918bbc13ca1b9a7461` disables modules in the TB8704
  configuration and `6b5c69e93f256b6b64038ce163b4528efb37fff4` enables target
  TTL in that configuration.
* The branch head `331772c6dd08d087cfa2e50ad69185f11bc5f47d` adds
  `KEY_WAKEUP` for the TB8704 double-tap event.

The fork's branch head Makefile reports Linux 3.18.140. The shared history
contains `9a298fe002c95c0b48677128584a5ec34d9c12c4`,
`Merge 3.18.140 into android-3.18`, which changes the kernel base from
3.18.139 to 3.18.140. The later merge
`3ed361af1b3803f2dec1ecfecd369fd446165143` brings the Android common
`android-3.18` line into `lineage-16.0-caf-8996`; it is an additional common
kernel-history merge, not a TB8704 change. Both commits are ancestors of the
lenovo-devs `lineage-17.1` head.

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

The Android device-tree side preserves the TB-8704 family model and selects
the family defconfig for F, X, N and V. This is evidence of shared
source/configuration, not evidence that one variant's hardware support proves
another variant's support. The official kernel repository remains a common
MSM8953 lineage source; TB8704 support in this document refers specifically to
the lenovo-devs fork and its device-specific history.

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
* No valid flattened-device-tree header was found in the decompressed payload.
  This earlier search examined only the decompressed kernel payload and did
  not inspect bytes trailing the gzip stream.

K3 later examined the bytes trailing the gzip stream and found three valid
appended FDT blobs. The supplied file is therefore established to contain an
appended DTB table.

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

The lenovo-devs `lineage-17.1` line is now a substantially stronger public
relative of the physical kernel's `3.18.140-lineageos` version than the older
3.18.139 HighwayStar configuration anchor. This is a lineage relationship,
not identification of `g217079fec494`; that suffix remains unresolved.

## Baseline recommendations

### Historical provenance baseline

The historical provenance baseline remains HighwayStar's
`HighwayStar/android_kernel_lenovo_tb8704`, branch `cm-14.1`, at the F
configuration anchor `cfffe19df9e92c60a9f15608bb917d7685785b0b`. It preserves
the Lenovo source import and the explicitly documented TB8704 DT
reconstruction path. It is the source trail for understanding the origin of
TB8704 support, not the preferred first build candidate anymore.

### Practical Lineage 17.1 baseline

The preferred first reproducible-build candidate is now:

* Repository: `lenovo-devs/android_kernel_lenovo_msm8953`
* Branch: `lineage-17.1`
* Commit: `331772c6dd08d087cfa2e50ad69185f11bc5f47d`
* Defconfig: `lineageos_tb8704_defconfig`
* Architecture: `arm64`
* Output: `Image.gz-dtb`
* Toolchain: historical Android GCC 4.9, preferably
  `aarch64-linux-android-4.9` with matching binutils

This candidate contains TB8704 support, the TB8704 defconfig, Linux 3.18.140,
and the Android 10/Lineage 17.1-era history. It is still not established as
the exact source of the physical kernel release.

## Reproducible build command

The command below is documented for the practical baseline only. It must be
run in K1 after source import is explicitly approved:

```sh
export ARCH=arm64
export CROSS_COMPILE=/path/to/aarch64-linux-android-4.9/bin/aarch64-linux-android-
make lineageos_tb8704_defconfig
make -j$(nproc) Image.gz-dtb
```

The later K2.4 GCC 4.9 build is documented separately. A GCC 4.9 build was
prioritized over current GCC/Clang because both the prebuilt compiler string
and the original Android 3.18 build environment point to that generation.

## Windows checkout constraint

The earlier Windows checkout failed at:

`drivers/gpu/drm/nouveau/core/subdev/i2c/aux.C`

The final path component `AUX` is reserved by Windows, so a normal
Windows/NTFS working tree is not suitable for kernel source checkout or build.
K1 must use a real Linux filesystem, such as an ext4-backed Linux VM or the
internal WSL2 filesystem. Do not use `/mnt/c/...` as the kernel working tree.

## Open provenance gaps

* Identify the exact source of `3.18.71-perf-gdbc2759-dirty`.
* Identify which DT data, if any, was supplied to the prebuilt at boot-image
  packaging time.
* Resolve `g217079fec494` using a complete boot image, kernel config,
  `/proc/version`, or a matching public repository history.
* Perform a clean build of the practical candidate with the historical
  toolchain and compare output metadata before any functional change.

No source import was authorized by this K0 result; the later K1 source import
is documented in the current provenance records.
