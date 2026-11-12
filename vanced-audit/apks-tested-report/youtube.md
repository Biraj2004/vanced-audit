# Application Audit: YouTube ReVanced

## 1. Identity & File Attributes

| Attribute | Details | Evidence ID |
| :--- | :--- | :--- |
| **App Name** | YouTube ReVanced (Flagship Ecosystem Target) | `EVD-YTB-ID-001` |
| **Package Name** | `app.revanced.android.youtube` | `EVD-YTB-ID-002` |
| **Original Package** | `com.google.android.youtube` | `EVD-YTB-ID-003` |
| **Version Name** | `21.16.256` | `EVD-YTB-ID-004` |
| **Version Code** | `1561068412` | `EVD-YTB-ID-005` |
| **File Size** | 195,914,241 bytes (~186.8 MiB) | `EVD-YTB-BIN-001` |
| **SHA-256** | `e948a7ef5692c227ab7f8425cb71818e7f075f6d3bba61346fb68fc00afdfb7c` | `EVD-YTB-BIN-002` |
| **SHA-1** | `dac7a8c56efad4dbb1b6ae9e292b5b50a365713e` | `EVD-YTB-BIN-003` |
| **MD5** | `591ad66d7a62461d5bf17c1a0f85cf96` | `EVD-YTB-BIN-004` |
| **Target Architecture** | `arm64-v8a` | `EVD-YTB-BIN-005` |

---

## 2. Signing & Certificate Verification

* **Signing Scheme**: Dual APK Signature Scheme v2 and v3 (Block IDs `0x7109871a`, `0xf05368c0`) [OBSERVED, `EVD-YTB-SIG-001`]
* **Certificate Subject**: `CN=ReVanced` [OBSERVED, `EVD-YTB-SIG-002`]
* **Certificate Issuer**: `CN=ReVanced` [OBSERVED, `EVD-YTB-SIG-003`]
* **Certificate Serial**: `8194446765327472407` [OBSERVED, `EVD-YTB-SIG-004`]
* **Validity Period**: `2023-07-14T04:24:59Z` to `2053-09-04T04:24:59Z` [OBSERVED, `EVD-YTB-SIG-005`]
* **SHA-256 Fingerprint**: `5d87c9bdc8e368dcf80971f33455db4c2e7dd754e1f288f1162c04b2a4811708` [OBSERVED, `EVD-YTB-SIG-006`]

### Signer Analysis
Signed with the official default ReVanced CLI keystore, matching the signer fingerprint across all genuine community builds.

---

## 3. Manifest & Component Analysis

* **Package Renaming**: Renamed from `com.google.android.youtube` to `app.revanced.android.youtube` [OBSERVED, `EVD-YTB-MAN-001`]. This decouples the APK from Google Play Services and permits standalone coexistence with stock YouTube.
* **GmsCore Redirection**: Manifest hooks redirect authorization, account management, and push notification tokens from Google Play Services to **Vanced MicroG / GmsCore** (`app.revanced.android.gms`).
* **Permissions**:
  * Internet, Access Network State, Post Notifications, WakeLock, Foreground Service, Picture-in-Picture (`SYSTEM_ALERT_WINDOW` not granted by default; handled via modern Android PiP API).
  * No SMS, call log, contacts exfiltration, or device administrator permissions are requested [OBSERVED, `EVD-YTB-MAN-002`].

---

## 4. Code & Patch Provenance

### 4.1 Identified Patch Set
* **Patch Framework**: Official **ReVanced Patches** (`github.com/ReVanced/revanced-patches`) [OBSERVED, `EVD-YTB-PAT-001`].
* **CLI Version**: `6.0.0` [OBSERVED, `EVD-YTB-PAT-002`].
* **Patch Version**: `p6.3.0` [OBSERVED, `EVD-YTB-PAT-003`].
* **Git Commit**: `cb9a113e85` [OBSERVED, `EVD-YTB-PAT-004`].

### 4.2 Added Classes
ReVanced integration injects 1,903 extension classes spread across `classes.dex` through `classes8.dex`:
* `app.revanced.extension.youtube.patches.VideoAdsPatch`: Ad-blocking hooks for video streams and feed cards.
* `app.revanced.extension.youtube.patches.SponsorBlockPatch`: Community sponsor skipping via Ajay's SponsorBlock API.
* `app.revanced.extension.youtube.patches.ReturnYouTubeDislikePatch`: Re-enables public dislike metrics via RYD API.
* `app.revanced.extension.youtube.patches.DeArrowPatch`: Replaces clickbait video titles and thumbnails with community crowdsourced alternatives.
* `app.revanced.extension.youtube.patches.BackgroundPlaybackPatch`: Enables audio playback when the screen is locked or application is minimized.
* `app.revanced.extension.youtube.patches.MicroGSupportPatch`: Client-side OAuth token proxying for MicroG.

### 4.3 Malicious Code Search
* No hidden second-stage payloads or encrypted `.dex` / `.jar` archives in `assets/` or `res/raw/`. The `.dat` files in `res/raw/` are authentic Google YouTube protobuf element templates (`downloads_page_video_element_android.dat`, etc.).
* No credential harvesters, keyloggers, or unauthorized telemetry found.

---

## 5. Network Endpoints

All external domains extracted from Dalvik strings map cleanly into legitimate services:

| Domain / Endpoint | Classification | Context / Purpose | Evidence ID |
| :--- | :--- | :--- | :--- |
| `https://sponsor.ajay.app` | Expected Patch Framework | SponsorBlock segment database | `EVD-YTB-NET-001` |
| `https://returnyoutubedislikeapi.com/` | Expected Patch Framework | Return YouTube Dislike metrics API | `EVD-YTB-NET-002` |
| `https://dearrow.ajay.app` | Expected Patch Framework | DeArrow community thumbnail & title API | `EVD-YTB-NET-003` |
| `https://api.revanced.app/v4` & `v5` | Expected Patch Framework | ReVanced announcement & patch configuration | `EVD-YTB-NET-004` |
| `https://github.com/revanced/gmscore` | Expected Patch Framework | GmsCore companion download link | `EVD-YTB-NET-005` |
| `*.youtube.com`, `*.googlevideo.com` | Expected Original Vendor | Video streaming and feed API | `EVD-YTB-NET-006` |
| `https://sb.ltn.fi/userid/` | Expected Patch Framework | SponsorBlock public contributor profile mirror | `EVD-YTB-NET-007` |

---

## 6. Verdict & Findings

* **Verdict**: **Clean-as-patched**
* **Confidence**: High
* **Limitations**: Static inspection only; accounts must authenticate via MicroG, which constitutes a separate supply-chain dependency.
* **Summary**: The build is an authentic, unmodified compilation of official ReVanced patches (`v6.3.0`, commit `cb9a113e85`) applied to YouTube `v21.16.256`. No extraneous or malicious code was introduced by `vanced.to`.

