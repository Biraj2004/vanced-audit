# Application Audit: Morphe Google Photos

## 1. Identity & File Attributes

| Attribute | Details | Evidence ID |
| :--- | :--- | :--- |
| **App Name** | Google Photos (Morphe / Rushii Patched) | `EVD-GPH-ID-001` |
| **Package Name** | `app.morphe.android.apps.photos` | `EVD-GPH-ID-002` |
| **Original Package** | `com.google.android.apps.photos` | `EVD-GPH-ID-003` |
| **Version Name** | `7.98.0.1001971222` | `EVD-GPH-ID-004` |
| **Version Code** | `52589059` | `EVD-GPH-ID-005` |
| **File Size** | 134,247,409 bytes (~128 MiB) | `EVD-GPH-BIN-001` |
| **SHA-256** | `be9bb732f7c3f014299769a66b4f70730d08a2c310dd8f517695a92cbd4939f4` | `EVD-GPH-BIN-002` |
| **SHA-1** | `c907af2e75189947cfa3bdd4ac2fad93e932712e` | `EVD-GPH-BIN-003` |
| **MD5** | `695e3543d131724c307811aedbabeec9` | `EVD-GPH-BIN-004` |
| **Target Architecture** | `arm64-v8a` | `EVD-GPH-BIN-005` |

---

## 2. Signing & Certificate Verification

* **Signing Scheme**: APK Signature Scheme v2 (Block ID `0x7109871a`) [OBSERVED, `EVD-GPH-SIG-001`]
* **Certificate Subject**: `CN=ReVanced` [OBSERVED, `EVD-GPH-SIG-002`]
* **Certificate Issuer**: `CN=ReVanced` [OBSERVED, `EVD-GPH-SIG-003`]
* **Certificate Serial**: `8194446765327472407` [OBSERVED, `EVD-GPH-SIG-004`]
* **Validity Period**: `2023-07-14T04:24:59Z` to `2053-09-04T04:24:59Z` [OBSERVED, `EVD-GPH-SIG-005`]
* **SHA-256 Fingerprint**: `5d87c9bdc8e368dcf80971f33455db4c2e7dd754e1f288f1162c04b2a4811708` [OBSERVED, `EVD-GPH-SIG-006`]

### Signer Analysis
The APK is signed with the default test keystore bundled within the open-source **ReVanced CLI** toolchain. This confirms the package was recompiled using ReVanced CLI rather than an unknown corporate or individual key. It differs predictably from the official Google Play Store signature (`Google Inc.`), which is unavoidable when modifying APK bytecode.

---

## 3. Manifest & Component Analysis

* **Package Renaming**: Renamed from `com.google.android.apps.photos` to `app.morphe.android.apps.photos`. This allows parallel installation alongside the original Google Photos application without signature collision.
* **Permissions**: Contains standard Google Photos permissions:
  * Camera, External Storage (`READ_MEDIA_IMAGES`, `READ_MEDIA_VIDEO`), Location (`ACCESS_FINE_LOCATION`, `ACCESS_COARSE_LOCATION`), Internet, and WakeLock.
  * No unusual SMS, telephony, accessibility service, or overlay permissions were added [OBSERVED, `EVD-GPH-MAN-001`].
* **Added Components**:
  * Added `InternalDataDocumentsProvider` (`app.template.extension.extension.InternalDataDocumentsProvider`) to manage sandboxed storage isolation for unrooted devices.

---

## 4. Code & Patch Provenance

### 4.1 Identified Patch Set
* **Toolchain / Framework**: Rushii / Morphe extension framework (`RushiiMachine` fork of ReVanced / Morphe) [INFERRED, `EVD-GPH-PAT-001`].
* **CLI Version**: `1.20.0.5` [OBSERVED in filename metadata, `EVD-GPH-PAT-002`].
* **Patch Version**: `p1.23.1` [OBSERVED in filename metadata, `EVD-GPH-PAT-003`].

### 4.2 Added Classes
Patch logic is cleanly isolated in `classes11.dex` under the package `app.template.extension.extension.*`:
* `PixelSpoofHelper`: Implements device fingerprint spoofing (emulating a Google Pixel device profile) to enable Google Photos original quality unlimited storage tier.
* `PhotosDcimFolderPatch`: Redirects screenshot and image directories to prevent automatic camera folder mixing.
* `GmsCoreSupportPatch`: Replaces Google Play Services authentication calls with MicroG / GmsCore hooks.
* `SignatureHookApp`: Bypasses client-side certificate integrity verification within Google Photos.

### 4.3 Malicious Code Search
* Dynamic code loading (`DexClassLoader`): Present only inside Google Play Core and original Google Photos split loading libraries; absent in the patch extension package.
* Network exfiltration: No exfiltration endpoints, webhooks, or remote loggers observed.
* Binary assets: All 29 `.bin` files in `assets/` (e.g. `awb_table_*.bin`, `viewer_*.bin`) are native Auto White Balance colour-calibration tables and VR viewport models belonging to the authentic Google Photos package.

---

## 5. Network Endpoints

* **Expected Vendor**: `*.google.com`, `*.googleapis.com`, `*.gstatic.com`, `*.googleusercontent.com` [OBSERVED].
* **Patch Endpoints**: None (Google Photos patches operate purely offline via device property spoofing).
* **Analytics**: Standard Google Firebase and Crashlytics libraries inherited from the original APK.

---

## 6. Verdict & Findings

* **Verdict**: **Clean-as-patched**
* **Confidence**: High
* **Limitations**: Analysis bounded to static Dalvik bytecode and native library inventory. Does not establish runtime safety on modified Google accounts.
* **Summary**: All modifications are fully explained by the known Rushii / Morphe Google Photos patch bundle (`p1.23.1`). No spyware, Trojans, or unauthorized telemetry found within the bounded static analysis.

