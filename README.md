# Redroid Docker Images with ARM / ARM64 Support

This repository provides Redroid Docker images integrated with **Houdini** or **NDK Translation**, making it possible to run ARM architecture Android apps on an x86_64 platform (such as Intel / AMD processors). These images are especially useful for application testing and integration development.

## Image List

| Houdini Image Tag                       | Source Platform      | Houdini Version            | Houdini64 Version          | Supported Platforms        | Notes                                                             |
|:----------------------------------------|:--------------------:|:--------------------------:|:--------------------------:|:---------------------------|:------------------------------------------------------------------|
| `erstt/redroid:11.0.0_houdini_ChromeOS` | ChromeOS brya R112   | 11.0.1f_y.38795.g          | 11.0.1f_z.38795.g          | Intel (64-bit)             | Verified stable on Intel platform, not yet tested on AMD.         |
| `erstt/redroid:11.0.0_houdini_R127`     | ChromeOS brask R127  | 11.0.2a.y.38870a           | 11.0.2a.z.38870a           | Intel (64-bit)             | Not yet tested.                                                   |
| `erstt/redroid:12.0.0_houdini_WSA`      | WSA 12L              | 12.0.0a_y.38818.m          | 12.0.0a_z.38818.m          | Intel / AMD (64-bit)       | Some apps may have compatibility issues, recommended for testing. |
| `erstt/redroid:13.0.0_houdini_ChromeOS` | ChromeOS nissa R134  | 13.0.1_y.39540.g           | 13.0.1_z.39540.g           | Intel (64-bit)             | Some apps may have compatibility issues, recommended for testing. |

| NDK Translation Image Tag               | Source Platform      | NDK Translation Version | Supported Platforms        | Notes                                                        |
|:----------------------------------------|:--------------------:|:-----------------------:|:---------------------------|:-------------------------------------------------------------|
| `erstt/redroid:11.0.0_ndk_ChromeOS`     | ChromeOS grunt R134  | 0.2.2                   | Intel / AMD (64-bit)       | Verified stable on AMD platform, not yet tested on Intel.    |
| `erstt/redroid:12.0.0_ndk_ChromeOS`     | ChromeOS skyrim R134 | 0.2.3                   | Intel / AMD (64-bit)       | Verified stable on AMD platform, not yet tested on Intel.    |
| `erstt/redroid:13.0.0_ndk_ChromeOS`     | ChromeOS skyrim R134 | 0.2.3                   | Intel / AMD (64-bit)       | Verified stable on AMD platform, not yet tested on Intel.    |

## Usage Suggestions

- The images are only suitable for **x86_64 architecture** devices.
- When starting the container, make sure to configure device mapping and mount options properly.
- If stability is a priority, it’s recommended to choose the **ChromeOS**-based images, especially on Intel processors.
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
      - ro.product.cpu.abilist=x86,armeabi-v7a,armeabi,x86_64,arm64-v8a
      - ro.product.cpu.abilist32=x86,armeabi-v7a,armeabi
      - ro.product.cpu.abilist64=x86_64,arm64-v8a
      - ro.dalvik.vm.isa.arm=x86
      - ro.dalvik.vm.isa.arm64=x86_64
      - ro.enable.native.bridge.exec=1
      - ro.enable.native.bridge.exec64=1
      - ro.dalvik.vm.native.bridge=libhoudini.so
```

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
      - ro.product.cpu.abilist=x86,armeabi-v7a,armeabi,x86_64,arm64-v8a
      - ro.product.cpu.abilist32=x86,armeabi-v7a,armeabi
      - ro.product.cpu.abilist64=x86_64,arm64-v8a
      - ro.dalvik.vm.isa.arm=x86
      - ro.dalvik.vm.isa.arm64=x86_64
      - ro.enable.native.bridge.exec=1
      - ro.enable.native.bridge.exec64=1
      - ro.dalvik.vm.native.bridge=libndk_translation.so
```

---

# 支持 ARM / ARM64 的 Redroid Docker 镜像

本仓库提供集成 **Houdini** 或 **NDK Translation** 的 Redroid Docker 镜像，使得在 x86_64 平台（如 Intel / AMD 处理器）上运行 ARM 架构的 Android 应用成为可能，特别适合用于应用测试与集成开发场景。

## 镜像列表

| Houdini 镜像标签名                       | 源平台               | Houdini 版本               | Houdini64 版本              | 支持平台                   | 说明                                     |
|:----------------------------------------|:--------------------:|:--------------------------:|:--------------------------:|:---------------------------|:-----------------------------------------|
| `erstt/redroid:11.0.0_houdini_ChromeOS` | ChromeOS brya R112   | 11.0.1f_y.38795.g          | 11.0.1f_z.38795.g          | Intel (64-bit)             | 已在 Intel 平台验证运行稳定，AMD 暂未测试。 |
| `erstt/redroid:11.0.0_houdini_R127`     | ChromeOS brask R127  | 11.0.2a.y.38870a           | 11.0.2a.z.38870a           | Intel (64-bit)             | 暂未测试。                                |
| `erstt/redroid:12.0.0_houdini_WSA`      | WSA 12L              | 12.0.0a_y.38818.m          | 12.0.0a_z.38818.m          | Intel / AMD (64-bit)       | 部分应用存在兼容性问题，建议测试验证后使用。 |
| `erstt/redroid:13.0.0_houdini_ChromeOS` | ChromeOS nissa R134  | 13.0.1_y.39540.g           | 13.0.1_z.39540.g           | Intel (64-bit)             | 部分应用存在兼容性问题，建议测试验证后使用。 |

| NDK Translation 镜像标签名               | Source Platform      | NDK Translation 版本    | 支持平台                    | 说明                                        |
|:----------------------------------------|:--------------------:|:-----------------------:|:---------------------------|:--------------------------------------------|
| `erstt/redroid:11.0.0_ndk_ChromeOS`     | ChromeOS grunt R134  | 0.2.2                   | Intel / AMD (64-bit)       | 已在 AMD 平台验证运行稳定，Intel 暂未测试。    |
| `erstt/redroid:12.0.0_ndk_ChromeOS`     | ChromeOS skyrim R134 | 0.2.3                   | Intel / AMD (64-bit)       | 已在 AMD 平台验证运行稳定，Intel 暂未测试。    |
| `erstt/redroid:13.0.0_ndk_ChromeOS`     | ChromeOS skyrim R134 | 0.2.3                   | Intel / AMD (64-bit)       | 已在 AMD 平台验证运行稳定，Intel 暂未测试。    |

## 使用建议

- 镜像仅适用于 **x86_64 架构** 的设备。
- 启动容器时请合理配置设备映射与挂载选项。
- 若对稳定性有较高要求，推荐优先使用基于 **ChromeOS** 的镜像（特别是在 Intel 处理器平台上）。
- 若仅运行支持 arm64-v8a 的游戏（例如明日方舟），建议删除 armeabi-v7a 和 armeabi 的 ABI，以提高稳定性。可以使用以下命令安装：

  ```bash
  pm install --abi arm64-v8a your_app.apk
  ```

- 本设备未获得 Play 保护机制认证，如需使用，请执行以下步骤：

  1. 使用 ADB 获取设备 ID：

     ```bash
     adb root
     adb shell 'sqlite3 /data/data/com.google.android.gsf/databases/gservices.db \
     "select * from main where name = \"android_id\";"'
     ```

     或者

     ```bash
     adb shell
     su
     sqlite3 /data/data/com.google.android.gsf/databases/gservices.db "select * from main where name = \"android_id\";"
     ```

  2. 获取的设备 ID 可在以下网址进行注册：

     [https://www.google.com/android/uncertified/](https://www.google.com/android/uncertified/)

## houdini 镜像 Docker Compose 示例

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
      - ro.product.cpu.abilist=x86,armeabi-v7a,armeabi,x86_64,arm64-v8a
      - ro.product.cpu.abilist32=x86,armeabi-v7a,armeabi
      - ro.product.cpu.abilist64=x86_64,arm64-v8a
      - ro.dalvik.vm.isa.arm=x86
      - ro.dalvik.vm.isa.arm64=x86_64
      - ro.enable.native.bridge.exec=1
      - ro.enable.native.bridge.exec64=1
      - ro.dalvik.vm.native.bridge=libhoudini.so
```

## ndk 镜像 Docker Compose 示例

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
      - ro.product.cpu.abilist=x86,armeabi-v7a,armeabi,x86_64,arm64-v8a
      - ro.product.cpu.abilist32=x86,armeabi-v7a,armeabi
      - ro.product.cpu.abilist64=x86_64,arm64-v8a
      - ro.dalvik.vm.isa.arm=x86
      - ro.dalvik.vm.isa.arm64=x86_64
      - ro.enable.native.bridge.exec=1
      - ro.enable.native.bridge.exec64=1
      - ro.dalvik.vm.native.bridge=libndk_translation.so
```


## Credits

- [remote-android](https://github.com/remote-android)  
- [redroid-script](https://github.com/ayasa520/redroid-script)  
- [Magisk Delta](https://huskydg.github.io/magisk-files/)  
- [vendor_intel_proprietary_houdini](https://github.com/supremegamers/vendor_intel_proprietary_houdini)  
- [chromeos-update-directory](https://github.com/jay0lee/chromeos-update-directory)  
- [android_vendor_google_chromeos-x86](https://github.com/BlissRoms-x86/android_vendor_google_chromeos-x86)
