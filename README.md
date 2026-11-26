# LocalSend Lite for KOReader

[简体中文](README.zh-CN.md)

Send and receive books and other files over your local network using [LocalSend v2.2](https://github.com/localsend/protocol).

**About 28 KB to download.** The v0.1.4 ZIP contains a Lua plugin that runs entirely inside KOReader and reuses its bundled networking libraries. The network service starts on demand when you send or receive files.

[Changelog](CHANGELOG.md)

## Install

1. Download the ZIP from [GitHub Releases](https://github.com/bbsteel/localsend-lite.koplugin/releases/latest).
2. Extract the `localsend-lite.koplugin` directory into KOReader's `plugins/` directory. On Kindle, the resulting path on the USB drive is `koreader/plugins/localsend-lite.koplugin/main.lua`.
3. Eject the Kindle and restart KOReader. Open **Tools → LocalSend Lite**. The entry appears first in the default Tools menu.

If you're upgrading from the pre-Lite development build of this project, move its `localsend.koplugin` directory out of `plugins/` before installing Lite. Lite has its own settings and creates its own device identity on first use. Run one LocalSend receiver at a time when two plugins use the default port.

## Send and receive

- **Send files** opens a file picker. Tap one or more files, tap the checkmark, then select a discovered device. Long-press a file to preview it, or use **Preview files** to review the selected batch. After sending, the device list stays open so you can reuse the batch or choose new files.
- **Receive files** displays your device nickname and save folder. Leave the screen open, select the nickname on the sending device, then accept on your reader. The receiver shows the current file, progress, transfer speed, and the number of files saved. The default destination is the current library folder. New files receive a numeric suffix if the name is already taken.
- **Stop** closes the receiver. On Kindle, the plugin allows the LocalSend ports on Wi-Fi while in use and removes its firewall rules when it stops.

The plugin interface supports English, Simplified Chinese, and Traditional Chinese. Nicknames follow the KOReader interface language and stay stable for the same device identity and language. The plugin includes 22 nickname word lists and falls back to English. File bodies stream in 32 KiB chunks. Transfers support up to 128 files per batch and files smaller than 4 GiB. Plain `.txt` files transfer normally. The plugin's discovery uses IPv4 multicast at `224.0.0.167:53317` and its receiver uses HTTPS on TCP port `53317` by default.

## Screenshots

Captured in the KOReader emulator.

<img src="docs/screenshots/receive-waiting.png" alt="Waiting for files" width="245"> <img src="docs/screenshots/receive-batch.png" alt="Batch confirmation" width="245"> <img src="docs/screenshots/receive-progress.png" alt="Transfer in progress" width="245">

## Verification

Run the tests against KOReader v2026.07.1 on Linux. Each runner defaults to the installed KOReader Flatpak, or accepts a path to a KOReader directory containing `luajit` and `setupkoenv.lua`. Temporary test files are created under `~/tmp` and removed at exit.

| Command | Coverage |
| --- | --- |
| `bash tests/run-koreader-ui.sh` | Plugin loading, Tools menu placement, receive progress, Chinese interface and mocked Kindle startup diagnostics |
| `bash tests/run-loopback.sh` | Discovery, delayed fourth upload, TLS fingerprints, checksum handling, cancellation, firewall cleanup and log rotation |
| `bash tests/run-official-cli.sh` | Discovery by Chinese nickname, four-file receiving batch and byte-for-byte HTTPS transfers in both directions with the official LocalSend CLI 1.18.2 |

An earlier build was installed on a Kindle Paperwhite 2. The v0.1.4 sending changes have been tested on Linux, with Kindle startup simulated.

## Troubleshooting

The plugin writes a local diagnostic log when you open **Send files** or **Receive files**. On Kindle, look for `koreader/settings/localsend-lite.log` and, if present, `localsend-lite.log.1` on the USB drive. Each file is capped at 64 KiB. The logs include startup and firewall events, filenames, upload status, and errors. If a transfer fails, note its time and the error shown on the sending device before collecting the logs. Review filenames and device nicknames before sharing them.
