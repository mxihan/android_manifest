# Meizu 21 Pro build manifest

This branch adds the public Meizu 21 Pro source set used by the PixelOS 17 build:

- `mxihan/android_device_meizu_meizu21Pro`
- `mxihan/android_device_meizu_meizu21Pro-meizucamera`
- `mxihan/android_hardware_meizu`
- `mxihan/android_kernel_meizu_sm8650`
- `mxihan/meizu21pro-droidspaces-kernel`

The DroidSpaces project is consumed as a prebuilt under `prebuilts/meizu/droidspaces`. The manifest keeps unchanged Meizu kernel module projects on `meizu-8650-kernel` and Qualcomm device-tree dependencies on LineageOS. Proprietary `vendor/meizu` files are not redistributed here; they must be supplied separately from a legally obtained matching PixelOS build source or vendor package.

Initialize with:

```bash
repo init -u https://github.com/mxihan/android_manifest.git -b meizu21pro-droidspaces --git-lfs
repo sync
```

## Validated November 13, 2026 source set

The validated release is PixelOS 17, Android SDK 37, security patch
`2026-11-01`, incremental version `1794511064`. Its full OTA SHA-256 is
`19979bf9f96c05e14e85dd03a63c43202fd6a86da0420417c70a380bd3b17276`.

The five modified source forks use branch `pixelos17-droidspaces`. This
manifest pins their commits and the curated build inputs.

| Repository | Commit | Preserved changes |
| --- | --- | --- |
| `mxihan/android_device_meizu_meizu21Pro` | `cc9123f3948cce881acbe4303dcbaa73d6d8e7a5` | DroidSpaces board configuration, built-in NR/Wi-Fi signal thresholds, two-microphone VoIP Endfire processing, IR properties and permissions |
| `mxihan/android_kernel_meizu_sm8650` | `ddd872579d674cdd74033a30d37cd8de1286f5f3` | DroidSpaces SYSVIPC KABI fields, public trust certificate, five GCC carrier rates, Meizu clock-backed IR and precise worker timing |
| `mxihan/android_build` | `a80300884c2b6e7acb4b75598a85e649dbd12e1c` | KernelSU init_boot processing before AVB and inclusion in target-files |
| `mxihan/android_build_soong` | `0344627080efe36ec95ceae17cab527839896914` | Forward explicit Go host memory and concurrency settings |
| `mxihan/android_packages_apps_CarrierConfig` | `8cf047f7153ee023b9348bae7ad1796df8289bbf` | Provider version 2 to refresh signal configuration after a retained-data upgrade |
| `mxihan/meizu21pro-droidspaces-kernel` | `d98612fa28f478084f12eb9254052e1d30be56ed` | Validated IR modules, official KernelSU LKM inputs, reproducible image preparation and LG remote configuration tool |

The camera and Meizu hardware projects are pinned to the existing forks at
`cece1c826ac918ba9691479331c9db93f05bb539` and
`d1355f82b0b209cfd2a9b24e1340b473ae38bbb6` respectively.

## Reuse the validated source

`source-manifest.lock.xml` pins all 1,237 projects from the validated build,
including the newly committed device fixes. Initialize a fresh checkout with
the locked manifest to reproduce that source set.

```bash
repo init -u https://github.com/mxihan/android_manifest.git \
  -b meizu21pro-droidspaces -m source-manifest.lock.xml --git-lfs
repo sync
```

When migrating the earlier build tree, save its
`.repo/local_manifests/meizu21pro.xml` outside `local_manifests` before using
this manifest. The projects are supplied directly by this manifest.

Supply the matching proprietary `vendor/meizu` tree and the four base images
extracted from `PixelOS_meizu21Pro-17.0-20260928-0803.zip`. The preparation
script verifies the base hashes and reconstructs the exact vendor boot and
module images used for the validated release. The generated device inputs
are consumed from `device/meizu/meizu21Pro/droidspaces-prebuilt/`.

```bash
source build/envsetup.sh
breakfast meizu21Pro userdebug
m mkfs.erofs fsck.erofs
python3 prebuilts/meizu/droidspaces/scripts/prepare_pixelos_prebuilts.py \
  --android-root "$PWD" --base-images /path/to/base-images
export GOGC=20 GOMEMLIMIT=20GiB GOMAXPROCS=8
m pixelos -j12
```

The validated package uses the published DroidSpaces Image with SHA-256
`7f8d7ed65ef5a636c11b952bbcdb534d7bf84cff6ffd17504b3e9fc27fb8a6d1`
and the matching rebuilt GCC/IR modules. KernelSU Manager is installed
separately by the user. Builds produce a full manual A/B OTA.

The LG remote profile correction is supplied as a host-side tool with
reversible SQL generation. Its guide covers selecting the profile and
exporting a quiescent database. The build input guide also records module
source provenance, rebuild prerequisites, and runtime validation.
[Device input and remote configuration guide](https://github.com/mxihan/meizu21pro-droidspaces-kernel/blob/main/docs/pixelos17-build.md)
