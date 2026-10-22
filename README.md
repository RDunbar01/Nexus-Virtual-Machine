# Nexus VM Studio

## Current release licensing

This Nexus release is not offered under Apache License 2.0. Nexus-owned software may be used only for personal, non-commercial purposes. Any other use, including commercial use, modification or redistribution, requires express written consent from the rights holder. Third-party code, models, datasets and separately licensed components retain their own licenses and notices. Previously granted rights and rights that cannot lawfully be restricted remain unchanged.


> Packaging refresh pending: existing staged data will be replaced by the newly reviewed licensing-corrected package before merge. Draft release staging: the v9-W1.1R4 installer and data components are included below. Windows/hardware acceptance and merge review remain pending; this is not a published release.

**Next-release documentation staging · not a published application update**

A local HTML and PowerShell control interface for QEMU, not a new hypervisor.

## Current baseline and release status

Verified baseline: **v9 · W1.1R3 / VMBOOT1**. Next identifier: **v9 · W1.1R4 / VMBOOT1**. Do not tag or label a binary with a new version until its application, installer and package metadata agree.

QEMU provides emulation and TianoCore EDK II/OVMF provides UEFI firmware. Guest OS images, QEMU and firmware are not bundled. Safe regression and PowerShell parsing checks do not establish successful Windows guest boot, WHPX acceleration, UAC, Forms or USB operation.

## Start and inspect the original project

Use the exact launch instructions accompanying the matching release package. The [preserved previous README](docs/README_PREVIOUS.md) contains the original workflow, research citations, credits, downloads and limitations. Treat its release-specific statements as historical; this staged update does not silently replace its software or relicense dependencies.

## Evidence and licensing

Developed by Rich Dunbar / Nexus Emerging Technology with AI-assisted coding. Retain existing licenses, attribution and third-party notices. No additional Windows, physical GPU, device-write or model-quality qualification is claimed by this documentation refresh. No live browser-trial deployment is created by this README.

## Staged v9-W1.1R4 installation files

[Installation instructions](releases/v9-W1.1R4/INSTALL.txt) · [Python installer](releases/v9-W1.1R4/installer.py) · [Package directory](releases/v9-W1.1R4) · [Validation record](releases/v9-W1.1R4/RELEASE_VALIDATION.json).

Download the entire version directory together, not the installer alone. Keep its manifest and data file alongside installer.py. Every distributed component is below 20,000,000 bytes. No new live release or hardware certification is implied.
