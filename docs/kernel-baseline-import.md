# K1 Legacy Kernel Source Import

## Baseline

- Phase: `K1`
- Upstream repository: `lenovo-devs/android_kernel_lenovo_msm8953`
- Upstream URL: `https://github.com/lenovo-devs/android_kernel_lenovo_msm8953.git`
- Upstream branch: `lineage-17.1`
- Pinned commit: `331772c6dd08d087cfa2e50ad69185f11bc5f47d`
- Kernel version: `3.18.140`
- Architecture: `arm64`
- Defconfig: `arch/arm64/configs/lineageos_tb8704_defconfig`
- Expected later output: `Image.gz-dtb`
- Import type: pinned source snapshot
- Import date: `2026-09-17`

## Import

The upstream repository was cloned into the separate temporary directory
`/tmp/tb8704-kernel-upstream.zun9ep` and the pinned commit was checked out in
detached HEAD. The snapshot was copied into the project root with `rsync`.

Import exclusions:

- `AGENTS.md`
- `README.md`
- `docs/`
- `.git/`

The import used no `--delete`. The post-import
`rsync -a --checksum --dry-run --itemize-changes` check reported no source
differences. Source modifications during import: none.

The upstream README is preserved as `docs/upstream-readme.txt`.

## Pinned commit metadata

```text
commit=331772c6dd08d087cfa2e50ad69185f11bc5f47d
parent=6b5c69e93f256b6b64038ce163b4528efb37fff4
author=Keith Mok <kmok@cyngn.com>
author_date=2020-03-20T14:31:11+08:00
committer=Vitaliy Tomin <highwaystar.ru@gmail.com>
commit_date=2020-05-30T21:48:02+08:00
subject=drivers: input: gt9xx_tb8704: Send KEY_WAKEUP for double-tap event
```

The pinned commit is contained by the upstream `lineage-17.1` branch. The
historical reboot-to-recovery fix
`7d2feae5daa60b7e6521f16eadf04e24989ff8f4` is an ancestor of the pinned
commit; it was not cherry-picked or reapplied.

## Verification

- Build performed: no
- Device access: no
- Functional kernel changes: none
- Defconfig changes: none
- Build fixes: none
- TB8704 DTS files are present under `arch/arm/boot/dts/qcom/tb8704/`.
