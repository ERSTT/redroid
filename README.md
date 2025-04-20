# Adding ARM and ARM64 Support to Redroid Docker Images

This repository provides two Redroid Docker images with **Houdini** integration, enabling Android ARM binary translation on x86_64 platforms. These images are particularly useful for testing ARM apps on Intel/AMD machines using Redroid.

## Image List

| Tag Name                   | Base Platform  | Houdini Version          | Houdini64 Version         | Supported Platforms      | Notes                                                                 |
|----------------------------|----------------|---------------------------|----------------------------|---------------------------|------------------------------------------------------------------------|
| `11.0.0_houdini_ChromeOS`  | ChromeOS brya  | 11.0.1f_y.38795.g         | 11.0.1f_z.38795.g          | Intel (64-bit)            | Verified to run stably on Intel platforms. Not yet tested on AMD.     |
| `12.0.0_houdini_WSA`       | WSA 12L        | 12.0.0a_y.38818.m         | 12.0.0a_z.38818.m          | Intel / AMD (64-bit)      | Compatibility issues found with some applications. Testing recommended before use. |

## Recommended Usage

- Make sure to run on x86_64 machines.
- Use proper device and mount options when launching Redroid containers.
- Prefer the `ChromeOS` image for better stability, especially on Intel systems.

- The device isn't Play Protect certified

    1. Run the following command on host:

        ```bash
        adb root
        adb shell 'sqlite3 /data/data/com.google.android.gsf/databases/gservices.db \
        "select * from main where name = \"android_id\";"'

        # or

        adb shell
        su
        sqlite3 /data/data/com.google.android.gsf/databases/gservices.db "select * from main where name = \"android_id\";"
        ```

    2. Grab device ID and register on this website:  
       [https://www.google.com/android/uncertified/](https://www.google.com/android/uncertified/)

```yaml
services:
  redroid:
    image: <your_redroid_image>
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
      - persist.sys.nativebridge=1
      - ro.product.cpu.abilist=x86,armeabi-v7a,armeabi,x86_64,arm64-v8a
      - ro.product.cpu.abilist32=x86,armeabi-v7a,armeabi
      - ro.product.cpu.abilist64=x86_64,arm64-v8a
      - ro.dalvik.vm.isa.arm=x86
      - ro.dalvik.vm.isa.arm64=x86_64
      - ro.enable.native.bridge.exec=1
      - ro.enable.native.bridge.exec64=1
      - ro.dalvik.vm.native.bridge=libhoudini.so
```
---

# 添加了 ARM 和 ARM64 支持的 Redroid Docker 镜像

本仓库提供两个在 **Redroid 镜像基础上集成 Houdini** 的 Docker 镜像，使得在 x86_64 平台上运行 ARM 架构 Android 应用成为可能，适用于 Intel 或 AMD 处理器环境，尤其适合使用 Redroid 进行测试或集成。

## 镜像列表

| 标签名                     | 基础平台       | Houdini 版本              | Houdini64 版本            | 支持平台              | 说明                                                         |
|----------------------------|---------------|---------------------------|---------------------------|----------------------|--------------------------------------------------------------|
| `11.0.0_houdini_ChromeOS`  | ChromeOS brya | 11.0.1f_y.38795.g         | 11.0.1f_z.38795.g         | Intel (64位)         | 已在 Intel 平台上验证运行稳定，AMD 暂未测试。               |
| `12.0.0_houdini_WSA`       | WSA 12L       | 12.0.0a_y.38818.m         | 12.0.0a_z.38818.m         | Intel / AMD (64位)   | 部分应用存在兼容性问题，建议在测试验证后使用。               |


## 使用建议

- 仅适用于 x86_64 架构的设备。
- 启动容器时请合理配置设备映射和挂载选项。
- 若对稳定性有要求，优先选择 `ChromeOS` 镜像版本，尤其是在 Intel 处理器上。
- 如果只跑支持arm64-v8a的游戏(例如明日方舟)则建议删除armeabi-v7a和armeabi的ABI，运行起来会更稳定一些 or pm install --abi arm64-v8a your_app.apk
- 此设备未获得 Play 保护机制认证

    1. 使用ADB运行以下命令

        ```bash
        adb root
        adb shell 'sqlite3 /data/data/com.google.android.gsf/databases/gservices.db \
        "select * from main where name = \"android_id\";"'

        # or

        adb shell
        su
        sqlite3 /data/data/com.google.android.gsf/databases/gservices.db "select * from main where name = \"android_id\";"
        ```

    2. 获取的 设备ID 在此网站上注册:  
       [https://www.google.com/android/uncertified/](https://www.google.com/android/uncertified/)

```yaml
services:
  redroid:
    image: <your_redroid_image>
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
      - persist.sys.nativebridge=1
      - ro.product.cpu.abilist=x86,armeabi-v7a,armeabi,x86_64,arm64-v8a
      - ro.product.cpu.abilist32=x86,armeabi-v7a,armeabi
      - ro.product.cpu.abilist64=x86_64,arm64-v8a
      - ro.dalvik.vm.isa.arm=x86
      - ro.dalvik.vm.isa.arm64=x86_64
      - ro.enable.native.bridge.exec=1
      - ro.enable.native.bridge.exec64=1
      - ro.dalvik.vm.native.bridge=libhoudini.so
```

## Credits

- [remote-android](https://github.com/remote-android)  
- [redroid-script](https://github.com/ayasa520/redroid-script)  
- [Magisk Delta](https://huskydg.github.io/magisk-files/)  
- [vendor_intel_proprietary_houdini](https://github.com/supremegamers/vendor_intel_proprietary_houdini)  
- [chromeos-update-directory](https://github.com/jay0lee/chromeos-update-directory)  
- [android_vendor_google_chromeos-x86](https://github.com/BlissRoms-x86/android_vendor_google_chromeos-x86)
