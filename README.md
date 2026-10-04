# Root Scanner

[![Platform](https://img.shields.io/badge/Platform-Windows%20x64-0078D6?style=flat-square&logo=windows)](https://microsoft.com/windows)
[![Target](https://img.shields.io/badge/Target-Minecraft%20Java%20Edition-5B8C32?style=flat-square)](https://minecraft.net)
[![Architecture](https://img.shields.io/badge/Architecture-x86__64-orange?style=flat-square)](#system-requirements)
[![Privileges](https://img.shields.io/badge/Privileges-User%20%2F%20Admin-lightgrey?style=flat-square)](#execution)

**A Minecraft Java screenshare scanner for Windows.**

Root helps players and reviewers check a running Minecraft instance for cheat-related findings and unexpected modifications. Open the scanner, let it finish, and review the results in a local HTML report.

## Getting started

1. Start Minecraft Java Edition and leave it running.
2. Open **Root.exe** from the `release` folder.
3. Root automatically finds Minecraft and starts scanning. If multiple instances are open, choose the one you want to check.
4. Follow the loading bar and status message while the scan runs.
5. When it finishes, select **View results**.

Use a 64-bit Windows PC with a running Minecraft Java instance. If a check cannot access something it needs, review the coverage notes; administrator permissions may be needed for some checks.

## What Root can flag

Root includes checks for named cheat clients, potentially injected components, suspicious game modifications, and signs of removed or disguised cheat-related files. Some results are general observations that need a closer look rather than a named-client result.

The current build includes checks associated with these clients:

| | | |
| --- | --- | --- |
| Meteor | Wurst | Vape |
| Drip Lite | Raven b+ | Slinky |
| Entropy | Dream | Bloom |
| Kura | Karma | Doomsday |
| Augustus | Novoline | LiquidBounce |
| Rise | Astolfo | Tenacity |
| Moon | Aristois | Future |
| RusherHack | Oringo | Whoami |
| Arctic | | |

This list describes checks included in the current build. It does not guarantee detection of every version or configuration. A finding needs review, and an empty report does not guarantee that a client is cheat-free.

## Reading your results

The report keeps categories on the left and findings on the right. Start with the overview, then click a finding to see what was recorded.

| Category | What it means |
| --- | --- |
| **Detections** | A detection rule matched. Review the recorded evidence before making a decision. |
| **Warnings** | Something needs attention but may have a legitimate explanation. |
| **Suspicious** | An unusual observation needs further review. |
| **System integrity** | Observations about system state that may matter to the review. |
| **Bypass indicators** | Observations that may affect a scan; they do not by themselves establish an intentional bypass. |

**Check notes** explain incomplete checks and provide additional context. Ordinary launcher connections or unrelated applications should not be treated as proof of cheating. Multiple occurrences of one finding are not multiple confirmed cheats.

### Report controls

- **Search** by finding name, source path, or evidence.
- **Expand visible findings** to read several entries at once.
- **Technical details** shows extra information when needed.
- **Open location** opens Windows File Explorer for a provided local file path. Accept the browser's **Open Root** prompt if it appears.
- **Copy file path** lets you open a location manually if the link is unavailable.

Reports are saved under `%TEMP%\RootScanner`. Keep the report if you need it later, since temporary files can be cleared. Run a new scan after updating Root to get the latest report features.

## Local scans and session PINs

A regular build runs locally and displays **LOCAL**. A personalized build can include a session PIN and submit results to its configured session service. Website delivery depends on that service being available; the local report remains available if an upload fails.

## If something looks wrong

Check the finding's explanation and source before taking action. Legitimate mods and applications can produce findings too.

When reporting a possible false positive or missed detection, include:

- The finding name and relevant evidence.
- Your Minecraft version, launcher, and mod loader.
- The mod or client version involved and what you expected.

Root's game checks are read-only. It does not remove mods, change game settings, or ban players. Reviewers make the final decision.

---

Created by **tosloi**.
