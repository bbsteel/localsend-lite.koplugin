# LocalSend Lite for KOReader

[English](README.md)

通过 [LocalSend v2.2](https://github.com/localsend/protocol) 在局域网内收发电子书和其他文件。

**v0.1.4 安装包仅约 28 KB。** 插件在 KOReader 内运行，复用其自带的 Lua 运行环境和网络库，收发文件时才启动网络服务。

[Changelog](CHANGELOG.md)

## 安装

1. 从 [GitHub Releases](https://github.com/bbsteel/localsend-lite.koplugin/releases/latest) 下载 ZIP。
2. 解压并把整个 `localsend-lite.koplugin` 目录复制到 KOReader 的 `plugins/` 目录。Kindle 的 USB 存储中应能看到 `koreader/plugins/localsend-lite.koplugin/main.lua`。
3. 安全弹出 Kindle，重启 KOReader，打开 **工具 → LocalSend Lite**。默认位于工具菜单首页第一项。

如果你装过本项目更名前的开发版，先把它的 `localsend.koplugin` 移出 `plugins/`。Lite 使用独立设置，首次启动会生成自己的设备身份。两个插件都使用默认端口时，每次只启动一个接收服务。

## 收发文件

- **发送文件** 打开文件选择器。轻点选中一个或多个文件，点勾号确认，再点自动发现的设备。长按单个文件可预览，也可在设备列表点 **预览文件** 检查整批。发送完成后保留设备列表，可以继续发同一批或换一批文件。
- **接收文件** 显示本机昵称和保存目录。保持页面打开，在发送方选中该昵称，然后在阅读器上确认。接收过程中会显示当前文件、进度、速度和已保存的文件数。接收默认使用当前书库目录，遇到同名文件会给新文件加编号。
- **停止** 关闭接收服务。Kindle 版在传输界面打开期间临时放行 Wi‑Fi 上的 LocalSend 端口，并在停止时撤销规则。

插件界面支持英文、简体中文和繁体中文。昵称跟随 KOReader 界面语言，同一设备身份在同一语言下使用固定昵称。插件内置 22 套昵称词库，其他语言使用英文。文件按 32 KiB 分块读写，每批最多 128 个文件，单个小于 4 GiB。普通 `.txt` 附件可正常传输。发现使用 IPv4 组播 `224.0.0.167:53317`，默认接收端口为 HTTPS TCP `53317`。

## 界面截图

截图来自 KOReader 模拟器。

<img src="docs/screenshots/receive-waiting.png" alt="等待接收" width="245"> <img src="docs/screenshots/receive-batch.png" alt="批量确认" width="245"> <img src="docs/screenshots/receive-progress.png" alt="传输进度" width="245">

## 验证

测试基于 Linux 上的 KOReader v2026.07.1。脚本默认使用已安装的 Flatpak 版 KOReader，也可传入含 `luajit` 和 `setupkoenv.lua` 的目录。临时文件写到 `~/tmp`，结束后自动清理。

| 命令 | 验证内容 |
| --- | --- |
| `bash tests/run-koreader-ui.sh` | 插件加载、工具菜单位置、接收进度、中文界面与 Kindle 启动日志模拟 |
| `bash tests/run-loopback.sh` | 自动发现、第四个文件延迟上传、TLS 指纹、校验失败处理、取消、防火墙清理模拟与日志轮转 |
| `bash tests/run-official-cli.sh` | 官方 LocalSend CLI 1.18.2 按中文昵称发现插件，一次发送四个文件，并逐字节核对双向 HTTPS 文件传输 |

更名前的开发版已装到一台 Kindle Paperwhite 2。v0.1.4 的发送流程已在 Linux 上测试，Kindle 启动流程使用模拟验证。

## 问题排查

打开 **发送文件** 或 **接收文件** 时，插件会写入本机诊断日志。用 USB 连接 Kindle 后，可在 `koreader/settings/` 找到 `localsend-lite.log`，轮转后还会有 `localsend-lite.log.1`。每份最多 64 KiB。日志记录启动、防火墙、文件名、上传状态和错误原因。传输失败时，可以记下失败时间和发送端的报错，再与日志对照。分享前可以检查其中的文件名和设备昵称。
