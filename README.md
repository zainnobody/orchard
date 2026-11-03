<p align="center">
  <img src="assets/banner.jpg" alt="Orchard: extra drivers, fixes and a Mac-like desktop for Linux on Apple silicon Macs" width="100%">
</p>

<p align="center">
  <a href="https://github.com/zainnobody/apple-silicon-linux-kit/blob/main/GUIDE.md"><b>Guide</b></a> ·
  <a href="#quick-start"><b>Quick start</b></a> ·
  <a href="https://github.com/zainnobody/apple-silicon-linux-kit/blob/main/docs/claude-code-setup.md"><b>Set up with Claude Code</b></a> ·
  <a href="https://github.com/zainnobody/apple-silicon-linux-kit/blob/main/docs/test-results-2026-11.md"><b>Test results</b></a>
</p>

<div align="center">

[![License: GPL-2.0](https://img.shields.io/badge/license-GPL--2.0-blue)](LICENSE) ![Tested on: M3 Pro J516S](https://img.shields.io/badge/tested%20on-M3%20Pro%20J516S-555) ![Community project](https://img.shields.io/badge/community-project-2f8f4e)

</div>

Orchard builds on Fedora Asahi Remix. It adds the native GPU driver stack, Touch ID, faster memory, hardware video decode, fan control and more. Each part comes with install scripts that you can undo, and with test results.


> **Community project.** Orchard builds on Asahi Linux but is not part of it, and the Asahi developers do not support it. Please report problems in these repositories, not to Asahi.

<table>
<tr>
<td><img src="screenshots/desktop.jpg" alt="KDE desktop with a top bar and a dock"></td>
<td><img src="screenshots/lock-screen.jpg" alt="Flat lock screen with a password field and a fingerprint hint"></td>
</tr>
<tr>
<td colspan="2" align="center"><img width="560" src="screenshots/konsole-gpu.jpg" alt="Terminal showing the OpenGL 4.6 and Vulkan 1.4 driver for the Apple M3 Pro GPU"></td>
</tr>
</table>

## How it fits together

```mermaid
flowchart TB
    mac(["Apple silicon Mac<br/><small>tested: MacBook Pro 16-inch M3 Pro</small>"]):::hw
    boot("Boot chain<br/><small>m1n1 and U-Boot from Asahi</small>"):::hw
    kernel("Linux kernel + kit patches<br/><small>GPU firmware pin, memory floor, fans,<br/>Bluetooth resume, AV1 fix, Touch ID</small>"):::core
    mesa("Native GPU driver<br/><small>Mesa: OpenGL 4.6, Vulkan 1.4</small>"):::core
    touch("Touch ID<br/><small>fprintd and PAM</small>"):::core
    kde("KDE Plasma<br/><small>Mac-like desktop and lock screen</small>"):::ui
    ai("Local AI<br/><small>llama.cpp on the GPU</small>"):::ai
    mac --> boot --> kernel
    kernel --> mesa & touch
    mesa --> kde
    touch --> kde
    mesa --> ai
    classDef hw fill:#e8f0fe,stroke:#4a72d9,stroke-width:1.5px,color:#14264d
    classDef core fill:#e6f4ea,stroke:#2f8f4e,stroke-width:1.5px,color:#0f3a1f
    classDef ui fill:#f3e8fd,stroke:#8e44c9,stroke-width:1.5px,color:#38174f
    classDef ai fill:#fdeee0,stroke:#d9822b,stroke-width:1.5px,color:#4a2706
    classDef todo fill:#f1f3f4,stroke:#9aa0a6,stroke-width:1.5px,stroke-dasharray:5 4,color:#3c4043
```

## The projects

| Repository | What it is | Start with |
|---|---|---|
| [apple-silicon-linux-kit](https://github.com/zainnobody/apple-silicon-linux-kit) | The main kit: the step-by-step guide, kernel and Mesa patches, install stages, Touch ID, tests | [GUIDE.md](https://github.com/zainnobody/apple-silicon-linux-kit/blob/main/GUIDE.md) |
| [apple-silicon-linux-desktop](https://github.com/zainnobody/apple-silicon-linux-desktop) | The `agx` installer for Fedora, Debian, Ubuntu and Arch, a kernel RPM build, and the Mac-like KDE desktop and lock screen | [README](https://github.com/zainnobody/apple-silicon-linux-desktop#readme) |
| [apple-silicon-neural-engine](https://github.com/zainnobody/apple-silicon-neural-engine) | Neural Engine research, and llama.cpp fixes for local AI on the GPU | [README](https://github.com/zainnobody/apple-silicon-neural-engine#readme) |

## What works

Tested on a MacBook Pro 16-inch M3 Pro (J516S) with Fedora Asahi Remix 44 and KDE Plasma.

| Area | Result |
|---|---|
| GPU | OpenGL 4.6, GLES 3.2 and Vulkan 1.4 on the native Mesa driver. KDE runs on the GPU. |
| Memory speed | About 130 GB/s instead of about 32 GB/s, with the memory floor patch |
| Touch ID | Unlocks the lock screen in about 1 s, with no key press, and works at the login screen. A wrong finger is refused. sudo stays password-only. |
| Video decode | H.264, HEVC, VP9 and AV1 in hardware |
| Displays and ports | HDMI, DisplayPort over USB-C, USB4 links |
| Fans | A kernel patch and a fan curve service. Without them the fans stay off. |
| Local AI | Llama 3.1 8B at 150 tokens/s prompt and 22.7 tokens/s generation on the GPU |
| Desktop | A top bar, a dock, flat icons, Meta+Space search, a flat lock screen |

Not done yet: sleep drains the battery, video encode uses the CPU, and the Neural Engine is research only. The full status table is in [the guide](https://github.com/zainnobody/apple-silicon-linux-kit/blob/main/GUIDE.md#3-status-of-each-feature).

## Get started

1. Read [Before you start](https://github.com/zainnobody/apple-silicon-linux-kit/blob/main/GUIDE.md#4-before-you-start) in the guide. Back up macOS, and rehearse the rollback.
2. Follow the [step-by-step guide](https://github.com/zainnobody/apple-silicon-linux-kit/blob/main/GUIDE.md#5-step-by-step). Each step has a verify check and an undo step.
3. Or let Claude Code do it with you: [connect a second Mac and paste the prompt](https://github.com/zainnobody/apple-silicon-linux-kit/blob/main/docs/claude-code-setup.md).
4. Check your install with the self-test: `tests/m3-selftest.sh` in the kit.

## Quick start

On the Mac that runs Fedora Asahi Remix, get the three projects side by side:

```bash
mkdir -p ~/apple-silicon-linux && cd ~/apple-silicon-linux
```

```bash
git clone https://github.com/zainnobody/apple-silicon-linux-kit.git agx-linux-kit
```

```bash
git clone https://github.com/zainnobody/apple-silicon-linux-desktop.git distro-pack
```

```bash
git clone https://github.com/zainnobody/apple-silicon-neural-engine.git ane-linux
```

See what your Mac supports and every install step, in order. These two commands change nothing:

```bash
distro-pack/agx detect
```

```bash
distro-pack/agx plan-install
```

Then follow [the guide](https://github.com/zainnobody/apple-silicon-linux-kit/blob/main/GUIDE.md#5-step-by-step). After each reboot and at the end, check the Mac:

```bash
agx-linux-kit/tests/m3-selftest.sh
```

## Let Claude Code help

Claude Code can run the guide with you from a second Mac. It runs the commands, checks each result and stops at every step that needs you. First [connect the second Mac over SSH](https://github.com/zainnobody/apple-silicon-linux-kit/blob/main/docs/claude-code-setup.md#option-a-connect-a-mac-to-the-m3). Then paste this prompt:

```text
Install Orchard (github.com/zainnobody/orchard) on my Apple silicon Mac that runs
Fedora Asahi Remix. It is reachable with `ssh m3`. Use the full prompt and rules in
docs/claude-code-setup.md of apple-silicon-linux-kit. Read GUIDE.md first and show me
a plan. Stop before every sudo command, every reboot and every Touch ID step.
Never ask for or type my password.
```

## Other Macs

Only the M3 Pro (J516S) is tested. Other Macs need a machine profile in the kit (`machines/`). Profiles are welcome with test results.

## Credits

Orchard is built on the work of the [Asahi Linux](https://asahilinux.org/) project and the community SiliconForest fork. Thanks to their developers.

## License

GPL-2.0 ([LICENSE](LICENSE)). Patches keep the license of the project they change. Each repository lists the details.
