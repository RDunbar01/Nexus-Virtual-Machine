# Nexus VM Studio

## Start locally: Python and application requirements

Download the full [v9-W1.1R4 package](releases/v9-W1.1R4/) for VM execution. The runtime is **Windows PowerShell plus QEMU, compatible UEFI firmware and a separately supplied guest image**, not Python or PyTorch. The HTML interface alone does not run a VM, and no approved standalone hosted VM trial is supplied.

Use **CPython 3.12** only for the optional supplied Python installer. That installer has no explicit Python-version guard; 3.12 is a recommendation, not a tested universal compatibility range. Python is not the VM runtime. QEMU, firmware and guest images are not bundled. Native Windows startup, guest boot, WHPX acceleration, UAC and USB behavior remain separate qualification steps.

## Current release licensing

This Nexus release is not offered under Apache License 2.0. Nexus-owned software may be used only for personal, non-commercial purposes. Any other use, including commercial use, modification or redistribution, requires express written consent from the rights holder. Third-party code, models, datasets and separately licensed components retain their own licenses and notices. Previously granted rights and rights that cannot lawfully be restricted remain unchanged.


> The reviewed v9-W1.1R4 source, installer and data components are published on the main branch. Windows/hardware acceptance remains pending; publication is not hardware certification.

**v9-W1.1R4 · published source and installer components · experimental software**

A local HTML and PowerShell control interface for QEMU, not a new hypervisor.

## Current baseline and release status

Previous baseline: **v9 · W1.1R3 / VMBOOT1**. Published package: **v9 · W1.1R4 / VMBOOT1**. Use the matching application, installer and package metadata together.

QEMU provides emulation and TianoCore EDK II/OVMF provides UEFI firmware. Guest OS images, QEMU and firmware are not bundled. Safe regression and PowerShell parsing checks do not establish successful Windows guest boot, WHPX acceleration, UAC, Forms or USB operation.

## Start and inspect the original project

Use the exact launch instructions accompanying the matching release package. The [preserved previous README](docs/README_PREVIOUS.md) contains the original workflow, research citations, credits, downloads and limitations. Treat its release-specific statements as historical; historical software and its original licenses remain preserved.

## Evidence and licensing

Developed by Rich Dunbar / Nexus Emerging Technology with AI-assisted coding. Retain existing licenses, attribution and third-party notices. No additional Windows, physical GPU, device-write or model-quality qualification is claimed by this documentation refresh. No live browser-trial deployment is created by this README.

## v9-W1.1R4 installation files

[Installation instructions](releases/v9-W1.1R4/INSTALL.txt) · [Python installer](releases/v9-W1.1R4/installer.py) · [Package directory](releases/v9-W1.1R4) · [Validation record](releases/v9-W1.1R4/RELEASE_VALIDATION.json).

Download the entire version directory together, not the installer alone. Keep its manifest and data file alongside installer.py. Every distributed component is at most 10,000,000 bytes. The complete licensing-corrected package has passed independent package review and isolated no-launch extraction; Windows/hardware acceptance remains pending. Publication of these components does not imply hardware certification.
