# Application Audit: YouTube Music ReVanced

## 1. Identity & File Attributes

| Attribute | Details | Evidence ID |
| :--- | :--- | :--- |
| **App Name** | YouTube Music ReVanced (Companion Audio Target) | `EVD-YTM-ID-001` |
| **Package Name** | `app.revanced.android.apps.youtube.music` | `EVD-YTM-ID-002` |
| **Original Package** | `com.google.android.apps.youtube.music` | `EVD-YTM-ID-003` |
| **Version Name** | `9.15.51` | `EVD-YTM-ID-004` |
| **Version Code** | `91551240` | `EVD-YTM-ID-005` |
| **File Size** | 78,762,579 bytes (~75.1 MiB) | `EVD-YTM-BIN-001` |
| **SHA-256** | `841156b72979b80ff05aafbcd9cb8269a04e5688f4c25cf500e3abbe902ee32c` | `EVD-YTM-BIN-002` |
| **SHA-1** | `094eae83ba9c96b7d03b3e2ed96104d0b03810a1` | `EVD-YTM-BIN-003` |
| **MD5** | `e16d50174ce4d8c30c2faa8c57bc4b47` | `EVD-YTM-BIN-004` |
| **Target Architecture** | `arm64-v8a` | `EVD-YTM-BIN-005` |

---

## 2. Signing & Certificate Verification

* **Signing Scheme**: Dual APK Signature Scheme v2 and v3 (Block IDs `0x7109871a`, `0xf05368c0`) [OBSERVED, `EVD-YTM-SIG-001`]
* **Certificate Subject**: `CN=ReVanced` [OBSERVED, `EVD-YTM-SIG-002`]
* **Certificate Issuer**: `CN=ReVanced` [OBSERVED, `EVD-YTM-SIG-003`]
* **Certificate Serial**: `8194446765327472407` [OBSERVED, `EVD-YTM-SIG-004`]
* **Validity Period**: `2023-07-14T04:24:59Z` to `2053-09-04T04:24:59Z` [OBSERVED, `EVD-YTM-SIG-005`]
* **SHA-256 Fingerprint**: `5d87c9bdc8e368dcf80971f33455db4c2e7dd754e1f288f1162c04b2a4811708` [OBSERVED, `EVD-YTM-SIG-006`]

### Signer Analysis
Shares the exact same ReVanced community builder certificate as YouTube ReVanced, Morphe Reddit, Google Photos, and Strava.

---

## 3. Manifest & Component Analysis

* **Package Renaming**: Renamed from `com.google.android.apps.youtube.music` to `app.revanced.android.apps.youtube.music`.
* **Permissions**:
  * Standard media streaming permissions: Internet, Access Network State, Foreground Service, Media Playback, WakeLock, Bluetooth audio routing.
  * No SMS, phone, camera, or accessibility service permissions requested [OBSERVED, `EVD-YTM-MAN-001`].
* **GmsCore Redirection**: Uses MicroG for non-root account authentication.

---

## 4. Code & Patch Provenance

### 4.1 Identified Patch Set
* **Patch Framework**: Official **ReVanced Patches** (`github.com/ReVanced/revanced-patches`) [OBSERVED, `EVD-YTM-PAT-001`].
* **CLI Version**: `6.0.0` [OBSERVED, `EVD-YTM-PAT-002`].
* **Patch Version**: `p6.2.0` [OBSERVED, `EVD-YTM-PAT-003`].
* **Git Commit**: `c6507b5051` [OBSERVED, `EVD-YTM-PAT-004`].

### 4.2 Added Classes
Injects 1,106 patch classes in `app.revanced.extension.music.*`:
* `app.revanced.extension.music.patches.AudioAdsPatch`: Removes audio ad interruptions.
* `app.revanced.extension.music.patches.BackgroundPlaybackPatch`: Unlocks continuous background playback without a paid YouTube Music Premium subscription.
* `app.revanced.extension.music.patches.ExclusiveAudioModePatch`: Unlocks the audio-only toggle to stream audio streams without downloading video frames, conserving cellular data.
* `app.revanced.extension.music.patches.MicroGSupportPatch`: MicroG account authentication routing.

### 4.3 Malicious Code Search
* No dynamic bytecode loading hooks or executable binaries found in `assets/music_filegroup/*.dat`.
* No unauthorized exfiltration or spyware routines detected.

---

## 5. Network Endpoints

* **Vendor Endpoints**: `music.youtube.com`, `*.googlevideo.com`, `youtubei.googleapis.com`.
* **Patch Endpoints**:
  * `https://api.revanced.app/v4` (ReVanced update and announcements).
  * `https://github.com/revanced/gmscore/releases/latest` (MicroG companion link).

---

## 6. Verdict & Findings

* **Verdict**: **Clean-as-patched**
* **Confidence**: High
* **Limitations**: Static Dalvik analysis only; backend audio stream availability is controlled by Google's API policies.
* **Summary**: Honest, standard community build produced by ReVanced CLI (`6.0.0`) using ReVanced patches `v6.2.0` (commit `c6507b5051`). Contains no malicious additions or hidden telemetry.

