# Third-Party Software Notices and Acknowledgements

**Project:** NEXUS VM Studio (NEXUS Emerging Technology)  
**Scope:** Source and runtime dependencies identified by inspecting the supplied *Nexus Virtual Machine.zip* package (October 2026).

This file distinguishes **third-party tools that NEXUS calls**, **third-party firmware used by QEMU**, **operating-system components**, and **third-party code actually embedded in the project**. No unverified code authorship claims are made.

## Open-source infrastructure

| Project / contributors | Role in NEXUS VM Studio | License and upstream source | Redistribution status in this clean GitHub package |
| --- | --- | --- | --- |
| **QEMU — Fabrice Bellard and QEMU contributors** | Host emulator (`qemu-system-x86_64.exe`); device emulation including e1000; user-mode NAT; QCOW2 and `qemu-img`; WHPX/TCG execution | QEMU emulator as a whole: **GNU GPL version 2**; source files may have additional compatible licenses. [Official license](https://github.com/qemu/qemu/blob/master/LICENSE) · [Project](https://www.qemu.org/) | **Not bundled** — found/invoked from the installed Windows QEMU package |
| **TianoCore EDK II / OVMF contributors** | Virtual UEFI code and variable template firmware (`edk2-*` / `OVMF_*` `.fd` firmware) used by QEMU | Primarily **BSD-2-Clause-Patent**, with additional license notices for particular components. [EDK II license](https://github.com/tianocore/edk2/blob/master/License.txt) · [OVMF package notices](https://github.com/tianocore/edk2/blob/master/OvmfPkg/License.txt) · [Repository](https://github.com/tianocore/edk2) | **Not bundled** in this clean package; generated variable-store copies from the supplied archive are excluded |
| **WinGet — Microsoft and contributors** | Optional installation of QEMU using `winget install` | **MIT License**. [Official license](https://github.com/microsoft/winget-cli/blob/master/LICENSE) · [Repository](https://github.com/microsoft/winget-cli) | **Not bundled** — invoked when available on Windows |

### QEMU license boundary

The project calls a separately installed `qemu-system-x86_64.exe` / `qemu-img.exe`. It does **not** contain QEMU emulator source code in its inspected PowerShell/HTML files. This dependency does not by itself prove that the NEXUS launcher code is a derivative of QEMU. If any QEMU source code is later copied, modified or redistributed, review the actual applicable file licenses and GNU GPL obligations before release.

### OVMF firmware and UEFI variable stores

The original ZIP contains `.fd` files under `state/` that appear to be generated **image-scoped OVMF variable-store copies**, created by `Start-NEXUS-VM.ps1` from firmware templates found in QEMU's installation. These copies were **excluded from the clean GitHub source package**, because they are generated runtime state and may contain guest-specific variables. If OVMF firmware or prebuilt `.fd` images are distributed later, include required upstream copyright/license notices for the exact binaries and components.

## Windows platform interfaces (not open-source source attribution)

NEXUS uses the following Windows capabilities but does not distribute their implementations:

- **Windows PowerShell 5.x (`powershell.exe`)** — executes `.ps1` launcher/bridge scripts.
- **Windows Forms / System.Drawing** — used by the file dialog and legacy native GUI.
- **Windows Hypervisor Platform (WHPX)** — optional acceleration accessed through QEMU.
- **Windows storage/physical-disk interfaces** — read-only USB image capture workflow.
- **Standard browser APIs** — HTML, CSS, JavaScript and the `fetch` interface for the local control panel.

These are platform interfaces or runtime tools; the source archive is not evidence that their proprietary or separately licensed implementation code was copied into NEXUS. **Windows PowerShell `powershell.exe` should not be confused with the separate open-source PowerShell 7 / `pwsh` distribution.**

## Attribution and embedded-source audit

The inspected project code comprises PowerShell scripts, Windows CMD launchers, one standalone HTML/CSS/JavaScript interface, a UEFI Shell startup script, and documentation. A text search for common external script imports, library references and copied license headers found **no identified bundled third-party JavaScript library, QEMU source tree or imported open-source PowerShell module** in those application source files.

This is **not** a provenance certification. It cannot determine from text alone whether a developer previously consulted tutorials, adapted snippets, used AI-assisted drafting, or copied material whose notices were lost. If the development history identifies any additional upstream source, add:

1. Repository URL, author(s), and exact source file or commit.
2. Which NEXUS file incorporates or adapts that work.
3. The original copyright statement and applicable license.
4. Any modifications and whether redistribution conditions apply.

Do not credit unrelated projects (e.g. VirtualBox, VMware, Rufus or balenaEtcher) as code sources unless source evidence supports it.

## Licensing scope

The original ZIP has **no NEXUS-owned project-level `LICENSE`** file. No license has been invented in this package. The upstream license links above apply to their respective third-party projects **only**; they do not automatically relicense NEXUS scripts or HTML.

The cleaned source package excludes executable QEMU distributions, OVMF firmware binaries, guest OS images and runtime data, so third-party binary license files are not packaged here. If a future release bundles these files, review redistribution obligations for the exact distribution and retain the full upstream license and required copyright notices, rather than relying solely on this summary.

### Source acknowledgements

- QEMU: https://github.com/qemu/qemu and https://www.qemu.org/
- TianoCore EDK II / OVMF: https://github.com/tianocore/edk2
- WinGet: https://github.com/microsoft/winget-cli

*Attribution based on the files supplied for review on 8 October 2026. This document may be updated when additional original-source provenance becomes available.*
