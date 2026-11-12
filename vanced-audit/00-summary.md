# Forensic Audit Summary: ReVanced Manager Plus & Prebuilt Patched APKs (vanced.to)

**Date of Audit**: 12 November 2026  
**Analyst**: Defensive Mobile Malware Analyst  
**Investigation Framework**: Reverse-Engineer-Anything (REA) Android Static Analysis Route  
**Target Source**: `https://vanced.to/revanced-manager` and `https://vanced.to/apps`  
**Host Environment**: Windows 11, Node.js v22.13.0, Java 21 (Eclipse Adoptium), JADX 1.5.1, Python 3.14.7  

---

## 1. Executive Summary

An in-depth defensive static malware audit was conducted on six Android binaries obtained from `vanced.to`, an unvetted third-party website distributing modified applications. The targets comprised **ReVanced Manager Plus** (the site's flagship distribution utility) and five popular prebuilt patched applications: **Morphe Google Photos**, **Morphe Reddit**, **Strava**, **YouTube ReVanced**, and **YouTube Music ReVanced**.

The investigation answered two core questions:
1. *Are the distributed APKs honest community-patched builds or do they contain malware, loggers, spyware, or hidden payloads?*
2. *Which exact community patch sets and versions were used?*

### Key Analytical Findings
* **Prebuilt Patched Apps (Clean-as-patched)**:
  All five prebuilt applications (YouTube, YouTube Music, Reddit, Google Photos, and Strava) were verified to be **honest community builds**. Their code additions match known open-source patch frameworks: official **ReVanced Patches** (`v6.3.0` and `v6.2.0`), official **Morphe Patches** (`v2.2.0.5`), and **Rushii's universal patch bundle** (`p1.23.1`). No trojans, spyware, keyloggers, remote exfiltration webhooks, or second-stage payloads were detected within the bounded static analysis. All five share the standard ReVanced community test signing key (`CN=ReVanced`).
* **ReVanced Manager Plus (Suspicious / High Supply-Chain Risk)**:
  `ReVanced Manager Plus` (`com.revanced.net.revancedmanager`) is **not** an authentic ReVanced product. It is a proprietary Kotlin application signed with a key registered to `CN=revanced.net`. It contains **no patcher engine** and cannot modify apps locally. Instead, it operates strictly as an unauthorized app store that downloads prebuilt APKs from `vanced.to` and `github.com/vancedto/vanced.to/releases/`. Crucially, it requests elevated installation permissions (`REQUEST_INSTALL_PACKAGES` and `UPDATE_PACKAGES_WITHOUT_USER_ACTION`) while performing **zero cryptographic verification** (no SHA-256 or signature check) on downloaded APKs, checking only HTTP content length. This creates an unmitigated supply-chain vulnerability.

---

## 2. Verdict & Risk Table

| Application | File Name | Version | Verdict | Risk Level | Key Forensic Rationale | Evidence ID |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **ReVanced Manager Plus** | `vanced.to_revanced_manager_plus_v3.6.25.apk` | 3.6.25 | **Suspicious** | **High** | Deceptive branding; author signed by `revanced.net`; contains zero patcher code; downloads and installs remote APKs without hash or signature verification | `EVD-MGR-VRD-001` |
| **Morphe Google Photos** | `vanced.to_rushi_com.google.android.apps.photos_...` | 7.98.0.1001971222 | **Clean-as-patched** | Low | Modifications fully explained by Rushii Morphe patch set (`p1.23.1`) for Pixel spoofing and DCIM isolation; standard ReVanced test signer | `EVD-GPH-VRD-001` |
| **Morphe Reddit** | `vanced.to_morphe_com.reddit.frontpage_...` | 2026.40.0 | **Clean-as-patched** | Low | Modifications fully explained by official MorpheApp Reddit patches (`p2.2.0.5`, commit `c90067a430`) for ad-blocking; standard ReVanced test signer | `EVD-RED-VRD-001` |
| **Strava** | `vanced.to_rushi_com.strava_...` | 480.15 | **Clean-as-patched** | Low | Isolated client-side entitlement patch in `classes9.dex` (`SubscriptionDetail`); no location tracking exfiltration; standard ReVanced test signer | `EVD-STR-VRD-001` |
| **YouTube ReVanced** | `vanced.to_revanced_com.google.android.youtube_...` | 21.16.256 | **Clean-as-patched** | Low | Modifications fully explained by official ReVanced patches (`p6.3.0`, commit `cb9a113e85`); standard SponsorBlock/RYD endpoints; ReVanced test signer | `EVD-YTB-VRD-001` |
| **YouTube Music ReVanced** | `vanced.to_revanced_com.google.android.apps.youtube.music_...` | 9.15.51 | **Clean-as-patched** | Low | Modifications fully explained by official ReVanced patches (`p6.2.0`, commit `c6507b5051`); ad-blocking and audio-only playback; ReVanced test signer | `EVD-YTM-VRD-001` |

---

## 3. Investigation Methodology

The assessment adhered strictly to the **Reverse-Engineer-Anything (REA)** defensive mobile-application analysis workflow, maintaining a bounded search with summary-first indexing and retained Evidence IDs.

```
+-----------------------------------------------------------------------------------+
|                            REA FORENSIC WORKFLOW                                  |
+-----------------------------------------------------------------------------------+
|  1. Acquisition & Hash Baseline (vanced-audit/samples/)                            |
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

## 4. Audit Boundaries & Limitations

1. **Static Analysis Boundary**:
   All findings are based on static inspection of shipped APK archive structures, binary XML manifests, Dalvik bytecode, and static string literals. In accordance with strict defensive rules, binaries were never installed or executed on real hardware or host operating systems.
2. **Server-Side Mutability**:
   `vanced.to` and its associated GitHub repository (`vancedto/vanced.to`) serve files over dynamic HTTP endpoints (`apps-v3.json`). A static verification confirming that binaries were clean at the time of download does not guarantee that future downloads will remain untampered.
3. **Host Tooling Context**:
   The native `rea-agents` npm package required Node.js `>=22.23.4` (for built-in `zstdDecompress`), whereas the host platform provided Node.js `v22.13.0`. In compliance with REA connection and recovery protocols, the exact REA Android inspection pipeline was executed using Python 3.14, standard cryptographic modules, and standalone JADX `1.5.1` running on Java 21, retaining all Evidence IDs.

---

## 5. Audit Documentation Index

The complete audit output is structured into the following files:

* [`00-summary.md`](00-summary.md): This executive report, verdict matrix, and methodology.
* [`01-hashes.csv`](01-hashes.csv): Complete tabular record of filenames, versions, sizes, SHA-256, SHA-1, MD5, signer fingerprints, and download URLs.
* [`02-manager.md`](02-manager.md): Comprehensive teardown of ReVanced Manager Plus architecture, signing, and verification flaws.
* **Application Dossiers**:
  * [`apps/gphotos.md`](apps/gphotos.md): Morphe Google Photos analysis.
  * [`apps/reddit.md`](apps/reddit.md): Morphe Reddit analysis.
  * [`apps/strava.md`](apps/strava.md): Strava subscription entitlement analysis.
  * [`apps/youtube.md`](apps/youtube.md): YouTube ReVanced flagship analysis.
  * [`apps/ytmusic.md`](apps/ytmusic.md): YouTube Music ReVanced analysis.
* [`03-patches.md`](03-patches.md): Community patch framework identification, version mapping, and commit tracking.
* [`04-network.md`](04-network.md): Consolidated domain and IP classification ledger.
* [`05-malware-report.md`](05-malware-report.md): Formal malware findings, indicators of compromise, and false-positive notes.
* [`06-recs.md`](06-recs.md): Guidance on safe local compilation using official ReVanced Manager and CLI.

