# SCHOOL_BUILD 定制说明（school-custom 分支）

本 fork 在上游 RustDesk 基础上做校内课堂投屏定制（详见 `../classroom-cast/` 与 `../docs/`）。

## 定制内容

| 文件 | 修改 | 说明 |
| --- | --- | --- |
| `libs/hbb_common/src/config.rs` | `RENDEZVOUS_SERVERS` → `192.168.8.10`；`RS_PUB_KEY` → 占位 | 客户端默认连校内 hbbs/hbbr；中继自动推导 21117 |
| `flutter/android/app/src/main/AndroidManifest.xml` | `android:label` → `课堂投屏` | 区分官方版 |

## 部署服务器后必做

hbbs 首次启动会在数据目录生成密钥对，公钥在 `id_ed25519.pub`（`docker compose logs hbbs` 也会打印）。**把该公钥内容替换 `config.rs` 里的 `REPLACE_WITH_HBBS_ID_ED25519_PUB` 再编译**，否则客户端连校内服务器会校验失败。

## 编译 Android APK

环境要求：Rust stable（含 `aarch64-linux-android` 等 android 目标）、Android SDK/NDK、Flutter stable。

```bash
git clone --recursive https://github.com/uedius/rustdesk.git
cd rustdesk && git switch school-custom
python build.py --flutter --android apk
# 产物：flutter/build/app/outputs/flutter-apk/app-release.apk
```

## 安装后验收（开箱即用点）

1. 主界面正常显示本机 RustDesk ID；
2. 「设置 → 网络」中 ID 服务器已预填 `192.168.8.10`，Key 已预填（无需手动输入）；
3. 设置一次永久密码（Android 沙箱限制无法预置），把设备 ID 登记进 Classroom Cast 控制层 `devices` 表。

## 维护约定

- 所有定制只落在 `school-custom` 分支；升级上游版本 = merge upstream/master → 解决冲突 → 重新编译。
- `libs/hbb_common` 为子模块：公钥/服务器改动需先在 hbb_common 仓库（建议 fork 到 uedius/hbb_common）提交，再在主仓库更新子模块指针。
