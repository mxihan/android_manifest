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
