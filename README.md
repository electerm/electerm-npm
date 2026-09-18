<h1 align="center" style="padding-top: 60px;padding-bottom: 40px;">
    <a href="https://electerm.org">
        <img src="https://github.com/electerm/electerm-resource/raw/master/static/images/electerm.png" alt="electerm" />
    </a>
</h1>

[![npm version](https://img.shields.io/npm/v/electerm.svg)](https://www.npmjs.com/package/electerm)
[![license](https://img.shields.io/badge/license-MIT-blue.svg)](https://github.com/electerm/electerm-npm/blob/master/LICENSE)
[![GitHub release](https://badgers.space/github/release/electerm/electerm?corner_radius=m)](https://github.com/electerm/electerm/releases)

[English](https://github.com/electerm/electerm-npm/blob/master/README.md) | [中文](https://github.com/electerm/electerm-npm/blob/master/README_cn.md)

> This is the **npm installer for electerm**, not the electerm app itself.
> The app source lives in [electerm/electerm](https://github.com/electerm/electerm).
> This repo ([electerm/electerm-npm](https://github.com/electerm/electerm-npm)) only contains the npm installer and its publish workflow.

## What is this?

`npm i -g electerm` installs a small installer that detects your OS / CPU architecture, downloads the matching prebuilt electerm binary from [electerm releases](https://github.com/electerm/electerm/releases), and launches it via the `electerm` command.

The package version always tracks the electerm release version, so `electerm@5.5.16` installs the `v5.5.16` binaries.

## Install

Requirements: Node.js >= 16.

```bash
npm i -g electerm
```

This runs `postinstall` (`node npm/install.js`), which downloads the binary for your platform. No manual download needed.

If the download fails (network / proxy), you can retry without reinstalling the package:

```bash
node "$(npm root -g)/electerm/npm/install.js"
```

### Use a GitHub proxy (optional)

If `github.com` is slow or blocked, set `GITHUB_PROXY` before installing:

```bash
GITHUB_PROXY=https://your-proxy.example npm i -g electerm
```

Only `github.com` URLs are proxied.

## Usage

```bash
electerm
```

- macOS: installs `electerm.app` into `/Applications`, then launches `/Applications/electerm.app/Contents/MacOS/electerm`.
- Windows: launches `<global-node-modules>/electerm/electerm/electerm.exe`.
- Linux: launches `<global-node-modules>/electerm/electerm/electerm` (adds `--no-sandbox` automatically unless `chrome-sandbox` is root-owned with mode `4755`).

Extra CLI args are passed through to the app, e.g. `electerm --help`.

## Upgrade / reinstall / uninstall

```bash
# upgrade to latest
npm i -g electerm@latest

# install a specific version (matches electerm release tag vX.Y.Z)
npm i -g electerm@5.5.16

# force re-download the binary for the current version
node "$(npm root -g)/electerm/npm/install.js"

# uninstall
npm rm -g electerm
```

On macOS, uninstalling the npm package does not remove `/Applications/electerm.app` — delete it manually if needed.

## Supported platforms

| OS | Arch | Asset installed |
| --- | --- | --- |
| Windows 10+ | x64, ia32 | `electerm-<ver>-win-x64.tar.gz` |
| Windows 10+ | arm64 | `electerm-<ver>-win-arm64.tar.gz` |
| Windows 7 | x64 | `electerm-<ver>-win7.tar.gz` |
| macOS 11+ | x64 | `electerm-<ver>-mac-x64.dmg` |
| macOS 11+ | arm64 | `electerm-<ver>-mac-arm64.dmg` |
| macOS 10.x | x64 | `electerm-<ver>-mac10-x64.dmg` |
| Linux | x64, arm64, armv7l, loong64 | `electerm-<ver>-linux-<arch>.tar.gz` |
| Linux (glibc < 2.34) | x64, arm64, armv7l, loong64 | `electerm-<ver>-linux-<arch>-legacy.tar.gz` |
| Linux | riscv64, ppc64le | `electerm-<ver>-linux-<arch>.tar.gz` (no legacy variant) |

Notes:

- 32-bit Node (`ia32`) on a 64-bit host installs the 64-bit build.
- On Linux with glibc < 2.34 (e.g. UOS / Kylin / Ubuntu 18.04), the `-legacy` (glibc 2.17+) build is picked automatically where published.
- `riscv64` / `ppc64le` (little-endian only) have no `-legacy` build; the standard build is used even on old glibc.
- On Linux you may optionally enable the Electron sandbox:
  `sudo chown root:root <pkg>/electerm/chrome-sandbox && sudo chmod 4755 <pkg>/electerm/chrome-sandbox`.

## How it works

1. `postinstall` reads the latest version from `https://electerm.org/version.html` and the release asset list from `https://electerm.org/data/electerm-github-release.json`.
2. It resolves the asset for `os.platform()` / `os.arch()` (plus win7 / mac10 / linux-legacy detection).
3. It downloads and extracts it to `<package>/electerm/` (Linux/Windows) or installs the `.app` to `/Applications` (macOS).
4. The `electerm` bin script (`npm/electerm`) finds and launches that binary.

Publishing is driven by the electerm release itself via [`npm.yml`](https://github.com/electerm/electerm-npm/blob/master/.github/workflows/npm.yml) — see [CONTRIBUTING.md](https://github.com/electerm/electerm-npm/blob/master/CONTRIBUTING.md).

## About electerm

Electerm is an open-sourced terminal / ssh / sftp / telnet / serialport / RDP / VNC / Spice / ftp client for Linux, Mac, Windows, Android, HarmonyOS and iOS.

It also supports older / special systems: Ubuntu 18, Windows 7, macOS 10+, UOS / Kylin / LoongArch (old-world and new-world), riscv64 and ppc64le Linux.

Highlights (full docs in the [main repo](https://github.com/electerm/electerm)):

- Terminal + file manager, ssh/sftp/ftp/telnet/serialport/RDP/VNC/Spice
- Global hotkey to toggle window (`ctrl + 2` by default)
- Multi-language support ([electerm-locales](https://github.com/electerm/electerm-locales))
- Public key / password / ssh-agent / certificate / OTP auth, tunnels, jump hosts
- Zmodem (`rz`/`sz`) and [trzsz](https://github.com/trzsz/trzsz) (`trz`/`tsz`)
- Themes, transparency, background image
- Global / session proxy, quick commands, sync via gist / webdav / electerm cloud
- AI assistant, MCP widget, deep links (`ssh://`, `telnet://`), CLI usage

Related links / projects:

- [Homepage / downloads](https://electerm.org) · [Releases](https://github.com/electerm/electerm/releases) · [Videos](https://electerm.org/videos)
- [Theme](https://theme.electerm.org) · [Docker](https://github.com/electerm/electerm-web-docker) · [Online demo](https://demo.electerm.org)
- [Android](https://github.com/electerm/electerm-android) · [HarmonyOS](https://github.com/electerm/electerm-harmony) · [Web version](https://github.com/electerm/electerm-web)
- [electerm online](https://cloud.electerm.org) · [electerm AI](https://ai.electerm.org)
- [Apple App Store](https://apps.apple.com/cn/app/electerm/id6792971552) · [Huawei AppGallery](https://appgallery.huawei.com/app/detail?id=org.electerm.electerm) · [Microsoft Store](https://www.microsoft.com/store/apps/9NCN7272GTFF) · [Snap Store](https://snapcraft.io/electerm)
- [deb repo](https://repos.electerm.org/deb) · [rpm repo](https://repos.electerm.org/rpm)

## Issues / support

- Installer problems (`npm i -g electerm` fails, wrong binary, launch failure): open an issue in [electerm-npm](https://github.com/electerm/electerm-npm/issues).
- App bugs / feature requests: open an issue in [electerm/electerm](https://github.com/electerm/electerm/issues) or start a [discussion](https://github.com/electerm/electerm/discussions).
- Language files: [electerm-locales](https://github.com/electerm/electerm-locales).

## Contributing

This repo only holds the installer (`npm/`) and the publish workflow. See [CONTRIBUTING.md](https://github.com/electerm/electerm-npm/blob/master/CONTRIBUTING.md) for layout, tests (`npm test`, `npm run test-network`, `npm run pack-check`) and the release-driven publishing flow.

## Change log

See [electerm releases](https://github.com/electerm/electerm/releases) — the npm version tracks it 1:1.

## License

MIT
