# Mobile Malware & Supply-Chain Audit: ReVanced Manager Plus & Prebuilt Patched APKs (vanced.to)

![Analysis](https://img.shields.io/badge/Analysis-Static%20Defensive%20REA-blue)
![Platform](https://img.shields.io/badge/Platform-Android%20APK-green)
![Safety](https://img.shields.io/badge/Safety-Isolated%20%2F%20No%20Execution-success)
![Audit Status](https://img.shields.io/badge/Audit%20Status-Complete-brightgreen)
![Language](https://img.shields.io/badge/Language-English%20(UK%2FIndia)-informational)

This repository contains a comprehensive, defensive mobile malware and supply-chain security audit of **ReVanced Manager Plus** and **five popular prebuilt patched Android applications** distributed by the third-party website `https://vanced.to`.

All investigations were conducted using the **Reverse-Engineer-Anything (REA)** defensive Android static analysis route, strictly isolating target binaries without device installation or host execution.

---

## Table of Contents

* [1. Executive Summary](#1-executive-summary)
* [2. Consolidated Verdict Matrix](#2-consolidated-verdict-matrix)
* [3. Target Inventory & Cryptographic Hashes](#3-target-inventory--cryptographic-hashes)
* [4. Key Forensic Findings](#4-key-forensic-findings)
  * [4.1 ReVanced Manager Plus: The Impersonation & Insecure Sideloading Anomaly](#41-revanced-manager-plus-the-impersonation--insecure-sideloading-anomaly)
  * [4.2 The Five Prebuilt Patched Applications: Authentic Community Code](#42-the-five-prebuilt-patched-applications-authentic-community-code)
* [5. Patch Provenance & Community Forks](#5-patch-provenance--community-forks)
* [6. Network Infrastructure & Endpoint Classification](#6-network-infrastructure--endpoint-classification)
* [7. Repository Structure & Documentation Index](#7-repository-structure--documentation-index)
* [8. Defensive Methodology & Boundaries](#8-defensive-methodology--boundaries)
* [9. Safe Recommendations & Official Alternatives](#9-safe-recommendations--official-alternatives)
* [10. Disclaimer & Research Context](#10-disclaimer--research-context)

---

## 1. Executive Summary

Third-party download portals frequently distribute pre-compiled modded Android applications, introducing significant supply-chain risks. This audit evaluated six artifacts obtained from `vanced.to` to determine:
1. Whether each application is an honest community build or contains trojans, surveillance spyware, keyloggers, or hidden payloads.
2. The exact open-source community patch sets, toolchains, and versions utilized.

### Key Conclusions

1. **Prebuilt Applications (`Clean-as-patched`)**:
   * **Morphe Google Photos**, **Morphe Reddit**, **Strava**, **YouTube ReVanced**, and **YouTube Music ReVanced** were verified to be **honest community builds**.
   * Bytecode additions across all five apps correspond strictly to known open-source patch projects: **official ReVanced** (`v6.3.0` and `v6.2.0`), **official Morphe** (`v2.2.0.5`), and **Rushii's universal patch bundle** (`p1.23.1`).
   * No spyware, keyloggers, screen recording hooks, data exfiltration webhooks, or second-stage executable droppers were identified.
   * All five applications share the standard open-source ReVanced CLI community debug keystore (`CN=ReVanced`).

2. **ReVanced Manager Plus (`Suspicious` / High Supply-Chain Risk)**:
   * **Not an official ReVanced application**. It uses package name `com.revanced.net.revancedmanager` and is signed by an entity identifying as `CN=revanced.net`.
   * **Contains zero patcher engine code**. It cannot decompile or patch APKs locally; instead, it functions strictly as a prebuilt APK downloader and sideload orchestrator querying `https://vanced.to/apps-v3.json`.
   * **Critical verification flaw**: In `DownloadWorker.java`, the app requests elevated install permissions (`REQUEST_INSTALL_PACKAGES` and `UPDATE_PACKAGES_WITHOUT_USER_ACTION`) but performs **zero cryptographic hash or certificate verification** on downloaded files, checking only HTTP content length. A server-side compromise of `vanced.to` would allow seamless distribution of malicious APKs to all users without triggering client-side warnings.

---

## 2. Consolidated Verdict Matrix

| Application | File Name | Version | Verdict | Risk Level | Primary Forensic Rationale | Evidence ID |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **ReVanced Manager Plus** | `vanced.to_revanced_manager_plus_v3.6.25.apk` | 3.6.25 | **Suspicious** | **High** | Deceptive branding; author signed by `CN=revanced.net`; contains zero patcher engine code; downloads remote APKs without hash or signature checks | [`EVD-MGR-VRD-001`](02-manager.md) |
| **Morphe Google Photos** | `vanced.to_rushi_com.google.android.apps.photos_...` | 7.98.0.1001971222 | **Clean-as-patched** | Low | Modifications fully explained by Rushii Morphe patch set (`p1.23.1`) for Pixel spoofing and DCIM isolation; standard ReVanced test signer | [`EVD-GPH-VRD-001`](apks-tested-report/gphotos.md) |
| **Morphe Reddit** | `vanced.to_morphe_com.reddit.frontpage_...` | 2026.40.0 | **Clean-as-patched** | Low | Modifications fully explained by official MorpheApp Reddit patch set (`p2.2.0.5`, commit `c90067a430`) for ad-blocking; standard ReVanced test signer | [`EVD-RED-VRD-001`](apks-tested-report/reddit.md) |
| **Strava** | `vanced.to_rushi_com.strava_...` | 480.15 | **Clean-as-patched** | Low | Isolated client-side entitlement patch in `classes9.dex` (`SubscriptionDetail`); no location tracking exfiltration; standard ReVanced test signer | [`EVD-STR-VRD-001`](apks-tested-report/strava.md) |
| **YouTube ReVanced** | `vanced.to_revanced_com.google.android.youtube_...` | 21.16.256 | **Clean-as-patched** | Low | Modifications fully explained by official ReVanced patches (`p6.3.0`, commit `cb9a113e85`); standard SponsorBlock/RYD endpoints; ReVanced test signer | [`EVD-YTB-VRD-001`](apks-tested-report/youtube.md) |
| **YouTube Music ReVanced** | `vanced.to_revanced_com.google.android.apps.youtube.music_...` | 9.15.51 | **Clean-as-patched** | Low | Modifications fully explained by official ReVanced patches (`p6.2.0`, commit `c6507b5051`); ad-blocking and audio-only playback; ReVanced test signer | [`EVD-YTM-VRD-001`](apks-tested-report/ytmusic.md) |

---

## 3. Target Inventory & Cryptographic Hashes

The complete ledger is available in machine-readable format at [`01-hashes.csv`](01-hashes.csv):

| Application | Version | Size (Bytes) | SHA-256 Digest | Signer SHA-256 Fingerprint |
| :--- | :--- | :--- | :--- | :--- |
| **ReVanced Manager Plus** | 3.6.25 | 2,477,424 | `f13df8fb954e096ec4f2d79718f8161f7b771b9094bc105600a7493837045a60` | `74a62bdf5dc3105af7ac189364e4925d84a669ca21abe3bc709d61a3f48e1379` |
| **Morphe Google Photos** | 7.98.0.1001971222 | 134,247,409 | `be9bb732f7c3f014299769a66b4f70730d08a2c310dd8f517695a92cbd4939f4` | `5d87c9bdc8e368dcf80971f33455db4c2e7dd754e1f288f1162c04b2a4811708` |
| **Morphe Reddit** | 2026.40.0 | 88,417,484 | `a4468cd7924e55970303df7deca23162b5d01a8160b0ebafd9597d83fc041112` | `5d87c9bdc8e368dcf80971f33455db4c2e7dd754e1f288f1162c04b2a4811708` |
| **Strava** | 480.15 | 122,829,417 | `60b3e22f226067d473adb032d83972750112ccdcb5b95bc0efe603b6650f14d8` | `5d87c9bdc8e368dcf80971f33455db4c2e7dd754e1f288f1162c04b2a4811708` |
| **YouTube ReVanced** | 21.16.256 | 195,914,241 | `e948a7ef5692c227ab7f8425cb71818e7f075f6d3bba61346fb68fc00afdfb7c` | `5d87c9bdc8e368dcf80971f33455db4c2e7dd754e1f288f1162c04b2a4811708` |
| **YouTube Music ReVanced** | 9.15.51 | 78,762,579 | `841156b72979b80ff05aafbcd9cb8269a04e5688f4c25cf500e3abbe902ee32c` | `5d87c9bdc8e368dcf80971f33455db4c2e7dd754e1f288f1162c04b2a4811708` |

---

## 4. Key Forensic Findings

### 4.1 ReVanced Manager Plus: The Impersonation & Insecure Sideloading Anomaly
* **Certificate Discrepancy**: While official ReVanced tools originate from the ReVanced team (`app.revanced.manager.flutter`), ReVanced Manager Plus (`com.revanced.net.revancedmanager`) is signed by `CN=revanced.net, OU=ReVanced, O=ReVanced, L=San Francisco, ST=California, C=US`.
* **Absence of Patcher Architecture**: Decompilation reveals zero patcher libraries (`revanced-patcher`, `smali`, `baksmali`, `dexlib2`). It is incapable of inspecting or patching APKs on the user's phone.
* **Insecure Downloader**: The app parses JSON catalogues (`https://vanced.to/apps-v3.json`) and downloads prebuilt APKs from `github.com/vancedto/vanced.to/releases/`.
* **Zero Integrity Verification**: Decompiled code in `DownloadWorker.java` confirms that the application checks only whether the downloaded file length matches the HTTP `content-length` header. It performs **no SHA-256 checksum comparison** and **no APK signing certificate validation**.

Detailed analysis: [`02-manager.md`](02-manager.md).

### 4.2 The Five Prebuilt Patched Applications: Authentic Community Code
* **Morphe Google Photos**: Injects `app.template.extension.extension.PixelSpoofHelper` and `PhotosDcimFolderPatch` in `classes11.dex`. It enables unlimited original-quality cloud backups by emulating Google Pixel device profiles. All 29 `.bin` files in `assets/` were verified to be stock Google Photos white-balance and VR viewport models. (Dossier: [`apks-tested-report/gphotos.md`](apks-tested-report/gphotos.md)).
* **Morphe Reddit**: Injects 591 classes under `app.morphe.extension.reddit.patches.*` across `classes14.dex`–`classes16.dex`. Implements feed ad-blocking, text copying, and custom typography. Version code is set to `2147483647` (`Integer.MAX_VALUE`) to inhibit automatic Play Store overwriting. (Dossier: [`apks-tested-report/reddit.md`](apks-tested-report/reddit.md)).
* **Strava**: Replaces subscription entitlement logic via a compact `classes9.dex` (13.8 KiB) containing an overridden `SubscriptionDetail` model that forces `isPremium() = true`. No location or GPS exfiltration routines were detected. (Dossier: [`apks-tested-report/strava.md`](apks-tested-report/strava.md)).
* **YouTube ReVanced**: Contains 1,903 patch classes (`app.revanced.extension.youtube.*`) matching official ReVanced patch set `v6.3.0` (commit `cb9a113e85`). Employs standard SponsorBlock, ReturnYouTubeDislike, and DeArrow community APIs. (Dossier: [`apks-tested-report/youtube.md`](apks-tested-report/youtube.md)).
* **YouTube Music ReVanced**: Contains 1,106 patch classes (`app.revanced.extension.music.*`) matching official ReVanced patch set `v6.2.0` (commit `c6507b5051`). Provides ad removal and background/audio-only streaming. (Dossier: [`apks-tested-report/ytmusic.md`](apks-tested-report/ytmusic.md)).

---

## 5. Patch Provenance & Community Forks

Complete provenance mapping and git commit tracking is available in [`vanced-audit/03-patches.md`](vanced-audit/03-patches.md):

* **Official ReVanced Patches** ([`github.com/ReVanced/revanced-patches`](https://github.com/ReVanced/revanced-patches)):
  * YouTube ReVanced: Patches `p6.3.0` (Commit: `cb9a113e85`), CLI `6.0.0`
  * YouTube Music ReVanced: Patches `p6.2.0` (Commit: `c6507b5051`), CLI `6.0.0`
* **Official Morphe Patches** ([`github.com/MorpheApp/morphe-patches`](https://github.com/MorpheApp/morphe-patches)):
  * Reddit Morphe: Patches `p2.2.0.5` (Commit: `c90067a430`), CLI `2.2.0.12`
* **Rushii Universal Patch Bundle** ([`github.com/RushiiMachine`](https://github.com/RushiiMachine)):
  * Google Photos Morphe: Patches `p1.23.1`, CLI `1.20.0.5`
  * Strava: Patches `p1.23.1.1`, CLI `1.20.0.5`

---

## 6. Network Infrastructure & Endpoint Classification

The complete ledger categorizing 435 unique extracted network domains is documented in [`vanced-audit/04-network.md`](vanced-audit/04-network.md):

* **High / Medium Supply-Chain Risk Endpoints**:
  * `vanced.to` / `https://vanced.to/apps-v3.json`: Dynamic catalogue API used by ReVanced Manager Plus.
  * `github.com/vancedto`: Distribution repository hosting pre-compiled APK binaries.
  * `https://vanced.to/donate-redir`: Commercial donation redirect.
* **Expected Community Patch Infrastructure**:
  * `https://sponsor.ajay.app` (SponsorBlock database)
  * `https://returnyoutubedislikeapi.com/` (Public dislike restore metrics)
  * `https://dearrow.ajay.app` (DeArrow title/thumbnail crowdsourced database)
  * `https://api.revanced.app` (Official ReVanced API)
  * `https://api.morphe.software/v2` (Official Morphe API)
  * `https://api.redgifs.com` (Reddit video playback token negotiation)
  * `https://api.deepl.com` (Morphe Reddit comment translation)
* **Malicious Network Indicators**:
  * Zero command-and-control (C2) servers, Telegram bots (`api.telegram.org`), Discord webhooks, or dynamic code loading droppers were discovered in any APK.

---

## 7. Repository Structure & Documentation Index

```
.
├── .gitignore                      # Configured to exclude heavy binaries (*.apk, *.dex)
├── README.md                       # Root documentation, executive summary, and navigation
└── vanced-audit/
    ├── README.md                   # Audit index and overview
    ├── 00-summary.md               # Executive summary, methodology, and audit bounds
    ├── 01-hashes.csv               # CSV ledger of hashes, sizes, signers, and URLs
    ├── 02-manager.md               # In-depth architectural audit of ReVanced Manager Plus
    ├── 03-patches.md               # Proofs of patch framework provenance and commit IDs
    ├── 04-network.md               # Consolidated domain ledger and security classifications
    ├── 05-malware-report.md        # Formal malware report, IOCs, and false-positive notes
    ├── 06-recs.md                  # Secure local patching guide and official source links
    ├── apks-tested-report/         # Individual application forensic dossiers
    │   ├── gphotos.md              # Morphe Google Photos teardown
    │   ├── reddit.md               # Morphe Reddit teardown
    │   ├── strava.md               # Strava subscription hook teardown
    │   ├── youtube.md              # YouTube ReVanced teardown
    │   └── ytmusic.md              # YouTube Music ReVanced teardown
    └── apks-tested/                # Tested target APK sample binaries (excluded from git)
        └── README.md               # Instructions for verifying sample binary hashes
```

---

## 8. Defensive Methodology & Boundaries

The investigation adhered strictly to the **Reverse-Engineer-Anything (REA)** defensive mobile-application analysis workflow:

```
Acquisition (apks-tested/) ──> Hash Ledger (SHA-256) ──> Signing Block Inspection (v1/v2/v3)
                                                                 │
                                                                 ▼
Network Classification <── Dalvik Decompilation (JADX) <── Manifest Decoding (pyaxmlparser)
         │
         ▼
Patch Provenance Diffing ──> False-Positive Evaluation ──> Final Threat Ledger
```

### Audit Limitations
1. **Static Bounded Search**: Findings are based purely on static archive inventory, binary XML parsing, and Dalvik bytecode decompilation. Binaries were never executed on real hardware or host environments.
2. **Server-Side Mutability**: The clean findings for the 5 prebuilt applications reflect the exact artifacts downloaded on **12 November 2026**. Third-party distribution hubs can swap files on their servers at any point without notice.

---

## 9. Safe Recommendations & Official Alternatives

Detailed recommendations and step-by-step CLI compilation commands are available in [`06-recs.md`](06-recs.md):

1. **Uninstall ReVanced Manager Plus**: Do not use `com.revanced.net.revancedmanager`. It introduces an unverified, unauthenticated sideloading channel.
2. **Avoid Prebuilt Modified APKs**: Never download pre-compiled modded APKs from third-party sites (`vanced.to`, `revanced.net`, unofficial Telegram groups).
3. **Patch Locally via Official ReVanced Manager**:
   * Download the authentic manager strictly from [revanced.app](https://revanced.app) or [`ReVanced/revanced-manager`](https://github.com/ReVanced/revanced-manager).
   * Obtain unmodified, clean stock APKs from reputable repositories (such as APKMirror).
   * Apply patches locally on your own device.
4. **Build with ReVanced CLI and Private Keystores**: For maximum security, build patched applications using `revanced-cli.jar` signed with your own private keystore, preventing any third-party update hijacking.

---

## 10. Disclaimer & Research Context

This repository represents independent, defensive security research and software supply-chain analysis. All trademarks, service marks, and trade names are the property of their respective owners. The tools and techniques described herein are presented solely for threat analysis, malware detection, and user protection.

