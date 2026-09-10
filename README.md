# Sunshine for LazyCat

Sunshine is a self-hosted game stream host for Moonlight with low-latency hardware and software encoding, web configuration and client pairing.

主页：https://app.lizardbyte.dev/Sunshine/

## 运行范围

本包按官方容器模板封装 `lizardbyte/sunshine:v2026.909.201822-ubuntu-26.04`，目标 amd64，要求懒猫微服 1.6.0 或更新版本。

入口为 Sunshine 自带的 HTTPS Web 管理后台（47990），用于创建登录凭据、配置及 Moonlight 配对。上游使用自签名证书，仅针对容器后端关闭证书校验。保留手动登录，不添加文件选择器。

官方镜像并非完整桌面/游戏环境。本包不安装 Steam、游戏或虚拟桌面，也不自动连接宿主 X11/Wayland、PulseAudio/PipeWire 会话。可用串流仍需要可捕获的画面、音频和输入环境。GPU 加速声明不保证驱动、编码器及显示捕获可用；NVIDIA 运行时和输入设备需按实际硬件验证。**本包完成构建与发布不代表已完成游戏串流实测。**

## 映射

- `/lzcapp/var/config` 映射 `/home/lizard/.config/sunshine`，即镜像 `/config` 符号链接的目标。
- 使用镜像固定 UID/GID 1001，并通过 `run_as` 设置持久化目录属主；PUID/PGID 不能仅靠运行时环境变量改变镜像内用户身份。
- 时区为 `Asia/Shanghai`。保留 `ipc: host`，声明 GPU 加速。
- TCP 47984–47990、48010 与 UDP 47998–48000 使用 L4 ingress；端口范围不设置固定目标端口，保持一一对应。
- Moonlight 手动添加应用域名，不使用浏览器 HTTPS URL；组播自动发现与低延迟连接需在实际网络上测试。

## 构建与发布

```sh
mkdir -p dist
lzc-cli project release -o dist/application.lpk
lzc-cli lpk info dist/application.lpk
```

仅发布喵喵商店，使用 `docker.1ms.run` 镜像模式。首次版本为 `2026.909.201822`；每日检查 `vX.Y.Z-ubuntu-26.04` 镜像，提取 `X.Y.Z` 作为包版本并校验 amd64 摘要与上游一致。

组织 Secrets：`APPSTORE_URL`、`APPSTORE_TOKEN`，以及可选的 `PRIVATE_STORE_GROUP_CODES`。喵喵商店引用经过 SHA256 验证的 `community.lazycat.app.sunshine-v<version>.lpk` GitHub Release 文件。

图标由用户提供。上游源码和许可：https://github.com/LizardByte/Sunshine
