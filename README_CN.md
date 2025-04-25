## [English](README.md) / 简体中文
# 支持 ARM / ARM64 的 Redroid Docker 镜像

本仓库提供集成 **Houdini** 或 **NDK Translation** 的 Redroid Docker 镜像，使得在 x86_64 平台（如 Intel / AMD 处理器）上运行 ARM 架构的 Android 应用成为可能，特别适合用于应用测试与集成开发场景。

## 镜像列表

| NDK Translation 镜像标签名               | 源平台               | NDK Translation 版本    | 支持平台                    | 说明                                        |
|:----------------------------------------|:--------------------:|:-----------------------:|:---------------------------|:--------------------------------------------|
| `erstt/redroid:11.0.0_ndk_ChromeOS`     | ChromeOS grunt R134  | 0.2.2                   | Intel / AMD                | 已在 Intel / AMD 平台测试运行稳定。           |
| `erstt/redroid:12.0.0_ndk_ChromeOS`     | ChromeOS skyrim R134 | 0.2.3                   | Intel / AMD                | 已在 Intel / AMD 平台测试运行稳定。           |
| `erstt/redroid:13.0.0_ndk_ChromeOS`     | ChromeOS skyrim R134 | 0.2.3                   | Intel / AMD                | 已在 Intel / AMD 平台测试运行稳定。           |
| `erstt/redroid:15.0.0_ndk_AVD`          | AVD_35_ext15_r01     | 0.2.3                   | Intel / AMD                | 已在 Intel / AMD 平台测试运行稳定。仅支持转译arm64-v8a。|

| Houdini 镜像标签名                       | 源平台               | Houdini 版本               | Houdini64 版本              | 支持平台                   | 说明                                     |
|:----------------------------------------|:--------------------:|:--------------------------:|:--------------------------:|:---------------------------|:-----------------------------------------|
| `erstt/redroid:11.0.0_houdini_ChromeOS` | ChromeOS brya R112   | 11.0.1f_y.38795.g          | 11.0.1f_z.38795.g          | Intel                      | 已在 Intel 平台测试运行稳定。              |
| `erstt/redroid:12.0.0_houdini_WSA`      | WSA 12L              | 12.0.0a_y.38818.m          | 12.0.0a_z.38818.m          | Intel / AMD                | 部分应用存在兼容性问题，建议测试验证后使用。 |
| `erstt/redroid:13.0.0_houdini_ChromeOS` | ChromeOS nissa R134  | 13.0.1_y.39540.g           | 13.0.1_z.39540.g           | Intel                      | 部分应用存在兼容性问题，建议测试验证后使用。 |

#### 附加说明：
以下镜像版本也可用：

`erstt/<android_version>_<houdini_or_ndk><_magisk><_litegapps>_ChromeOS`

- `_magisk`（可选）：预装 Magisk
- `_litegapps`（可选）：预装轻量级 Google Apps

## 使用建议

- 镜像仅适用于 **x86_64 架构** 的设备。
- 启动容器时请合理配置设备映射与挂载选项。
- 在哪里提交问题: [https://github.com/ERSTT/redroid/issues⁠](https://github.com/ERSTT/redroid/issues)
- 若仅运行支持 arm64-v8a 的游戏（例如明日方舟），建议删除 armeabi-v7a 和 armeabi 的 ABI，以提高稳定性。
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

## NDK 镜像

### Docker / Podman 命令行示例

```bash
docker run -itd --rm --privileged \
  --pull always \
  -v /path/to/your/directory:/data \
  -p 5555:5555 \
  <ndk_image_tag>
```

```bash
podman run -itd --rm --privileged \
  --pull always \
  -v /path/to/your/directory:/data \
  -p 5555:5555 \
  <ndk_image_tag>
```

### Docker Compose 示例

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
```

## Houdini 镜像

### Docker / Podman 命令行示例

```bash
docker run -itd --rm --privileged \
  --pull always \
  -v /path/to/your/directory:/data \
  -p 5555:5555 \
  <houdini_image_tag> \
  ro.enable.native.bridge.exec64=1 \
  ro.dalvik.vm.native.bridge=libhoudini.so
```

```bash
podman run -itd --rm --privileged \
  --pull always \
  -v /path/to/your/directory:/data \
  -p 5555:5555 \
  <houdini_image_tag> \
  ro.enable.native.bridge.exec64=1 \
  ro.dalvik.vm.native.bridge=libhoudini.so
```

### Docker Compose 示例

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
      - ro.enable.native.bridge.exec64=1
      - ro.dalvik.vm.native.bridge=libhoudini.so
```

### 其他自定义参数

| 参数                              | 描述                                                                 | 默认值                                           |
|-----------------------------------|----------------------------------------------------------------------|-------------------------------------------------|
| androidboot.redroid_width         | 显示宽度                                                              | 720                                             |
| androidboot.redroid_height        | 显示高度                                                              | 1280                                            |
| androidboot.redroid_fps           | 显示帧率（GPU启用：30；未启用：15）                                     | 30（启用GPU）<br>15（未启用GPU）                 |
| androidboot.redroid_dpi           | 显示DPI                                                               | 320                                             |
| androidboot.use_memfd             | 使用 `memfd` 替代已弃用的 `ashmem`，计划默认启用                        | false                                           |
| androidboot.use_redroid_overlayfs | 使用 `overlayfs` 共享 `/data` 分区<br>`/data-base`: 共享的 data 分区<br>`/data-diff`: 私有数据 | 0                        |
| androidboot.redroid_net_ndns      | DNS服务器数量；未指定时默认使用 `8.8.8.8`                               | 0                                               |
| androidboot.redroid_net_dns<1..N> | DNS服务器                                                             |                                                 |
| androidboot.redroid_net_proxy_type | 代理类型：`static`、`pac`、`none`、`unassigned`                      |                                                 |
| androidboot.redroid_net_proxy_host | 代理服务器地址                                                        |                                                 |
| androidboot.redroid_net_proxy_port | 代理端口                                                             | 3128                                            |
| androidboot.redroid_net_proxy_exclude_list | 排除代理的地址列表，多个用英文逗号分隔                         |                                                  |
| androidboot.redroid_net_proxy_pac | PAC 脚本地址                                                          |                                                  |
| androidboot.redroid_gpu_mode      | GPU模式：`auto`、`host`、`guest`<br>`guest`: 软件渲染<br>`host`: GPU加速<br>`auto`: 自动检测 | guest                       |
| androidboot.redroid_gpu_node      | GPU节点，自动检测                                                     | auto-detect                                       |
| ro.xxx                            | **DEBUG**：允许覆盖 `ro.xxx` 属性，例如：设置 `ro.secure=0` 可默认提供 root adb shell |                                    |

## Credits

- [remote-android](https://github.com/remote-android)  
- [redroid-script](https://github.com/ayasa520/redroid-script)  
- [Magisk Delta](https://huskydg.github.io/magisk-files/)  
- [vendor_intel_proprietary_houdini](https://github.com/supremegamers/vendor_intel_proprietary_houdini)  
- [chromeos-update-directory](https://github.com/jay0lee/chromeos-update-directory)  
- [android_vendor_google_chromeos-x86](https://github.com/BlissRoms-x86/android_vendor_google_chromeos-x86)
- [chromeos_ndk](https://github.com/ER5TT/ndk/tree/grunt_R134)
- [chromeos_houdini](https://github.com/ER5TT/houdini/tree/brask_R127)

