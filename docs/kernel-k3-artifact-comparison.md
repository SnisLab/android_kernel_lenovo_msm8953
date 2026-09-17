# K3 Legacy Kernel Artifact Comparison

## Scope

This is a static comparison only. No kernel build, image packing, recovery
build, device access, boot, or flash operation was performed in K3.

## Inputs

### K2.4 build

- Kernel release: `3.18.140-lineageos-g4881b5d9`
- `Image`: 23867392 bytes,
  `aaebf0f912c98ffa634ea361b3803f304919bd8798db900d1c101be6fa46e7a8`
- `Image.gz`: 9768204 bytes,
  `62d52a3195fd54d180b45e8de5ccb28f40721b39b74f68236844f9dab66e12b6`
- `Image.gz-dtb`: 10282679 bytes,
  `c0b2011a09c5aa0e347069bc80730e8648ec4cd2687c4f5ceb8a40b400ad5240`
- Build directory: `/root/build/tb8704-k2/arch/arm64/boot/`

### Historical recovery prebuilt

- Repository: `brianreboot/twrp_device_lenovo_tb_8704f`
- Branch: `android-7.1`
- Commit: `508409d8dcdf2084a5a165e07babe013d1854494`
- File: `kernel`
- Total size: 10060180 bytes
- SHA-256: `9ed23e2eae57b61350110faaccd2a4a6fa6a3651535788275dd7aa0db55dff0c`

## gzip layout

Both files begin with a gzip stream. Decompression was performed with Python's
gzip-aware zlib decoder; the decoder's `unused_data` is the trailing region.

| Artifact | gzip portion | raw payload | tail | total |
| --- | ---: | ---: | ---: | ---: |
| K2.4 `Image.gz-dtb` | 9768204 | 23867392 | 514475 | 10282679 |
| Recovery `kernel` | 9335887 | 23088128 | 724293 | 10060180 |

The K2.4 tail starts with `d00dfeed` and contains two valid FDTs. The recovery
tail starts with `d00dfeed` and contains three valid FDTs. The recovery
prebuilt is therefore classified as **gzip kernel + appended DTB(s)**, not
gzip-only or an unresolved trailing format.

Tail SHA-256 values:

- K2.4 tail: `f2b780742d12c6420aa131d861cfb20a4f0565551e274a0fc72d1bf3f15c39e6`
- Recovery tail: `fb80abcbe57869f23437157969f0e360339ff5f5375d85259fe6065e3bd505e7`

## FDT inventory

### K2.4 `Image.gz-dtb`

| FDT | offset in tail | size | SHA-256 | identity |
| --- | ---: | ---: | --- | --- |
| 0 | 0 | 257528 | `0c6096a7bf103c44191e91d443f536c94fcf739c88162e70e85fd6495b695213` | MSM8953 MTP, `qcom,msm-id=<0x125 0x0>`, board `0x8` |
| 1 | 257528 | 256947 | `dd08c491ff277adb5dcee91572d9b6744a536018af9bfdc381ab31b1c78bbcb5` | APQ8053 MTP, `qcom,msm-id=<0x130 0x0>`, board `0x8` |

The corresponding build outputs are:

- `tb8704-msm8953-mtp.dtb`: SHA-256
  `0c6096a7bf103c44191e91d443f536c94fcf739c88162e70e85fd6495b695213`
- `tb8704-apq8053-mtp.dtb`: SHA-256
  `dd08c491ff277adb5dcee91572d9b6744a536018af9bfdc381ab31b1c78bbcb5`

### Historical recovery `kernel`

| FDT | offset in tail | size | SHA-256 | identity |
| --- | ---: | ---: | --- | --- |
| 0 | 0 | 214353 | `9cafbd5691527fa862a78e796388ac4a0f9e4a149b834d0dd6ef486de541aa52` | MSM8953 Simulator, `qcom,msm-id=<0x125 0x0>`, board `0x10` |
| 1 | 214353 | 254796 | `6500a010da11b7e76dcae3d12b4c368b173cb95c29f5ec908838f68b52d7c13` | APQ8053 MTP, `qcom,msm-id=<0x130 0x0>`, board `0x8` |
| 2 | 469149 | 255144 | `8ce0edc9e75f64bd2ce6ff893367bf4ff32e3fcd50237a1de59938c1bfcfa4f7` | MSM8953 MTP, `qcom,msm-id=<0x125 0x0>`, board `0x8` |

The historical FDT identities are `qcom,msm8953-sim`,
`qcom,apq8053-mtp`, and `qcom,msm8953-mtp`. The two MTP identities correspond
to the same broad board families as K2.4, while the historical set also has a
simulator DTB and its MSM8953 MTP includes the PMI8950 metadata.

## Payload comparison

The decompressed K2.4 payload is byte-identical to its raw `Image`:

```text
Image SHA-256:                  aaebf0f912c98ffa634ea361b3803f304919bd8798db900d1c101be6fa46e7a8
Image.gz-dtb decompressed SHA:  aaebf0f912c98ffa634ea361b3803f304919bd8798db900d1c101be6fa46e7a8
cmp:                            equal
```

The historical raw payload is 23088128 bytes and has SHA-256
`dcf99e1d508e82292f993a663307959857d0aa461b1bf82abbc83c597d9ad8b4`; it is
not equal to the K2.4 raw image. Both payloads have the arm64 `ARMd` marker at
offset `0x38`.

The historical payload identifies as:

```text
Linux version 3.18.71-perf-gdbc2759-dirty (ubuntu@201server) (gcc version 4.9.x 20150123 (prerelease) (GCC) ) #1 SMP PREEMPT Wed Oct 9 15:40:05 CST 2019
```

## DTB comparison

There are no exact binary matches between the three historical FDTs and the
two K2.4 DTBs. The historical prebuilt nevertheless contains recognizable
MSM8953/APQ8053 MTP board identities, so it is structurally related at the
board-family level but is not the same DTB set as K2.4.

## Running Android kernel

The known running identity remains:
`3.18.140-lineageos-g217079fec494`.

It shares the `3.18.140` base release with K2.4, but differs in build/source
identity from `3.18.140-lineageos-g4881b5d9`. The `g217079fec494` suffix remains
unresolved and is not assigned to a commit. No Android compatibility claim is
made from this static comparison.

## Conclusion

- K2.4: gzip-compressed arm64 kernel payload plus two appended DTBs.
- Historical recovery prebuilt: gzip-compressed arm64 kernel payload plus
  three appended DTBs.
- Historical prebuilt classification: **B, gzip kernel + appended DTB(s)**.
- K2.4 and the historical prebuilt have different raw payloads, gzip streams,
  DTB sizes, and DTB hashes.
- Kernel source modifications: none.
- Device access: none.
- Flash: none.
