<h1 align="center" style="padding-top: 60px;padding-bottom: 40px;">
    <a href="https://electerm.org">
        <img src="https://github.com/electerm/electerm-resource/raw/master/static/images/electerm.png" alt="electerm" />
    </a>
</h1>

[![npm version](https://img.shields.io/npm/v/electerm.svg)](https://www.npmjs.com/package/electerm)
[![license](https://img.shields.io/badge/license-MIT-blue.svg)](https://github.com/electerm/electerm-npm/blob/master/LICENSE)
[![GitHub release](https://badgers.space/github/release/electerm/electerm?corner_radius=m)](https://github.com/electerm/electerm/releases)

[English](https://github.com/electerm/electerm-npm/blob/master/README.md) | [中文](https://github.com/electerm/electerm-npm/blob/master/README_cn.md)

> 这是 **electerm 的 npm 安装器**，不是 electerm 应用本身。
> 应用源码在 [electerm/electerm](https://github.com/electerm/electerm)。
> 本仓库（[electerm/electerm-npm](https://github.com/electerm/electerm-npm)）只包含 npm 安装器和发布流程。

## 这是什么？

`npm i -g electerm` 会安装一个小安装器：自动识别你的操作系统 / CPU 架构，从 [electerm releases](https://github.com/electerm/electerm/releases) 下载对应的预编译二进制包，并通过 `electerm` 命令启动。

包版本与 electerm 发行版版本一一对应，例如 `electerm@5.5.16` 安装的就是 `v5.5.16` 的二进制包。

## 安装

要求：Node.js >= 16。

```bash
npm i -g electerm
```

安装过程会自动执行 `postinstall`（`node npm/install.js`）下载对应平台的二进制包，无需手动下载。

如果下载失败（网络 / 代理问题），无需重装包，直接重试即可：

```bash
node "$(npm root -g)/electerm/npm/install.js"
```

### 使用 GitHub 代理（可选）

如果 `github.com` 访问缓慢或被屏蔽，可在安装前设置 `GITHUB_PROXY`：

```bash
GITHUB_PROXY=https://your-proxy.example npm i -g electerm
```

只有 `github.com` 的 URL 会走代理。

## 使用

```bash
electerm
```

- macOS：把 `electerm.app` 安装到 `/Applications`，然后启动 `/Applications/electerm.app/Contents/MacOS/electerm`。
- Windows：启动 `<global-node-modules>/electerm/electerm/electerm.exe`。
- Linux：启动 `<global-node-modules>/electerm/electerm/electerm`（除非 `chrome-sandbox` 已是 root 属主且权限为 `4755`，否则会自动加上 `--no-sandbox`）。

多余的命令行参数会透传给应用，例如 `electerm --help`。

## 升级 / 重装 / 卸载

```bash
# 升级到最新版
npm i -g electerm@latest

# 安装指定版本（对应 electerm 发行版 tag vX.Y.Z）
npm i -g electerm@5.5.16

# 强制重新下载当前版本的二进制包
node "$(npm root -g)/electerm/npm/install.js"

# 卸载
npm rm -g electerm
```

macOS 上卸载 npm 包不会删除 `/Applications/electerm.app`，如需删除请手动处理。

## 支持的平台

| 系统 | 架构 | 安装的包 |
| --- | --- | --- |
| Windows 10+ | x64, ia32 | `electerm-<ver>-win-x64.tar.gz` |
| Windows 10+ | arm64 | `electerm-<ver>-win-arm64.tar.gz` |
| Windows 7 | x64 | `electerm-<ver>-win7.tar.gz` |
| macOS 11+ | x64 | `electerm-<ver>-mac-x64.dmg` |
| macOS 11+ | arm64 | `electerm-<ver>-mac-arm64.dmg` |
| macOS 10.x | x64 | `electerm-<ver>-mac10-x64.dmg` |
| Linux | x64, arm64, armv7l, loong64 | `electerm-<ver>-linux-<arch>.tar.gz` |
| Linux（glibc < 2.34） | x64, arm64, armv7l, loong64 | `electerm-<ver>-linux-<arch>-legacy.tar.gz` |
| Linux | riscv64, ppc64le | `electerm-<ver>-linux-<arch>.tar.gz`（无 legacy 版本） |

说明：

- 64 位机器上的 32 位 Node（`ia32`）会安装 64 位构建。
- glibc < 2.34 的 Linux（如 UOS / 麒麟 / Ubuntu 18.04），在有 `-legacy`（glibc 2.17+）构建时会自动选用。
- `riscv64` / `ppc64le`（仅小端）没有 `-legacy` 构建，即使是旧 glibc 也会用标准构建。
- Linux 可选开启 Electron sandbox：
  `sudo chown root:root <pkg>/electerm/chrome-sandbox && sudo chmod 4755 <pkg>/electerm/chrome-sandbox`。

## 工作原理

1. `postinstall` 从 `https://electerm.org/version.html` 读取最新版本，从 `https://electerm.org/data/electerm-github-release.json` 读取发行版资源列表。
2. 根据 `os.platform()` / `os.arch()`（外加 win7 / mac10 / linux-legacy 识别）确定要下载的资源。
3. 下载并解压到 `<package>/electerm/`（Linux/Windows），macOS 则把 `.app` 安装到 `/Applications`。
4. `electerm` 可执行脚本（`npm/electerm`）负责找到并启动该二进制文件。

发布由 electerm 发行版自动触发（[`npm.yml`](https://github.com/electerm/electerm-npm/blob/master/.github/workflows/npm.yml)），详见 [CONTRIBUTING.md](https://github.com/electerm/electerm-npm/blob/master/CONTRIBUTING.md)。

## 关于 electerm

Electerm 是开源的 terminal / ssh / sftp / telnet / serialport / RDP / VNC / Spice / ftp 客户端，支持 Linux、Mac、Windows、Android、HarmonyOS 和 iOS。

同时支持老旧 / 特殊系统：Ubuntu 18、Windows 7、macOS 10+、UOS / 麒麟 / 龙芯 LoongArch（新世界和旧世界）、riscv64 和 ppc64le Linux。

主要特性（完整文档见[主仓库](https://github.com/electerm/electerm)）：

- 终端 + 文件管理，ssh/sftp/ftp/telnet/serialport/RDP/VNC/Spice
- 全局快捷键切换窗口（默认 `ctrl + 2`）
- 多语言支持（[electerm-locales](https://github.com/electerm/electerm-locales)）
- 公钥 / 密码 / ssh-agent / 证书 / OTP 登录，隧道、跳板机
- Zmodem（`rz`/`sz`）与 [trzsz](https://github.com/trzsz/trzsz)（`trz`/`tsz`）
- 主题、透明窗口、背景图
- 全局 / 会话代理、快捷命令、经 gist / webdav / electerm cloud 同步
- AI 助手、MCP 组件、deep link（`ssh://`、`telnet://`）、命令行用法

相关链接 / 项目：

- [官网 / 下载](https://electerm.org) · [Releases](https://github.com/electerm/electerm/releases) · [视频教程](https://electerm.org/videos)
- [主题](https://theme.electerm.org) · [Docker](https://github.com/electerm/electerm-web-docker) · [在线演示](https://demo.electerm.org)
- [Android](https://github.com/electerm/electerm-android) · [HarmonyOS](https://github.com/electerm/electerm-harmony) · [网页版](https://github.com/electerm/electerm-web)
- [electerm online](https://cloud.electerm.org) · [electerm AI](https://ai.electerm.org)
- [Apple App Store](https://apps.apple.com/cn/app/electerm/id6792971552) · [华为应用市场](https://appgallery.huawei.com/app/detail?id=org.electerm.electerm) · [Microsoft Store](https://www.microsoft.com/store/apps/9NCN7272GTFF) · [Snap Store](https://snapcraft.io/electerm)
- [deb 源](https://repos.electerm.org/deb) · [rpm 源](https://repos.electerm.org/rpm)

## 问题反馈 / 支持

- 安装器问题（`npm i -g electerm` 失败、下错二进制、启动失败）：请提 issue 到 [electerm-npm](https://github.com/electerm/electerm-npm/issues)。
- 应用本身的 bug / 功能建议：请提 issue 到 [electerm/electerm](https://github.com/electerm/electerm/issues)，或发起 [discussion](https://github.com/electerm/electerm/discussions)。
- 语言文件：[electerm-locales](https://github.com/electerm/electerm-locales)。

## 参与贡献

本仓库只放安装器（`npm/`）和发布流程。目录结构、测试（`npm test`、`npm run test-network`、`npm run pack-check`）与发行版驱动的发布流程见 [CONTRIBUTING.md](https://github.com/electerm/electerm-npm/blob/master/CONTRIBUTING.md)。

## 更新日志

见 [electerm releases](https://github.com/electerm/electerm/releases) —— npm 版本与之 1:1 对应。

## License

MIT
