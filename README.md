# NEXUS VM Studio

**Windows-based virtual machine testing and diagnostics for NEXUS OS**  
**Project:** NEXUS Emerging Technology  
**Application:** NEXUS VM Studio v9 · W1.1R3 / VMBOOT1 integration  
**Status:** Experimental development and validation tooling

NEXUS VM Studio is a local **HTML + PowerShell control interface for the open-source QEMU emulator**. It lets developers boot NEXUS OS images under emulated x86 hardware, inspect startup diagnostics, test virtual networking, and preserve guest changes in copy-on-write disk overlays. The NEXUS interface and automation scripts are project work; **QEMU supplies the emulator** and **TianoCore EDK II/OVMF supplies UEFI firmware**.

> [!IMPORTANT]
> This is **not a new or independent hypervisor** and is not a complete substitute for physical-hardware testing. This repository contains the VM launcher and supporting scripts, **not NEXUS OS boot images, QEMU binaries or a qualified physical-hardware test report**.

## Contents

- [Features](#features)
- [System requirements](#system-requirements)
- [Quick start](#quick-start)
- [How it works](#how-it-works)
- [Hardware profiles and networking](#hardware-profiles-and-networking)
- [Image handling and safety](#image-handling-and-safety)
- [Troubleshooting](#troubleshooting)
- [Validation and limitations](#validation-and-limitations)
- [Repository files](#repository-files)
- [Third-party acknowledgements](#third-party-acknowledgements)
- [License and redistribution](#license-and-redistribution)

## Features

| Capability | Implementation |
| --- | --- |
| Browser control surface | Locally served HTML, CSS and JavaScript, connected to a Windows PowerShell bridge |
| VM execution | `qemu-system-x86_64.exe`, configured and launched by PowerShell |
| Boot paths | Local `.img`, `.raw`, `.bin` and `.iso` images; raw images use USB-storage emulation; ISOs use a virtual optical drive |
| Firmware | TianoCore EDK II / OVMF UEFI firmware located in the installed QEMU distribution |
| Acceleration | Windows Hypervisor Platform (WHPX) when available; QEMU Tiny Code Generator (TCG) alternative |
| Guest storage | QCOW2 copy-on-write overlays; optional ephemeral session mode |
| Guest networking | Emulated Intel 82540EM e1000 (`8086:100E`) with QEMU user-mode NAT |
| Diagnostics | Preflight checks, bridge health checks, serial output, host logs, QEMU output and errors |
| USB imaging | Administrator-only raw USB capture using `qemu-img` with explicit disk selection |
| Recovery controls | Reset virtual overlays, reinitialize image-scoped UEFI variable state, restart bridge |
| Alternative UI | A native Windows Forms launcher is retained as a legacy option |

The browser is only the control panel: **it does not perform virtualization by itself**. PowerShell invokes QEMU as a separate host process.

## System requirements

- **Host:** Windows with Windows PowerShell (`powershell.exe`), Windows Forms support and a modern browser.
- **Emulator:** QEMU for Windows with `qemu-system-x86_64.exe`, `qemu-img.exe` and compatible OVMF/EDK II firmware files.
- **Optional installer:** WinGet (`winget.exe`) to install QEMU through `Setup-NEXUS-VM.ps1`.
- **Optional acceleration:** Windows Hypervisor Platform; TCG provides a software-emulation alternative.
- **Guest:** A separately supplied compatible NEXUS OS `.img`, `.raw`, `.bin` or `.iso` image.
- **Memory:** Defaults are **4 GiB guest RAM** and **4 virtual CPUs**; adjust to available host resources.

QEMU installation, firmware availability and hypervisor features vary by Windows installation. The scripts check the actual environment before VM startup.

## Quick start

1. Download or clone the repository and place its files in one **writable local directory**.
2. Install or locate QEMU for Windows. If required, launch **Setup / Check QEMU** from the application, or run the setup script in PowerShell.
3. Double-click **`NEXUS-VM-Lab.cmd`**. **Do not open `NEXUS-VM-Lab.html` directly.**
4. Wait for the launcher to start the loopback bridge and open the browser. Check for **Bridge Online**.
5. Select a NEXUS OS image using **SELECT IMAGE** or paste its full Windows path.
6. Begin with the **Compat** profile, **4096 MB RAM**, **4 vCPUs**, and **Virtual network** enabled.
7. Choose **START NEXUS VM**. QEMU opens the guest display in its own window.
8. Review the VM display, serial messages and host logs; stop the VM through the interface before resetting overlays.

### Alternative native interface

Run `OPEN-LEGACY-POWERSHELL-GUI.cmd` to use the legacy Windows Forms controls rather than the HTML bridge.

### Optional command-line setup

```powershell
# From the repository folder on Windows:
.\Setup-NEXUS-VM.ps1
```

The setup script looks for QEMU and, if absent, can invoke `winget install --id SoftwareFreedomConservancy.QEMU --exact`. Review installation prompts and execute only scripts you trust. The `.cmd` launcher invokes Windows PowerShell with `-ExecutionPolicy Bypass` for that process.

## How it works

```text
NEXUS-VM-Lab.cmd
       |
       v
NEXUS-VM-Launcher.ps1
       |  starts bridge / checks loopback health
       v
NEXUS-VM-Bridge.ps1 (127.0.0.1, token-gated API)
       |                         ^
       v                         |
NEXUS-VM-Lab.html (browser control panel)
       |
       v
Start-NEXUS-VM.ps1
       |  checks QEMU + firmware, prepares overlay/UEFI state
       v
QEMU x86_64 + OVMF
       |
       v
NEXUS OS guest image
```

The bridge creates a **local session token** and exposes control requests on loopback only. The launcher checks `/api/ping` before opening the UI and chooses a free port in its configured range. Tokens, logs, and generated VM disk/firmware files are runtime data: **do not commit them to GitHub**.

## Hardware profiles and networking

| Setting | Intended behavior |
| --- | --- |
| Compat (recommended) | NEXUS-compatible QEMU `pc`/i440fx topology and USB xHCI |
| Legacy | `pc`/i440fx topology with legacy USB controller |
| Modern | Uses the retained `pc`/i440fx machine for W1.1R3 guest compatibility; xHCI enabled |
| CPU | `qemu64,hypervisor=on,svm=off,vmx=off` |
| Network | Intel 82540EM `e1000` at PCI `00:03.0`, QEMU user NAT |
| Firmware state | OVMF variable-store copy keyed by selected boot image SHA-256 |

The NEXUS guest sees **virtual Ethernet**, not the physical Wi-Fi device or nearby SSIDs. QEMU NAT uses the host's existing Internet connection. The guest-side support for virtual networking is a separate matter from actual MediaTek or AMD device drivers.

## Image handling and safety

- **Raw USB/IMG input:** QEMU creates a writable **QCOW2 backing overlay** so ordinary guest writes do not directly modify the original image file.
- **ISO input:** QEMU uses virtual CD-ROM media and creates a separate virtual lab disk as needed.
- **Reset overlay:** Intentionally deletes generated guest overlay state and image-specific UEFI variables; changes stored in that overlay will be lost.
- **USB capture:** `Capture-NEXUS-USB.ps1` reads from a selected Windows physical USB disk to make an image, using `qemu-img`. Administrator permission is required; choose the disk number carefully.
- **Logs:** VM startup, error, serial and bridge logs can contain local paths, hardware details, session information or guest data.

> [!CAUTION]
> Back up anything important before capture, cleanup or reset work. Do not assume live-USB imaging produces a forensically consistent snapshot. Keep VM testing separate from destructive removable-media imaging operations.

### Local runtime data

The launcher and VM scripts automatically create `logs/`, `state/` and `images/` as needed. `.gitignore` excludes these directories by default. The source-only repository does **not** include the original archive's historical logs, session token, QCOW2 overlays, or OVMF variable-store copies.

## Troubleshooting

| Symptom | What to check |
| --- | --- |
| **NATIVE BRIDGE OFFLINE** | Close the tab and run `NEXUS-VM-Lab.cmd`; inspect launcher errors, not just the HTML |
| **Select image does nothing** | Verify the bridge status; retry the separate STA file-dialog helper or paste an absolute image path |
| **QEMU not found** | Run `Setup-NEXUS-VM.ps1`; confirm `qemu-system-x86_64.exe` and `qemu-img.exe` are installed |
| **UEFI firmware missing** | Verify QEMU installation includes OVMF/EDK II `.fd` code and variables templates |
| **WHPX unavailable or fails** | Enable Windows Hypervisor Platform when supported, or select **Force TCG** |
| **UEFI shell appears** | Confirm the guest's UEFI bootloader and image layout; the included `STARTUP.NSH` documents the project-specific shell fallback |
| **No guest Wi-Fi networks** | Expected: the VM exposes QEMU `e1000` Ethernet, not the host Wi-Fi adapter |
| **Networking unavailable** | Check guest e1000 driver, QEMU NAT setup and VM serial logs |
| **Lost guest changes after reset** | Resetting the QCOW2 overlay discards changes; restore from a saved guest backup |

For additional engineering context see [`BRIDGE_FIX_NOTES.md`](BRIDGE_FIX_NOTES.md) and [`VM_TEST_MATRIX.md`](VM_TEST_MATRIX.md).

## Validation and limitations

The supplied package includes historical launcher, QEMU, serial and bridge logs plus a NEXUS VM test matrix. These are **development evidence**, not proof of a repeatable current pass on every Windows/QEMU configuration. Historical VM behavior may depend on the exact guest image, firmware, host configuration and version.

**Useful VM targets:** UEFI boot path, kernel startup, exception handling, desktop lifecycle, basic file operations, browser/application UI, virtual PCI devices, virtual e1000 network and serial diagnostics.

**Physical testing still required:** Radeon RX 7900 XT GPU acceleration, MediaTek MT7922 Wi-Fi, physical SATA/NVMe and USB hardware, real peripherals, motherboard multi-display routing and actual Secure Boot enrollment.

This README was prepared by inspecting the provided archive and scripts. **The new documentation does not constitute a successful Windows hardware or guest boot test.**

## Repository files

| File | Purpose |
| --- | --- |
| `NEXUS-VM-Lab.cmd` | Recommended startup entry point |
| `NEXUS-VM-Launcher.ps1` | Starts and health-checks the native bridge |
| `NEXUS-VM-Bridge.ps1` | Loopback control API and host operations |
| `NEXUS-VM-Lab.html` | Browser user interface |
| `Start-NEXUS-VM.ps1` | VM configuration, QEMU invocation and guest image/overlay handling |
| `Preflight-NEXUS-VM.ps1` | Host, firmware and device preflight diagnostics |
| `Setup-NEXUS-VM.ps1` | QEMU discovery and optional WinGet installation |
| `Select-NEXUS-Image.ps1` | Native STA file-selection dialog |
| `Capture-NEXUS-USB.ps1` | Read-only capture of a physical USB disk into a raw image |
| `Reset-NEXUS-VM.ps1` | VM overlay/state reset helper |
| `DEBUG-BRIDGE.cmd` | Bridge troubleshooting mode |
| `NEXUS-VM-Lab.ps1` | Legacy Windows Forms interface |
| `OPEN-LEGACY-POWERSHELL-GUI.cmd` | Shortcut for the legacy UI |
| `RUN-DIAGNOSTICS.cmd` | Diagnostics shortcut |
| `STARTUP.NSH` | UEFI Shell fallback boot script for compatible guest images |
| `BRIDGE_FIX_NOTES.md` | Bridge development and troubleshooting notes |
| `VM_TEST_MATRIX.md` | Testing scope and physical-vs-virtual limitations |
| `VMBOOT1_VERSION.txt` | Guest-specific version/configuration notes |
| `THIRD_PARTY_NOTICES.md` | External software acknowledgements and license links |
| `.gitignore` | Prevents publication of generated logs, state, images and tokens |

## Third-party acknowledgements

NEXUS VM Studio is built to interoperate with important open-source infrastructure:

- **[QEMU](https://www.qemu.org/)** — x86 machine emulation, virtual devices, NAT networking, QCOW2 and the TCG accelerator; credits to Fabrice Bellard and QEMU contributors.
- **[TianoCore EDK II / OVMF](https://github.com/tianocore/edk2)** — open-source UEFI firmware used by QEMU; credits to TianoCore contributors and relevant upstream copyright holders.
- **[Microsoft WinGet](https://github.com/microsoft/winget-cli)** — optional open-source Windows package manager used to locate/install QEMU.

Windows PowerShell, Windows Forms, and Windows Hypervisor Platform are **Microsoft platform components**, not presented as redistributed open-source code. The interface uses built-in browser HTML, CSS and JavaScript; the inspected source does not declare bundled third-party JS libraries.

See **[`THIRD_PARTY_NOTICES.md`](THIRD_PARTY_NOTICES.md)** for project-level attribution, license references, what is and is not distributed, and provenance limitations. Acknowledgement is **not** a claim that NEXUS authored QEMU, OVMF or WinGet, or that those projects endorse NEXUS.

## License and redistribution

**No NEXUS project license was supplied with the source archive.** This documentation therefore does **not** impose or assume a license for the NEXUS-owned PowerShell, HTML, and associated project files. The separate upstream projects retain their own licenses. If this repository is to accept outside contributions or authorize redistribution, add a clearly scoped project `LICENSE` chosen by the rights holder and retain relevant third-party notices.

The source-only repository **does not bundle QEMU executables or firmware images**. Distributing those binaries would trigger their independent upstream notice and licensing obligations. See `THIRD_PARTY_NOTICES.md`.

---

*Prepared for the NEXUS Emerging Technology GitHub repository from the supplied NEXUS VM Studio v9 archive. Updated October 2026.*
