# Nexus VM Studio

A Windows HTML and PowerShell control interface for testing Nexus OS with QEMU. Version **v9-W1.1R4 / VMBOOT1** brings guest launch, storage overlays and diagnostics into one local development workflow.

[Download v9-W1.1R4](releases/v9-W1.1R4/) · [Installation instructions](releases/v9-W1.1R4/INSTALL.txt) · [Validation record](releases/v9-W1.1R4/RELEASE_VALIDATION.json)

## What it does

- Launch compatible `.img`, `.raw`, `.bin` and `.iso` guest images through QEMU and UEFI firmware.
- Keep ordinary guest writes in QCOW2 overlays rather than writing directly to the original raw image.
- Configure guest memory, virtual CPUs, WHPX or TCG acceleration, and QEMU user-mode NAT with virtual e1000 Ethernet.
- Show preflight checks, bridge health, serial output and host/QEMU logs alongside the guest controls.
- Archive generated overlays during reset and preserve image-scoped firmware state separately from the source image.

## Engineering contribution

Developed by **Rich Dunbar / Nexus Emerging Technology with AI-assisted coding** to shorten the Nexus OS kernel and GUI debugging cycle between physical boots. The Nexus work is the interface, token-gated loopback bridge, QEMU configuration, image-state handling and diagnostic automation.

**QEMU provides emulation; TianoCore EDK II/OVMF provides UEFI firmware.** Nexus VM Studio is not a new hypervisor. Virtual Ethernet is not the host's physical Wi-Fi device, and virtual-machine tests do not establish Radeon, NVMe, USB or other physical-device compatibility.

## Start locally

1. Download every file in [releases/v9-W1.1R4](releases/v9-W1.1R4/). Keep `Nexus_vm_Data_001.nxd`, `package_manifest.json` and `installer.py` together.
2. The optional installer uses **CPython 3.12**. Run `python installer.py --no-launch`, review the local installation plan and extract into a new directory. Python 3.12 is recommended, not a qualified universal version range.
3. On Windows, install or locate QEMU with `qemu-system-x86_64.exe`, `qemu-img.exe` and compatible OVMF/EDK II firmware. Supply your own compatible guest image. **QEMU, firmware and guest images are not bundled.**
4. Run **`NEXUS-VM-Lab.cmd`**, wait for **Bridge Online**, then select the guest image. Start with the Compat profile and settings appropriate to your host. QEMU opens the guest display in its own window.

Do not open `NEXUS-VM-Lab.html` directly to run a VM. It is a control panel for the local PowerShell bridge, not a standalone browser emulator. There is no hosted VM trial. Neither Python nor PyTorch is the VM runtime.

The launcher uses Windows PowerShell with process-scoped `-ExecutionPolicy Bypass`; review scripts before running them. QEMU setup, administrator-only USB capture and hypervisor configuration are separate host actions. Installer data parts are at most 10,000,000 bytes; individual distributed files remain below 20,000,000 bytes.

## Validation and safety

Safe regression tests and PowerShell parsing cover controller logic, request handling, browser state and temporary-file preservation. They do not establish native Windows startup, guest boot, WHPX, UAC, Windows Forms or physical USB behavior.

Back up existing `state/`, `images/` and `logs/` before updating. Stop the owned VM before resetting state. Reset archives generated overlays under `state/reset-backups/`; older overlays are not automatically migrated to new image keys. Logs and state may contain paths, guest data and session tokens, so keep them private.

See the extracted test records and [historical README](docs/README_PREVIOUS.md) for detailed workflow context. Historical reset behavior does not override the current archive-before-reset implementation.

## Credits and license

Credit Fabrice Bellard and QEMU contributors, TianoCore contributors, and Microsoft WinGet contributors. PowerShell, Windows Forms and WHPX are Microsoft platform components. See the [third-party notices](NEXUS_VM_Studio_THIRD_PARTY_NOTICES.md).

Personal use and internal commercial testing and evaluation are permitted for Nexus-owned material. Distribution, modification, production deployment and other commercial use require express written consent. Third-party licenses and previously granted rights remain unchanged. See the [license](LICENSE) and [evaluation permission](COMMERCIAL_EVALUATION_PERMISSION.md).
