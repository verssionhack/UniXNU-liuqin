# UniXNU-liuqin

macOS 27.3 (26C5035r) on the Xiaomi Pad 6 Pro (`liuqin`, SM8475 / Adreno 730.1), booted through the [UniXNU](https://github.com/verssionhack/UniXNU) UEFI path.

This repository holds only the liuqin-specific sources and build scripts. They overlay a UniXNU checkout (`ROOT` in the scripts points to it) and the external workspace under `/media/disk_4/unixnu_workspace/`. Firmware, Apple binaries and build outputs are not included.

**Last image confirmed to reach the desktop:** `boot.img` sha256 `b2572ef0b08ecedd9fd866b0831a90ffb753c93f19b3216b895e4d4753a01c1a` (capture `boot-gpu-20261125-204913`: lit framebuffer, WindowServer, loginwindow, Dock, Finder, Terminal, Wi-Fi scan 14 APs, Bluetooth active). Kernel layout gates pass: `__TEXT=0x1d0000`, `__DATA=0x280000`, `aes_gate=PASS`.

## Finished (verified on hardware)

| Area | State | Notes |
| --- | --- | --- |
| Display 2880x1800 landscape | Done | 120 Hz default, 144 / 60 Hz switching, DPU double buffering |
| Adreno 730 GPU / Metal | Done | GMU, ZAP, `AGXAccelerator` registered, DCVS, write-combining BOs |
| Desktop (WindowServer, Dock, Finder) | Done | Auto-login user `unixnu`, setup skipped |
| Persistent Data (UFS) | Done | APFS grown to ~199 GiB, reused across boots with `--persistent-data reuse` |
| Touchscreen (NT36532) | Done | SPI4 + DMA, absolute pointer and click |
| Backlight, buttons, Hall sensor | Done | |
| Wi-Fi (WCN6855) | Scan works | `ath11k` + `mac80211` + `cfg80211`, `en0`, `unixnu-wpa_supplicant`; scan finds 10-25 APs |
| Bluetooth (WCN6855) | Controller up | UART20 at 3.2 Mbps, `bluetoothd`, `BlueTool` |
| Idle CPU / heat after login | Mostly done | `no-lc=1`, short exec deny list, disabled-service lists |

## Not finished

| Area | State |
| --- | --- |
| Wi-Fi association and DHCP | Not tested on hardware (WPA2-PSK via `wpa_cli`) |
| Bluetooth pairing and data | Not tested with a peer device |
| Keyboard cover (I2C13) | I2C write times out, no interrupts |
| Touchpad on the cover | Unverified |
| Pen and multitouch gestures | Not implemented |
| CPU load during the first minute after login | Still high (`WindowServer`, `launchd`, `dasd`, `cloudd`, `mobileassetd`, `contextstored`, `searchpartyd`); a larger exec deny list is untested |
| Audio, camera, battery and charging UI, sleep/wake | Not started |

A larger exec deny list that redirected 40+ daemons to `/var/liuqin/parkd` made the desktop black (Dock and Finder never appeared) and is not used.


---

<p align="center">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="assets/logo-dark.svg">
    <source media="(prefers-color-scheme: light)" srcset="assets/logo-light.svg">
    <img alt="UniXNU" src="assets/logo-light.svg" width="200">
  </picture>
</p>

<p align="center">
  <a href="https://unixnu.com"><img alt="Documentation" src="https://img.shields.io/badge/docs-unixnu.com-2A5BD7?logo=readthedocs&logoColor=white"></a>
  <a href="https://t.me/unixnu"><img alt="Telegram" src="https://img.shields.io/badge/Telegram-@unixnu-26A5E4?logo=telegram&logoColor=white"></a>
  <a href="https://qun.qq.com/universal-share/share?ac=1&authKey=Pubz23DOCY76nxjlGuSms2zesk4jRvas4zrXJRYk7omf7CprXbGQigLEI%2BCoYR8D&busi_data=eyJncm91cENvZGUiOiIxMTI2MTE2NzU5IiwidG9rZW4iOiJwSHJsUDN1UnN2Vi8yM210TjJRbzBIcERmcUpyOS9lb0hHVVpadWdvVFdScVpSYUFLT1YzTk5TWC9mN2xWYmtIIiwidWluIjoiMzE1MTEwOTc0MSJ9&data=_cJgr-jX9yJ2-GXwVjRN5FOhDyk5B-HAfIk5LSunbW_XkkmKqNXbpH33wzP93J0p9DI1fbjgBUrUfTVhwDcxjg&svctype=4&tempid=h5_group_info"><img alt="QQ Group" src="https://img.shields.io/badge/QQ%20Group-join-12B7F5?logo=qq&logoColor=white"></a>
  <a href="https://x.com/UniXNU_new"><img alt="X" src="https://img.shields.io/badge/X-@UniXNU__new-000000?logo=x&logoColor=white"></a>
</p>

<p align="center">
  English | <a href="README.zh-CN.md">简体中文</a>
</p>

# UniXNU

> **Migration notice:** Update your device repository for shared SoC support and canonical component names. Follow the [migration guide](https://unixnu.com/porting/migration).

An independent project to run iOS and macOS on non-Apple ARM64 hardware through UEFI. Like OpenCore, UniXNU is a general UEFI environment and driver loading system: it loads the drivers a device needs and boots the OS through a common UEFI layer.

UniXNU provides shared build tools, ARM64 support, OS profiles and reusable components for ports across SoCs and devices. SoC repositories supply common platform support, and device repositories supply board configuration and hardware integration.

**🎉 News:** Adreno740 and AdrenoMetal implement GPU acceleration for A740/SM8550. See the [Adreno 740 driver documentation](https://unixnu.com/drivers/adreno740).

Documentation: [unixnu.com](https://unixnu.com). Start with [Port a device](https://unixnu.com/porting/) or [Build and boot](https://unixnu.com/building).

## Repositories

| Repository | Contents |
| --- | --- |
| [UniXNU](https://github.com/UniXNU/UniXNU) | Build tools, component catalog, ARM64 support, OS profiles, UEFI package, documentation |
| [UniXNU-sm8550-common](https://github.com/UniXNU/UniXNU-sm8550-common) | Shared SM8550 support |
| [UniXNU-sheng](https://github.com/UniXNU/UniXNU-sheng) | Xiaomi Pad 6S Pro |
| [uefi-sheng](https://github.com/UniXNU/uefi-sheng) | UEFI firmware for the Xiaomi Pad 6S Pro |

Clone with `--recurse-submodules`.

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md).

The macOS 27 profile follows [macOS-DroidVM](https://github.com/Droid-VM/macOS-DroidVM), the DroidVM port booted with UniXNU, for its kernel patches (the union root volume, code signature pages on a 4K kernel, the 24 MHz timebase, the realtime and mmap fixes, exec denial) and for the account setup in the Data seed.

## License

BSD-2-Clause-Patent. See [LICENSE](LICENSE).
