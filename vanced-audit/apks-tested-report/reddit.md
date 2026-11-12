# Application Audit: Morphe Reddit

## 1. Identity & File Attributes

| Attribute | Details | Evidence ID |
| :--- | :--- | :--- |
| **App Name** | Reddit (Morphe Patched) | `EVD-RED-ID-001` |
| **Package Name** | `com.reddit.frontpage` | `EVD-RED-ID-002` |
| **Original Package** | `com.reddit.frontpage` | `EVD-RED-ID-003` |
| **Version Name** | `2026.40.0` | `EVD-RED-ID-004` |
| **Version Code** | `2147483647` (Max integer to inhibit Play Store updates) | `EVD-RED-ID-005` |
| **File Size** | 88,417,484 bytes (~84.3 MiB) | `EVD-RED-BIN-001` |
| **SHA-256** | `a4468cd7924e55970303df7deca23162b5d01a8160b0ebafd9597d83fc041112` | `EVD-RED-BIN-002` |
| **SHA-1** | `510e5071973761c55b4f03d9d2d47bff912b9587` | `EVD-RED-BIN-003` |
| **MD5** | `66164e3f2fa81418d1469672b719a41a` | `EVD-RED-BIN-004` |
| **Target Architecture** | `arm64-v8a` | `EVD-RED-BIN-005` |

---

## 2. Signing & Certificate Verification

* **Signing Scheme**: APK Signature Scheme v2 (Block ID `0x7109871a`) [OBSERVED, `EVD-RED-SIG-001`]
* **Certificate Subject**: `CN=ReVanced` [OBSERVED, `EVD-RED-SIG-002`]
* **Certificate Issuer**: `CN=ReVanced` [OBSERVED, `EVD-RED-SIG-003`]
* **Certificate Serial**: `8194446765327472407` [OBSERVED, `EVD-RED-SIG-004`]
* **Validity Period**: `2023-07-14T04:24:59Z` to `2053-09-04T04:24:59Z` [OBSERVED, `EVD-RED-SIG-005`]
* **SHA-256 Fingerprint**: `5d87c9bdc8e368dcf80971f33455db4c2e7dd754e1f288f1162c04b2a4811708` [OBSERVED, `EVD-RED-SIG-006`]

### Signer Analysis
The build is signed with the ReVanced CLI test key. This confirms recompilation using standard community tooling.

---

## 3. Manifest & Component Analysis

* **Package Name**: Retains the original package name `com.reddit.frontpage`. Requires uninstallation of the official Reddit app prior to installation.
* **Version Code Manipulation**: The versionCode is set to `2147483647` (`Integer.MAX_VALUE`), which is an intentional community patch mechanism (`DisablePlayStoreUpdatesPatch`) to prevent Google Play Store from automatically overwriting the patched application with an unpatched stock release.
* **Permissions**: Standard Reddit application permissions:
  * Internet, Access Network State, Post Notifications, Read External Storage.
  * No dangerous system-level permissions (e.g. Accessibility service, Device admin, SMS, Call logs) were introduced [OBSERVED, `EVD-RED-MAN-001`].

---

## 4. Code & Patch Provenance

### 4.1 Identified Patch Set
* **Framework**: Official **Morphe** patch repository (`github.com/MorpheApp/morphe-patches`) [OBSERVED, `EVD-RED-PAT-001`].
* **CLI Version**: `2.2.0.12` [OBSERVED, `EVD-RED-PAT-002`].
* **Patch Version**: `p2.2.0.5` [OBSERVED, `EVD-RED-PAT-003`].
* **Git Commit**: `c90067a430` [OBSERVED in filename metadata, `EVD-RED-PAT-004`].

### 4.2 Added Classes
Morphe patch integration introduces 591 classes cleanly segmented into `classes14.dex`, `classes15.dex`, and `classes16.dex`:
* `app.morphe.extension.reddit.patches.HideAdsPatch`: Suppresses promoted posts and sponsored feed units.
* `app.morphe.extension.reddit.patches.CustomFontPatch` & `ForceSystemFontPatch`: Typography customization.
* `app.morphe.extension.reddit.patches.CopyTextPatch`: Allows copying text from locked or self-text posts.
* `app.morphe.extension.reddit.patches.DisableScreenshotPopupPatch`: Suppresses sharing prompts when taking screenshots.
* `app.morphe.extension.reddit.patches.OpenLinksExternallyPatch`: Redirects link clicks to the user's default browser.
* `app.morphe.extension.reddit.patches.HighQualityAvatarsPatch` & `MediaViewerBlackBackgroundPatch`: User interface styling.

### 4.3 Malicious Code Search
* Dynamic code loading: None in the Morphe patch packages.
* Native libraries: Identical native `.so` files to original Reddit release (`libruntimeexecutor.so`, `libhermes.so`, `libreactnativejni.so`).
* Webhooks / C2 channels: No Discord webhooks, Telegram bots, or unexplained command-and-control servers found.

---

## 5. Network Endpoints

| Category | Endpoints Observed | Purpose |
| :--- | :--- | :--- |
| **Vendor (Reddit)** | `*.reddit.com`, `*.redditmedia.com`, `*.redd.it` | Reddit content delivery and API operations |
| **Patch Framework** | `https://morphe.software`, `https://api.morphe.software/v2` | Morphe settings and version status |
| **DeepL Translation** | `https://api.deepl.com/v2/translate`, `https://api-free.deepl.com/v2/translate` | In-app comment and post translation feature |
| **Media Proxy** | `https://api.redgifs.com/v2/` | RedGIFs API token negotiation for embedded video playback |
| **Repository Links** | `https://github.com/MorpheApp/morphe-patches` | Open-source attribution links in settings |

---

## 6. Verdict & Findings

* **Verdict**: **Clean-as-patched**
* **Confidence**: High
* **Limitations**: Static code inspection only. Account security depends on third-party OAuth flows with Reddit.
* **Summary**: The modifications in this build strictly align with the documented, open-source Morphe Reddit patch suite (`v2.2.0.5`, commit `c90067a430`). No malicious behavior, loggers, or unauthorized exfiltration routines were detected.

