## English / [简体中文](https://github.com/ERSTT/redroid/blob/main/README_CN.md)
# Redroid Docker Images with ARM / ARM64 Support

This repository provides Redroid Docker images integrated with **Houdini** or **NDK Translation**, making it possible to run ARM architecture Android apps on an x86_64 platform (such as Intel / AMD processors). These images are especially useful for application testing and integration development.

## Image List

| NDK Translation Image Tag               | Source Platform      | NDK Translation Version | Supported Platforms        | Notes                                                        |
|:----------------------------------------|:--------------------:|:-----------------------:|:---------------------------|:-------------------------------------------------------------|
| `erstt/redroid:11.0.0_ndk_ChromeOS`     | ChromeOS grunt R134  | 0.2.2                   | Intel / AMD                | Verified stable on Intel / AMD platforms.                    |
| `erstt/redroid:12.0.0_ndk_ChromeOS`     | ChromeOS skyrim R134 | 0.2.3                   | Intel / AMD                | Verified stable on Intel / AMD platforms.                    |
| `erstt/redroid:13.0.0_ndk_ChromeOS`     | ChromeOS skyrim R134 | 0.2.3                   | Intel / AMD                | Verified stable on Intel / AMD platforms.                    |
| `erstt/redroid:15.0.0_ndk_AVD`          | AVD-x86_64-ext15_r01 | 0.2.3                   | Intel / AMD                | Verified stable on Intel / AMD platforms. Translation supports arm64-v8a only.|

| Houdini Image Tag                       | Source Platform      | Houdini Version            | Houdini64 Version          | Supported Platforms        | Notes                                                               |
|:----------------------------------------|:--------------------:|:--------------------------:|:--------------------------:|:---------------------------|:--------------------------------------------------------------------|
| `erstt/redroid:11.0.0_houdini_ChromeOS` | ChromeOS brya R112   | 11.0.1f_y.38795.g          | 11.0.1f_z.38795.g          | Intel                      | Verified stable on Intel platform.                                  |
| `erstt/redroid:12.0.0_houdini_WSA`      | WSA 12L              | 12.0.0a_y.38818.m          | 12.0.0a_z.38818.m          | Intel / AMD                | Potential compatibility issues with some apps; testing recommended. |
| `erstt/redroid:13.0.0_houdini_ChromeOS` | ChromeOS nissa R134  | 13.0.1_y.39540.g           | 13.0.1_z.39540.g           | Intel                      | Potential compatibility issues with some apps; testing recommended. |

## Usage Suggestions

- The images are only suitable for **x86_64 architecture** devices.
- When starting the container, make sure to configure device mapping and mount options properly.
- Where to file issues: [https://github.com/ERSTT/redroid/issues⁠](https://github.com/ERSTT/redroid/issues)
- If you only need to run games supporting arm64-v8a (e.g., Arknights), it’s suggested to remove the armeabi-v7a and armeabi ABIs for better stability. You can use the following command to install:

  ```bash
  pm install --abi arm64-v8a your_app.apk
  ```

- The device has not been certified by the Play Protect mechanism. To proceed, follow these steps:

  1. Use ADB to retrieve the device ID:

     ```bash
     adb root
     adb shell 'sqlite3 /data/data/com.google.android.gsf/databases/gservices.db \
     "select * from main where name = \"android_id\";"'
     ```

     Or

     ```bash
     adb shell
     su
     sqlite3 /data/data/com.google.android.gsf/databases/gservices.db "select * from main where name = \"android_id\";"
     ```

  2. Register the device ID at the following URL:

     [https://www.google.com/android/uncertified/](https://www.google.com/android/uncertified/)

# Configuration

## NDK Image Docker Compose Example

```yaml
services:
  redroid:
    image: <ndk_image_tag>
    tty: true
    stdin_open: true
    privileged: true
    ports:
      - 5555:5555
    volumes:
      - /path/to/your/directory:/data
    command:
      - androidboot.redroid_gpu_mode=auto
      - androidboot.redroid_fps=60
      - androidboot.use_memfd=1
      - ro.product.cpu.abilist=x86_64,x86,arm64-v8a,armeabi-v7a,armeabi
      - ro.product.cpu.abilist32=x86,armeabi-v7a,armeabi
      - ro.product.cpu.abilist64=x86_64,arm64-v8a
      - ro.dalvik.vm.isa.arm=x86
      - ro.dalvik.vm.isa.arm64=x86_64
      - ro.enable.native.bridge.exec=1
      - ro.dalvik.vm.native.bridge=libndk_translation.so
```

## Houdini Image Docker Compose Example

```yaml
services:
  redroid:
    image: <houdini_image_tag>
    tty: true
    stdin_open: true
    privileged: true
    ports:
      - 5555:5555
    volumes:
      - /path/to/your/directory:/data
    command:
      - androidboot.redroid_gpu_mode=auto
      - androidboot.redroid_fps=60
      - androidboot.use_memfd=1
      - ro.product.cpu.abilist=x86_64,x86,arm64-v8a,armeabi-v7a,armeabi
      - ro.product.cpu.abilist32=x86,armeabi-v7a,armeabi
      - ro.product.cpu.abilist64=x86_64,arm64-v8a
      - ro.dalvik.vm.isa.arm=x86
      - ro.dalvik.vm.isa.arm64=x86_64
      - ro.enable.native.bridge.exec=1
      - ro.enable.native.bridge.exec64=1
      - ro.dalvik.vm.native.bridge=libhoudini.so
```

| Param                             | Description                                                                                           | Default                                        |
|----------------------------------|--------------------------------------------------------------------------------------------------------|------------------------------------------------|
| androidboot.redroid_width        | display width                                                                                          | 720                                            |
| androidboot.redroid_height       | display height                                                                                         | 1280                                           |
| androidboot.redroid_fps          | display FPS                                                                                            | 30(GPU enabled)<br>15 (GPU not enabled)        |
| androidboot.redroid_dpi          | display DPI                                                                                            | 320                                            |
| androidboot.use_memfd            | use `memfd` to replace deprecated `ashmem`<br>plan to enable by default                                | false                                          |
| androidboot.use_redroid_overlayfs| use `overlayfs` to share `data` partition<br>`/data-base`: shared `data`<br>`/data-diff`: private data | 0                                              |
| androidboot.redroid_net_ndns     | number of DNS server, `8.8.8.8` will be used if no DNS server specified                                | 0                                              |
| androidboot.redroid_net_dns<1..N>| DNS                                                                                                    |                                                |
| androidboot.redroid_net_proxy_type | Proxy type; choose from: `static`, `pac`, `none`, `unassigned`                                       |                                                |
| androidboot.redroid_net_proxy_host|                                                                                                       |                                                |
| androidboot.redroid_net_proxy_port|                                                                                                       | 3128                                           |
| androidboot.redroid_net_proxy_exclude_list | comma separated list                                                                         |                                                |
| androidboot.redroid_net_proxy_pac|                                                                                                        |                                                |
| androidboot.redroid_gpu_mode     | choose from: `auto`, `host`, `guest`;<br>`guest`: use software rendering;<br>`host`: use GPU accelerated rendering;<br>`auto`: auto detect | guest      |
| androidboot.redroid_gpu_node     |                                                                                                        | auto-detect                                    |
| ro.xxx                           | **DEBUG** purpose, allow override `ro.xxx` prop; For example, set `ro.secure=0`, then root adb shell provided by default |                              |


## Credits

- [remote-android](https://github.com/remote-android)  
- [redroid-script](https://github.com/ayasa520/redroid-script)  
- [Magisk Delta](https://huskydg.github.io/magisk-files/)  
- [vendor_intel_proprietary_houdini](https://github.com/supremegamers/vendor_intel_proprietary_houdini)  
- [chromeos-update-directory](https://github.com/jay0lee/chromeos-update-directory)  
- [android_vendor_google_chromeos-x86](https://github.com/BlissRoms-x86/android_vendor_google_chromeos-x86)
- [chromeos_ndk](https://github.com/ER5TT/ndk/tree/grunt_R134)
- [chromeos_houdini](https://github.com/ER5TT/houdini/tree/brask_R127)
