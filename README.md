# Mobile Malware & Supply-Chain Audit: ReVanced Manager Plus & Prebuilt Patched APKs (vanced.to)

![Analysis](https://img.shields.io/badge/Analysis-Static%20Defensive%20REA-blue)
![Platform](https://img.shields.io/badge/Platform-Android%20APK-green)
![Safety](https://img.shields.io/badge/Safety-Isolated%20%2F%20No%20Execution-success)
![Audit Status](https://img.shields.io/badge/Audit%20Status-Complete-brightgreen)
![Language](https://img.shields.io/badge/Language-English%20(UK%2FIndia)-informational)

This repository contains a comprehensive, defensive mobile malware and software supply-chain security audit of **ReVanced Manager Plus** and **five popular prebuilt patched Android applications** distributed by the third-party portal `https://vanced.to`.

All assessments were conducted strictly under the **Reverse-Engineer-Anything (REA)** defensive mobile-application inspection workflow. Binary targets were evaluated in an isolated environment using static archive inventory, binary XML parsing, Dalvik bytecode decompilation, signature block validation, and string pool network triage, without host or device execution.

---

## Table of Contents

* [1. Executive Summary](#1-executive-summary)
* [2. Consolidated Verdict Matrix](#2-consolidated-verdict-matrix)
* [3. Target Inventory & Cryptographic Hashes](#3-target-inventory--cryptographic-hashes)
* [4. Key Security Findings & Threat Breakdown](#4-key-security-findings--threat-breakdown)
  * [4.1 ReVanced Manager Plus: Impersonation & Insecure Sideloading Pipeline](#41-revanced-manager-plus-impersonation--insecure-sideloading-pipeline)
  * [4.2 Prebuilt Applications: Verified Authentic Community Patches](#42-prebuilt-applications-verified-authentic-community-patches)
* [5. Patch Provenance & Open-Source Lineage](#5-patch-provenance--open-source-lineage)
* [6. Network Endpoint Triage & Infrastructure Analysis](#6-network-endpoint-triage--infrastructure-analysis)
* [7. Defensive Investigation Methodology](#7-defensive-investigation-methodology)
* [8. Repository Layout & Audit Document Index](#8-repository-layout--audit-document-index)
* [9. Defensive Recommendations & DIY Local Build Guide](#9-defensive-recommendations--diy-local-build-guide)
* [10. Audit Limitations & Disclaimer](#10-audit-limitations--disclaimer)

---

## 1. Executive Summary

Third-party repositories that distribute pre-compiled, modified Android applications represent a severe software supply-chain threat vector. End users typically have no mechanism to distinguish between genuine community enhancements and weaponised builds containing surveillance spyware, keyloggers, or remote access trojans (RATs).

This defensive audit evaluated six application packages obtained from `vanced.to` to establish:
1. Whether each distributed APK is an honest community-patched build or contains malicious telemetry, trojans, credential loggers, or remote droppers.
2. The exact community patch frameworks, versions, and git commits incorporated into each binary.

### Core Analytical Conclusions

1. **Prebuilt Patched Applications (`Clean-as-patched`)**:
   * **Morphe Google Photos**, **Morphe Reddit**, **Strava**, **YouTube ReVanced**, and **YouTube Music ReVanced** were confirmed to be **authentic community builds**.
   * Bytecode additions across all five applications correspond strictly to documented open-source patch repositories: **official ReVanced** (`v6.3.0` and `v6.2.0`), **official Morphe** (`v2.2.0.5`), and **Rushii's universal patch bundle** (`p1.23.1`).
   * No spyware, keylogging hooks, audio or screen capture routines, unauthorized contact or SMS harvesting, or secondary executable droppers were identified within the bounded static analysis.
   * All five applications are signed using the standard open-source ReVanced CLI community debug keystore (`CN=ReVanced`).

2. **ReVanced Manager Plus (`Suspicious` / High Supply-Chain Risk)**:
   * **Not an official ReVanced application**. It utilizes the package name `com.revanced.net.revancedmanager` and is signed with a private keystore registered to `CN=revanced.net`.
   * **Contains zero patch engine code**. The binary cannot parse, decompile, or patch APKs locally; instead, it acts as an unvetted APK downloader and sideload installer querying `https://vanced.to/apps-v3.json`.
   * **Critical verification flaw**: In `DownloadWorker.java`, the utility requests elevated package installation permissions (`REQUEST_INSTALL_PACKAGES` and `UPDATE_PACKAGES_WITHOUT_USER_ACTION`) but performs **zero cryptographic hash or certificate validation** on downloaded binaries, checking only the HTTP `content-length` header. A compromise of the hosting infrastructure would enable silent distribution of malicious payloads to all installed clients.

---

## 2. Consolidated Verdict Matrix

| Application | Package Name | Version | Verdict | Confidence | Risk Level | Primary Analytical Rationale | Forensic Dossier |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **ReVanced Manager Plus** | `com.revanced.net.revancedmanager` | 3.6.25 | **Suspicious** | High | **High** | Deceptive branding; author signed by `CN=revanced.net`; contains zero patcher engine code; downloads remote APKs without hash or signature checks | [`02-manager.md`](vanced-audit/02-manager.md) |
| **Morphe Google Photos** | `app.morphe.android.apps.photos` | 7.98.0.1001971222 | **Clean-as-patched** | High | Low | Modifications fully explained by Rushii Morphe patch set (`p1.23.1`) for Pixel spoofing and DCIM isolation; standard ReVanced test signer | [`gphotos.md`](vanced-audit/apks-tested-report/gphotos.md) |
| **Morphe Reddit** | `com.reddit.frontpage` | 2026.40.0 | **Clean-as-patched** | High | Low | Modifications fully explained by official MorpheApp Reddit patch set (`p2.2.0.5`, commit `c90067a430`) for ad-blocking; standard ReVanced test signer | [`reddit.md`](vanced-audit/apks-tested-report/reddit.md) |
| **Strava** | `com.strava` | 480.15 | **Clean-as-patched** | High | Low | Isolated client-side entitlement patch in `classes9.dex` (`SubscriptionDetail`); no location tracking exfiltration; standard ReVanced test signer | [`strava.md`](vanced-audit/apks-tested-report/strava.md) |
| **YouTube ReVanced** | `app.revanced.android.youtube` | 21.16.256 | **Clean-as-patched** | High | Low | Modifications fully explained by official ReVanced patches (`p6.3.0`, commit `cb9a113e85`); standard SponsorBlock/RYD endpoints; ReVanced test signer | [`youtube.md`](vanced-audit/apks-tested-report/youtube.md) |
| **YouTube Music ReVanced** | `app.revanced.android.apps.youtube.music` | 9.15.51 | **Clean-as-patched** | High | Low | Modifications fully explained by official ReVanced patches (`p6.2.0`, commit `c6507b5051`); ad-blocking and audio-only playback; ReVanced test signer | [`ytmusic.md`](vanced-audit/apks-tested-report/ytmusic.md) |

---

## 3. Target Inventory & Cryptographic Hashes

All cryptographic digests were computed locally from pristine downloaded samples. A machine-readable copy is maintained in [`vanced-audit/01-hashes.csv`](vanced-audit/01-hashes.csv):

| Application | File Name | Size (Bytes) | SHA-256 Digest | Signer SHA-256 Fingerprint |
| :--- | :--- | :--- | :--- | :--- |
| **ReVanced Manager Plus** | `vanced.to_revanced_manager_plus_v3.6.25.apk` | 2,477,424 | `f13df8fb954e096ec4f2d79718f8161f7b771b9094bc105600a7493837045a60` | `74a62bdf5dc3105af7ac189364e4925d84a669ca21abe3bc709d61a3f48e1379` |
| **Morphe Google Photos** | `vanced.to_rushi_com.google.android.apps.photos_arm64v8a_v7.98.0.1001971222_cli1.20.0.5_p1.23.1.apk` | 134,247,409 | `be9bb732f7c3f014299769a66b4f70730d08a2c310dd8f517695a92cbd4939f4` | `5d87c9bdc8e368dcf80971f33455db4c2e7dd754e1f288f1162c04b2a4811708` |
| **Morphe Reddit** | `vanced.to_morphe_com.reddit.frontpage_arm64v8a_v2026.40.0_cli2.2.0.12_p2.2.0.5_c90067a430.apk` | 88,417,484 | `a4468cd7924e55970303df7deca23162b5d01a8160b0ebafd9597d83fc041112` | `5d87c9bdc8e368dcf80971f33455db4c2e7dd754e1f288f1162c04b2a4811708` |
| **Strava** | `vanced.to_rushi_com.strava_arm64v8a_v480.15_cli1.20.0.5_p1.23.1.1.apk` | 122,829,417 | `60b3e22f226067d473adb032d83972750112ccdcb5b95bc0efe603b6650f14d8` | `5d87c9bdc8e368dcf80971f33455db4c2e7dd754e1f288f1162c04b2a4811708` |
| **YouTube ReVanced** | `vanced.to_revanced_com.google.android.youtube_arm64v8a_v21.16.256_cli6.0.0_p6.3.0_cb9a113e85.apk` | 195,914,241 | `e948a7ef5692c227ab7f8425cb71818e7f075f6d3bba61346fb68fc00afdfb7c` | `5d87c9bdc8e368dcf80971f33455db4c2e7dd754e1f288f1162c04b2a4811708` |
| **YouTube Music ReVanced** | `vanced.to_revanced_com.google.android.apps.youtube.music_arm64v8a_v9.15.51_cli6.0.0_p6.2.0_c6507b5051.apk` | 78,762,579 | `841156b72979b80ff05aafbcd9cb8269a04e5688f4c25cf500e3abbe902ee32c` | `5d87c9bdc8e368dcf80971f33455db4c2e7dd754e1f288f1162c04b2a4811708` |

---

## 4. Key Security Findings & Threat Breakdown

### 4.1 ReVanced Manager Plus: Impersonation & Insecure Sideloading Pipeline

Forensic decompilation of `ReVanced Manager Plus` (`v3.6.25`) established that the software does not originate from the ReVanced organisation and lacks the technical capability to patch applications:

1. **Keystore Discrepancy**: The package is signed with an untrusted certificate issued to `CN=revanced.net, OU=ReVanced, O=ReVanced, L=San Francisco, ST=California, C=US` (`SHA-256: 74a62bdf...`). Official ReVanced applications originate from `app.revanced.manager.flutter`.
2. **Absence of Local Patcher Engine**: Decompiled bytecode reveals no presence of `revanced-patcher`, `smali`, `baksmali`, or `dexlib2`. The application is incapable of inspecting or recompiling APK files on device.
3. **Insecure Downloader Architecture**: The client queries `https://vanced.to/apps-v3.json` to retrieve remote APK download links hosted on `github.com/vancedto/vanced.to/releases/`.
4. **Missing Cryptographic Integrity Checks**: Inspection of `DownloadWorker.java` confirms that the application checks solely whether the downloaded byte length matches the HTTP `content-length` header. It performs **no SHA-256 verification** and **no APK signing certificate validation**. Combined with Android install permissions (`REQUEST_INSTALL_PACKAGES` and `UPDATE_PACKAGES_WITHOUT_USER_ACTION`), this presents a severe supply-chain attack surface.

Full technical details: [`vanced-audit/02-manager.md`](vanced-audit/02-manager.md).

### 4.2 Prebuilt Applications: Verified Authentic Community Patches

Static analysis of the five prebuilt application binaries demonstrated that their code additions match verified open-source modification frameworks:

* **Morphe Google Photos**: Injects `PixelSpoofHelper` and `PhotosDcimFolderPatch` in `classes11.dex`. It enables unlimited original-quality backups by spoofing Google Pixel device properties. All 29 binary assets in `assets/` were verified as stock Google white-balance and VR models. (Dossier: [`vanced-audit/apks-tested-report/gphotos.md`](vanced-audit/apks-tested-report/gphotos.md)).
* **Morphe Reddit**: Injects 591 classes under `app.morphe.extension.reddit.patches.*` across `classes14.dex` through `classes16.dex`. Implements feed ad-blocking, text selection, and custom typography. The `versionCode` is set to `2147483647` (`Integer.MAX_VALUE`) to inhibit automatic Play Store overwriting. (Dossier: [`vanced-audit/apks-tested-report/reddit.md`](vanced-audit/apks-tested-report/reddit.md)).
* **Strava**: Replaces subscription entitlement logic via a compact `classes9.dex` (13.8 KiB) containing an overridden `SubscriptionDetail` model that forces `isPremium() = true`. No telemetry or GPS exfiltration hooks were detected. (Dossier: [`vanced-audit/apks-tested-report/strava.md`](vanced-audit/apks-tested-report/strava.md)).
* **YouTube ReVanced**: Contains 1,903 patch classes (`app.revanced.extension.youtube.*`) matching official ReVanced patch set `v6.3.0` (commit `cb9a113e85`). Employs standard SponsorBlock, ReturnYouTubeDislike, and DeArrow community APIs. (Dossier: [`vanced-audit/apks-tested-report/youtube.md`](vanced-audit/apks-tested-report/youtube.md)).
* **YouTube Music ReVanced**: Contains 1,106 patch classes (`app.revanced.extension.music.*`) matching official ReVanced patch set `v6.2.0` (commit `c6507b5051`). Provides ad removal and background audio streaming. (Dossier: [`vanced-audit/apks-tested-report/ytmusic.md`](vanced-audit/apks-tested-report/ytmusic.md)).

---

## 5. Patch Provenance & Open-Source Lineage

The provenance of each patch layer was correlated against upstream Git repositories and release tags:

| Application | Build Provenance Tag | Verified Patch Repository | Upstream Release / Commit |
| :--- | :--- | :--- | :--- |
| **YouTube ReVanced** | `cli6.0.0_p6.3.0_cb9a113e85` | `https://github.com/ReVanced/revanced-patches` | `v6.3.0` (`cb9a113e85`) |
| **YouTube Music ReVanced** | `cli6.0.0_p6.2.0_c6507b5051` | `https://github.com/ReVanced/revanced-patches` | `v6.2.0` (`c6507b5051`) |
| **Morphe Reddit** | `cli2.2.0.12_p2.2.0.5_c90067a430` | `https://github.com/MorpheApp/morphe-patches` | `v2.2.0.5` (`c90067a430`) |
| **Morphe Google Photos** | `cli1.20.0.5_p1.23.1` | `https://github.com/rushiiMachine/revanced-patches` | `p1.23.1` |
| **Strava** | `cli1.20.0.5_p1.23.1.1` | `https://github.com/rushiiMachine/revanced-patches` | `p1.23.1.1` |

Detailed bytecode diffing notes: [`vanced-audit/03-patches.md`](vanced-audit/03-patches.md).

---

## 6. Network Endpoint Triage & Infrastructure Analysis

A total of 435 unique network domains were extracted across all target binaries. Full categorization is documented in [`vanced-audit/04-network.md`](vanced-audit/04-network.md):

* **Distribution Infrastructure (`vanced.to`)**:
  * `https://vanced.to/apps-v3.json`: Primary application catalogue manifest.
  * `https://vanced.to/revanced-apps.json`: Legacy catalogue manifest.
  * `https://github.com/vancedto/vanced.to/releases/`: CDN storage for prebuilt APKs.
  * `https://vanced.to/donate-redir`: Commercial donation redirect.
* **Community Patch Infrastructure**:
  * `https://sponsor.ajay.app`: SponsorBlock crowdsourced segment skipping.
  * `https://returnyoutubedislikeapi.com`: Public video dislike count restoration.
  * `https://dearrow.ajay.app`: Crowdsourced thumbnail and title formatting.
  * `https://api.revanced.app`: Official ReVanced configuration and update checking.
  * `https://api.morphe.software/v2`: Morphe extension API.
* **Threat Indicators**:
  * Zero command-and-control (C2) domains, Telegram bots (`api.telegram.org`), Discord webhooks, or secondary droppers were detected in any APK.

---

## 7. Defensive Investigation Methodology

The assessment followed the standardized **Reverse-Engineer-Anything (REA)** defensive mobile malware audit pipeline:

```
+-----------------------------------------------------------------------------------+
|                            REA FORENSIC WORKFLOW                                  |
+-----------------------------------------------------------------------------------+
|  1. Acquisition & Hash Baseline (vanced-audit/apks-tested/)                       |
|     -> SHA-256, SHA-1, MD5, byte size, download timestamps                        |
+-----------------------------------------------------------------------------------+
                                         |
                                         v
+-----------------------------------------------------------------------------------+
|  2. Archive Inventory (open_binary equivalent)                                    |
|     -> Complete ZIP member catalogue, DEX files, native .so libraries, assets     |
+-----------------------------------------------------------------------------------+
                                         |
                                         v
+-----------------------------------------------------------------------------------+
|  3. Signing Block Inspection (inspect_apk_signature)                              |
|     -> Parsing APK Signing Block (magic: APK Sig Block 42)                        |
|     -> Scheme discovery (v1, v2, v3, v3.1), X.509 DER extraction, SHA-256 cert FP |
+-----------------------------------------------------------------------------------+
                                         |
                                         v
+-----------------------------------------------------------------------------------+
|  4. Package Manifest Analysis (inspect_android_package)                           |
|     -> Binary AndroidManifest.xml decoding, permissions, exported components      |
+-----------------------------------------------------------------------------------+
                                         |
                                         v
+-----------------------------------------------------------------------------------+
|  5. Dalvik Bytecode & Extension Decompilation (JADX / DexParser)                  |
|     -> Class hierarchy separation (Vendor vs Community Integrations vs Unknown)   |
|     -> Targeted sensitive API pattern matching (DCL, reflection, C2 webhooks)     |
+-----------------------------------------------------------------------------------+
                                         |
                                         v
+-----------------------------------------------------------------------------------+
|  6. Network Endpoint Extraction & Classification                                  |
|     -> Consolidated URI regex extraction from DEX string pools                    |
|     -> Categorisation: Expected Vendor, Patch Infrastructure, Analytics, Unknown  |
+-----------------------------------------------------------------------------------+
                                         |
                                         v
+-----------------------------------------------------------------------------------+
|  7. Provenance Verification & Reputation Correlation                              |
|     -> Matching extension fingerprints and commit tags to open-source patch repos |
|     -> Threat intelligence hash checks (MalwareBazaar, VirusTotal, Exodus)        |
+-----------------------------------------------------------------------------------+
```

---

## 8. Repository Layout & Audit Document Index

```
.
├── .gitignore                          # Excludes heavy binaries (*.apk, *.dex, *.so)
├── README.md                           # Flagship audit presentation and navigation
└── vanced-audit/
    ├── README.md                       # Subdirectory documentation index
    ├── 00-summary.md                   # Executive summary, methodology, and audit bounds
    ├── 01-hashes.csv                   # CSV ledger of hashes, sizes, signers, and URLs
    ├── 02-manager.md                   # In-depth architectural audit of ReVanced Manager Plus
    ├── 03-patches.md                   # Proofs of patch framework provenance and commit IDs
    ├── 04-network.md                   # Consolidated domain ledger and security classifications
    ├── 05-malware-report.md            # Formal malware report, IOCs, and false-positive notes
    ├── 06-recs.md                      # Secure local patching guide and official source links
    ├── apks-tested-report/             # Deep-dive application forensic dossiers
    │   ├── gphotos.md                  # Morphe Google Photos teardown
    │   ├── reddit.md                   # Morphe Reddit teardown
    │   ├── strava.md                   # Strava subscription hook teardown
    │   ├── youtube.md                  # YouTube ReVanced teardown
    │   └── ytmusic.md                  # YouTube Music ReVanced teardown
    └── apks-tested/                    # Sample target APKs (excluded from git tracking)
        └── README.md                   # Cryptographic verification instructions for binaries
```

---

## 9. Defensive Recommendations & DIY Local Build Guide

For complete security and protection against supply-chain tampering, follow the actionable guidance in [`vanced-audit/06-recs.md`](vanced-audit/06-recs.md):

1. **Remove ReVanced Manager Plus**: Immediately uninstall `com.revanced.net.revancedmanager`. It is an unverified distribution channel lacking integrity controls.
2. **Cease Downloading Pre-Compiled Patched APKs**: Never obtain prebuilt modified binaries from third-party websites or Telegram channels. Even if current files are benign, dynamic server-side file replacement can occur without warning.
3. **Compile Patches Locally via Official Toolchains**:
   * Acquire official ReVanced Manager strictly from [revanced.app](https://revanced.app) or [`ReVanced/revanced-manager`](https://github.com/ReVanced/revanced-manager).
   * Obtain unmodified vendor APKs from reputable sources (such as APKMirror).
   * Patch applications locally on your Android device.
4. **Reproducible Compilation with ReVanced CLI**: Build patched applications using `revanced-cli.jar` signed with a private keystore under your direct control to prevent update hijacking.

---

## 10. Audit Limitations & Disclaimer

This audit represents independent, defensive security analysis and software supply-chain research.
1. **Static Analysis Bounds**: Conclusions are derived exclusively from static inspection of archive files, manifests, and Dalvik bytecode. Binaries were never executed on real hardware or host platforms.
2. **Temporal Validity**: Clean findings for the prebuilt applications reflect the exact cryptographic artifacts downloaded on **12 November 2026**.
3. **Trademark Notice**: All product names, logos, and brands are property of their respective owners. Analysis is conducted strictly for educational, defensive, and research purposes.
