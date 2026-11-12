# Application Audit: Strava

## 1. Identity & File Attributes

| Attribute | Details | Evidence ID |
| :--- | :--- | :--- |
| **App Name** | Strava (Rushii Patched) | `EVD-STR-ID-001` |
| **Package Name** | `com.strava` | `EVD-STR-ID-002` |
| **Original Package** | `com.strava` | `EVD-STR-ID-003` |
| **Version Name** | `480.15` | `EVD-STR-ID-004` |
| **Version Code** | `12399564` | `EVD-STR-ID-005` |
| **File Size** | 122,829,417 bytes (~117 MiB) | `EVD-STR-BIN-001` |
| **SHA-256** | `60b3e22f226067d473adb032d83972750112ccdcb5b95bc0efe603b6650f14d8` | `EVD-STR-BIN-002` |
| **SHA-1** | `0242c8fc49823934d584e624e77b3303dc495c8e` | `EVD-STR-BIN-003` |
| **MD5** | `dd9f9e3a0a7a5cd0cc7c3388d85712a4` | `EVD-STR-BIN-004` |
| **Target Architecture** | `arm64-v8a` | `EVD-STR-BIN-005` |

---

## 2. Signing & Certificate Verification

* **Signing Scheme**: APK Signature Scheme v2 (Block ID `0x7109871a`) [OBSERVED, `EVD-STR-SIG-001`]
* **Certificate Subject**: `CN=ReVanced` [OBSERVED, `EVD-STR-SIG-002`]
* **Certificate Issuer**: `CN=ReVanced` [OBSERVED, `EVD-STR-SIG-003`]
* **Certificate Serial**: `8194446765327472407` [OBSERVED, `EVD-STR-SIG-004`]
* **Validity Period**: `2023-07-14T04:24:59Z` to `2053-09-04T04:24:59Z` [OBSERVED, `EVD-STR-SIG-005`]
* **SHA-256 Fingerprint**: `5d87c9bdc8e368dcf80971f33455db4c2e7dd754e1f288f1162c04b2a4811708` [OBSERVED, `EVD-STR-SIG-006`]

### Signer Analysis
Signed with the standard ReVanced community test key. Replaces Strava's official Google Play release key.

---

## 3. Manifest & Component Analysis

* **Permissions**: Identical to stock Strava:
  * Location (`ACCESS_FINE_LOCATION`, `ACCESS_COARSE_LOCATION`, `ACCESS_BACKGROUND_LOCATION`) - fundamental to GPS workout recording.
  * Physical Activity (`ACTIVITY_RECOGNITION`), Bluetooth (`BLUETOOTH_CONNECT`, `BLUETOOTH_SCAN`) for heart-rate monitors, and Camera for photo uploads.
  * No added or elevated permissions beyond standard workout recording requirements [OBSERVED, `EVD-STR-MAN-001`].
* **Components**: No new exported services, receivers, or content providers added.

---

## 4. Code & Patch Provenance

### 4.1 Identified Patch Set
* **Patch Toolchain**: Rushii (`RushiiMachine`) CLI `1.20.0.5`, Patches `p1.23.1.1` [OBSERVED in filename metadata, `EVD-STR-PAT-001`].

### 4.2 Structural Modification
Forensic examination of archive members identified that the patch was injected via a dedicated supplemental DEX file:
* **`classes9.dex`** (size: 13,804 bytes) [OBSERVED, `EVD-STR-DEX-001`].

Decompilation of `classes9.dex` revealed exactly two classes [OBSERVED, `EVD-STR-CODE-001`]:
1. `com.strava.subscriptions.data.models.SubscriptionDetail`:
   * Overrides `isPremium()`, `getProductAccess()`, `getSubscriptionState()`, and `getDaysRemainingInSubscription()`.
   * Forces the application's local licensing logic to evaluate user status as an active subscriber (`isPremium = true`).
2. `com.strava.authorization.data.RequestOtpLogInNetworkResponse`:
   * Minimal data class supporting authentication flow response mapping.

### 4.3 Malicious Code Search
* No dynamic code loading (`DexClassLoader`, `InMemoryDexClassLoader`) was added.
* No GPS coordinate exfiltration or unauthorized location monitoring hooks exist outside Strava's original tracking services.
* No telemetry endpoints other than Strava's official APIs and Sentry crash reporting were found.

---

## 5. Network Endpoints

* **Vendor Endpoints**:
  * `https://www.strava.com/api/v3/`
  * `https://analytics.strava.com`
  * `https://api.mapbox.com` (Map rendering and routing)
* **Crash Reporting**: `https://*.sentry.io` (Inherited from original Strava package).
* **Patch Framework Endpoints**: None (The patch is entirely self-contained and local).

---

## 6. Verdict & Findings

* **Verdict**: **Clean-as-patched** (Commercial Subscription Bypass Modification)
* **Confidence**: High
* **Limitations**: Static Dalvik bytecode analysis only. Server-side Strava features (e.g. server-processed segment leaderboards, beacon live tracking) that enforce server validation remain governed by Strava's backend.
* **Summary**: This APK represents a minimal, focused client-side subscription entitlement unlock. It contains no malware, backdoors, location loggers, or suspicious network infrastructure.

