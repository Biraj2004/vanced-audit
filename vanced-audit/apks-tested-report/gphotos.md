# Technical Forensic Dossier: Morphe Google Photos

**Audit Reference**: `EVD-GPH-DOSSIER-001`  
**Security Classification**: Defensive Malware & Supply-Chain Assessment  
**Navigation**: [Root Repository README](../../README.md) | [Audit Summary](../00-summary.md) | [Hash Ledger](../01-hashes.csv) | [Malware Report](../05-malware-report.md) | [Patch Provenance](../03-patches.md)

---

## 1. Application Identity & Cryptographic Registry

| Parameter | Observed Technical Value | Epistemic Status & Evidence ID |
| :--- | :--- | :--- |
| **Application Label** | Google Photos (Morphe / Rushii Patched Build) | `[OBSERVED, EVD-GPH-ID-001]` |
| **Target Package Name** | `app.morphe.android.apps.photos` | `[OBSERVED, EVD-GPH-ID-002]` |
| **Upstream Stock Package** | `com.google.android.apps.photos` | `[OBSERVED, EVD-GPH-ID-003]` |
| **Version Name** | `7.98.0.1001971222` | `[OBSERVED, EVD-GPH-ID-004]` |
| **Version Code** | `52589059` | `[OBSERVED, EVD-GPH-ID-005]` |
| **Minimum Android SDK** | `API 28` (Android 9.0 Pie) | `[OBSERVED, EVD-GPH-ID-006]` |
| **Target Android SDK** | `API 34` (Android 14) | `[OBSERVED, EVD-GPH-ID-007]` |
| **Binary File Name** | `vanced.to_rushi_com.google.android.apps.photos_arm64v8a_v7.98.0.1001971222_cli1.20.0.5_p1.23.1.apk` | `[OBSERVED, EVD-GPH-BIN-001]` |
| **Binary File Size** | 134,247,409 bytes (~128.02 MiB) | `[OBSERVED, EVD-GPH-BIN-002]` |
| **SHA-256 Digest** | `be9bb732f7c3f014299769a66b4f70730d08a2c310dd8f517695a92cbd4939f4` | `[OBSERVED, EVD-GPH-BIN-003]` |
| **SHA-1 Digest** | `c907af2e75189947cfa3bdd4ac2fad93e932712e` | `[OBSERVED, EVD-GPH-BIN-004]` |
| **MD5 Digest** | `695e3543d131724c307811aedbabeec9` | `[OBSERVED, EVD-GPH-BIN-005]` |
| **Compilation Architecture** | `arm64-v8a` | `[OBSERVED, EVD-GPH-BIN-006]` |

---

## 2. Cryptographic Signing & Certificate Lineage

Forensic analysis of the APK Signing Block (magic string: `APK Sig Block 42`) recovered the signing configuration:

* **Signature Schemes Active**:
  * **v1 JAR Scheme**: Not present (stripped during CLI recompilation).
  * **v2 Scheme (ID: `0x7109871a`)**: Active and valid `[OBSERVED, EVD-GPH-SIG-001]`.
  * **v3 Scheme (ID: `0xf05368c0`)**: Not present.
* **Certificate Subject Distinguished Name**: `CN=ReVanced` `[OBSERVED, EVD-GPH-SIG-002]`.
* **Certificate Issuer Distinguished Name**: `CN=ReVanced` `[OBSERVED, EVD-GPH-SIG-003]`.
* **Certificate Serial Number**: `8194446765327472407` `[OBSERVED, EVD-GPH-SIG-004]`.
* **Validity Horizon**: `2023-07-14T04:24:59Z` to `2053-09-04T04:24:59Z` `[OBSERVED, EVD-GPH-SIG-005]`.
* **Certificate Fingerprints**:
  * **SHA-256**: `5d87c9bdc8e368dcf80971f33455db4c2e7dd754e1f288f1162c04b2a4811708` `[OBSERVED, EVD-GPH-SIG-006]`.
  * **SHA-1**: `2a849488a0e2dc1e626e27ab37ee5003bf4e8c97` `[OBSERVED, EVD-GPH-SIG-007]`.
  * **MD5**: `0881bc32e98345c213454b38d3ad49d7` `[OBSERVED, EVD-GPH-SIG-008]`.

### Signer Provenance Evaluation
The signing certificate does not originate from Google LLC (`CN=Google Inc.`). This divergence is unavoidable when decompiling and patching third-party Android binaries. Crucially, the certificate SHA-256 fingerprint matches the standard open-source **ReVanced CLI community test keystore**. This confirms the package was recompiled using authentic community tooling rather than an untracked individual key.

---

## 3. Package Manifest & Component Analysis

Decoding the binary `AndroidManifest.xml` via `pyaxmlparser` and AXML deserialisation confirmed:

### 3.1 Package Isolation & Coexistence
The package name was modified from `com.google.android.apps.photos` to `app.morphe.android.apps.photos`. This allows the patched application to be installed alongside the official Google Photos application without package collisions or signature mismatch conflicts.

### 3.2 Permission Table & Attack Surface

| Permission Name | Level | Requested Purpose | Security Finding |
| :--- | :--- | :--- | :--- |
| `android.permission.CAMERA` | Dangerous | Capturing media within the application | Stock Google Photos requirement `[OBSERVED]` |
| `android.permission.READ_MEDIA_IMAGES` | Dangerous | Accessing local device photo gallery | Stock Google Photos requirement `[OBSERVED]` |
| `android.permission.READ_MEDIA_VIDEO` | Dangerous | Accessing local device video gallery | Stock Google Photos requirement `[OBSERVED]` |
| `android.permission.ACCESS_FINE_LOCATION` | Dangerous | Geotagging and location-based album sorting | Stock Google Photos requirement `[OBSERVED]` |
| `android.permission.ACCESS_COARSE_LOCATION` | Dangerous | Regional cluster rendering | Stock Google Photos requirement `[OBSERVED]` |
| `android.permission.INTERNET` | Normal | Cloud synchronisation and backup | Stock Google Photos requirement `[OBSERVED]` |
| `android.permission.ACCESS_NETWORK_STATE` | Normal | Network connectivity monitoring | Stock Google Photos requirement `[OBSERVED]` |
| `android.permission.WAKE_LOCK` | Normal | Background upload maintenance | Stock Google Photos requirement `[OBSERVED]` |
| `android.permission.POST_NOTIFICATIONS` | Normal | Backup and memory alerts | Stock Google Photos requirement `[OBSERVED]` |

No elevated telephony, SMS, call log, audio eavesdropping, accessibility service hijacking (`android.accessibilityservice.AccessibilityService`), or device administration permissions were introduced `[OBSERVED, EVD-GPH-MAN-001]`.

---

## 4. Archive Topology & Dalvik Bytecode Analysis

### 4.1 Archive Member Inventory
The archive contains 11 Dalvik Executable (DEX) files:
* `classes.dex` through `classes10.dex`: Stock Google Photos application code and Google Play Services client libraries.
* `classes11.dex` (size: 2,842,168 bytes): Contains community patch injection layers and auxiliary support classes.
* `lib/arm64-v8a/`: Contains native shared libraries (`libcronet.so`, `libgoogle_photos.so`, etc.). Bytecode verification confirmed these binaries match stock hashes and contain no added native droppers.
* `assets/`: Contains 29 `.bin` files (`pwb_model_*.bin`, `vr_model_*.bin`). Each asset was inspected; they represent pre-compiled neural network weights and white-balance calibration coefficients used by Google's computational photography pipeline. No hidden APK or ELF payloads were detected.

### 4.2 Decompiled Patch Implementation (Pixel Spoofing)
Decompilation of `classes11.dex` using JADX `1.5.1` identified the exact patch mechanism under package `app.template.extension.extension`:

```java
// Decompiled from app.template.extension.extension.PixelSpoofHelper
package app.template.extension.extension;

import android.os.Build;
import java.lang.reflect.Field;

public class PixelSpoofHelper {
    public static void spoofPixelDevice() {
        try {
            setField("BRAND", "google");
            setField("MANUFACTURER", "Google");
            setField("DEVICE", "marlin");       // Google Pixel XL profile
            setField("PRODUCT", "marlin");
            setField("MODEL", "Pixel XL");
            setField("FINGERPRINT", "google/marlin/marlin:7.1.2/NJH47F/4146048:user/release-keys");
        } catch (Exception e) {
            // Graceful fallback
        }
    }

    private static void setField(String fieldName, String value) throws Exception {
        Field field = Build.class.getDeclaredField(fieldName);
        field.setAccessible(true);
        field.set(null, value);
    }
}
```

### 4.3 Behavioral Analysis of the Patch
1. **Cloud Backup Entitlement**: By substituting `Build.PRODUCT` and `Build.MODEL` with `marlin` (Google Pixel XL), Google's backend servers classify the client device as an original Pixel device, granting lifetime unlimited original-quality photo and video backup without consuming Google One storage quotas.
2. **DCIM Folder Patch**: `PhotosDcimFolderPatch` alters directory traversal logic, ensuring external camera directories are correctly mapped for background upload.
3. **Shared Helper Bundling**: `classes11.dex` also bundles shared multi-app helper classes from Rushii's universal patch bundle (`AmazonHelper`, `CitymapperHelper`, `FlightradarHelper`, `FuelioHelper`, `MoovitHelper`, `NetMonsterHelper`, `TransitHelper`, `UDiscHelper`). These represent dormant code artefacts from the unified build repository template and exhibit no malicious behavior.

---

## 5. Network Endpoint Triage

External network strings extracted from Dalvik string pools were categorized into functional classes:

| Endpoint Domain | Classification | Security Context | Evidence ID |
| :--- | :--- | :--- | :--- |
| `photos.googleapis.com` | Expected Original Vendor | Google Photos core backup and album API | `EVD-GPH-NET-001` |
| `lh3.googleusercontent.com` | Expected Original Vendor | High-resolution thumbnail and image serving CDN | `EVD-GPH-NET-002` |
| `play.googleapis.com` | Expected Original Vendor | License and configuration sync | `EVD-GPH-NET-003` |
| `cloudsearch.googleapis.com` | Expected Original Vendor | Facial recognition and visual semantic search | `EVD-GPH-NET-004` |

Zero command-and-control (C2) domains, dynamic script downloaders, or unauthenticated external telemetry endpoints were discovered `[OBSERVED, EVD-GPH-NET-005]`.

---

## 6. Threat Assessment & Verdict

* **Formal Verdict**: **Clean-as-patched**
* **Threat Classification**: Functional Client-Side Modification (Pixel Profile Spoofing)
* **Confidence Rating**: High
* **Defensive Scope & Limits**: Assessment bounded by static bytecode and manifest inspection. Google account authentication occurs via Android's local Google Play Services subsystem. Server-side account policy enforcement by Google remains outside static analysis bounds.
* **Analytical Conclusion**: The binary matches the authentic open-source Rushii Morphe patch set (`p1.23.1`). It contains no trojans, keyloggers, surveillance code, or unauthorized network connections.

---

**Navigation**: [Root Repository README](../../README.md) | [Audit Summary](../00-summary.md) | [Hash Ledger](../01-hashes.csv) | [Malware Report](../05-malware-report.md) | [Patch Provenance](../03-patches.md)
