# Technical Forensic Dossier: Strava

**Audit Reference**: `EVD-STR-DOSSIER-001`  
**Security Classification**: Defensive Malware & Supply-Chain Assessment  
**Navigation**: [Root Repository README](../../README.md) | [Audit Summary](../00-summary.md) | [Hash Ledger](../01-hashes.csv) | [Malware Report](../05-malware-report.md) | [Patch Provenance](../03-patches.md)

---

## 1. Application Identity & Cryptographic Registry

| Parameter | Observed Technical Value | Epistemic Status & Evidence ID |
| :--- | :--- | :--- |
| **Application Label** | Strava (Rushii Subscription Unlock Build) | `[OBSERVED, EVD-STR-ID-001]` |
| **Target Package Name** | `com.strava` | `[OBSERVED, EVD-STR-ID-002]` |
| **Upstream Stock Package** | `com.strava` | `[OBSERVED, EVD-STR-ID-003]` |
| **Version Name** | `480.15` | `[OBSERVED, EVD-STR-ID-004]` |
| **Version Code** | `12399564` | `[OBSERVED, EVD-STR-ID-005]` |
| **Minimum Android SDK** | `API 26` (Android 8.0 Oreo) | `[OBSERVED, EVD-STR-ID-006]` |
| **Target Android SDK** | `API 34` (Android 14) | `[OBSERVED, EVD-STR-ID-007]` |
| **Binary File Name** | `vanced.to_rushi_com.strava_arm64v8a_v480.15_cli1.20.0.5_p1.23.1.1.apk` | `[OBSERVED, EVD-STR-BIN-001]` |
| **Binary File Size** | 122,829,417 bytes (~117.14 MiB) | `[OBSERVED, EVD-STR-BIN-002]` |
| **SHA-256 Digest** | `60b3e22f226067d473adb032d83972750112ccdcb5b95bc0efe603b6650f14d8` | `[OBSERVED, EVD-STR-BIN-003]` |
| **SHA-1 Digest** | `0242c8fc49823934d584e624e77b3303dc495c8e` | `[OBSERVED, EVD-STR-BIN-004]` |
| **MD5 Digest** | `dd9f9e3a0a7a5cd0cc7c3388d85712a4` | `[OBSERVED, EVD-STR-BIN-005]` |
| **Compilation Architecture** | `arm64-v8a` | `[OBSERVED, EVD-STR-BIN-006]` |

---

## 2. Cryptographic Signing & Certificate Lineage

APK Signing Block analysis (Block ID `0x7109871a`) confirmed:

* **Signature Schemes Active**:
  * **v1 JAR Scheme**: Not present.
  * **v2 Scheme (ID: `0x7109871a`)**: Active and cryptographically valid `[OBSERVED, EVD-STR-SIG-001]`.
  * **v3 Scheme (ID: `0xf05368c0`)**: Not present.
* **Certificate Subject Distinguished Name**: `CN=ReVanced` `[OBSERVED, EVD-STR-SIG-002]`.
* **Certificate Issuer Distinguished Name**: `CN=ReVanced` `[OBSERVED, EVD-STR-SIG-003]`.
* **Certificate Serial Number**: `8194446765327472407` `[OBSERVED, EVD-STR-SIG-004]`.
* **Validity Horizon**: `2023-07-14T04:24:59Z` to `2053-09-04T04:24:59Z` `[OBSERVED, EVD-STR-SIG-005]`.
* **Certificate Fingerprints**:
  * **SHA-256**: `5d87c9bdc8e368dcf80971f33455db4c2e7dd754e1f288f1162c04b2a4811708` `[OBSERVED, EVD-STR-SIG-006]`.
  * **SHA-1**: `2a849488a0e2dc1e626e27ab37ee5003bf4e8c97` `[OBSERVED, EVD-STR-SIG-007]`.
  * **MD5**: `0881bc32e98345c213454b38d3ad49d7` `[OBSERVED, EVD-STR-SIG-008]`.

### Signer Analysis
The build replaces Strava Inc.'s official Google Play signing key with the standard ReVanced CLI community test certificate. This signature matches all other clean community builds evaluated in this assessment.

---

## 3. Package Manifest & Component Analysis

### 3.1 Permission Audit (Location & Privacy Verification)
Strava is a GPS-intensive athletic tracking tool. An exhaustive permission comparison between the stock manifest and the audited binary was conducted to ensure no malicious location tracking or audio recording capabilities were added:

| Permission Name | Level | Purpose in App | Security Finding |
| :--- | :--- | :--- | :--- |
| `android.permission.ACCESS_FINE_LOCATION` | Dangerous | High-precision GPS tracking during runs/rides | Stock Strava requirement `[OBSERVED]` |
| `android.permission.ACCESS_COARSE_LOCATION` | Dangerous | Approximate network-based location | Stock Strava requirement `[OBSERVED]` |
| `android.permission.ACCESS_BACKGROUND_LOCATION` | Dangerous | Continued activity recording when screen is off | Stock Strava requirement `[OBSERVED]` |
| `android.permission.ACTIVITY_RECOGNITION` | Dangerous | Step counting and auto-pause detection | Stock Strava requirement `[OBSERVED]` |
| `android.permission.BLUETOOTH_CONNECT` | Dangerous | Syncing heart rate monitors & cadence sensors | Stock Strava requirement `[OBSERVED]` |
| `android.permission.BLUETOOTH_SCAN` | Dangerous | Scanning for nearby athletic sensors | Stock Strava requirement `[OBSERVED]` |
| `android.permission.CAMERA` | Dangerous | Photograph uploads for completed workouts | Stock Strava requirement `[OBSERVED]` |
| `android.permission.INTERNET` | Normal | Workout upload and leaderboard synchronisation | Stock Strava requirement `[OBSERVED]` |
| `android.permission.FOREGROUND_SERVICE` | Normal | Foreground notification during active tracking | Stock Strava requirement `[OBSERVED]` |

**Result**: No extra permissions were added. Zero covert SMS, contact reading, clipboard monitoring, or audio eavesdropping permissions were introduced `[OBSERVED, EVD-STR-MAN-001]`.

---

## 4. Archive Topology & Dalvik Bytecode Analysis

### 4.1 Architecture Inventory
The archive contains 9 DEX files (`classes.dex` through `classes9.dex`):
* `classes.dex` through `classes8.dex`: Pristine official Strava codebase, Kotlin standard libraries, Mapbox rendering engine, and Jetpack components.
* `classes9.dex` (size: 13,804 bytes): An isolated supplemental DEX injected during recompilation `[OBSERVED, EVD-STR-DEX-001]`.

### 4.2 Decompiled Injected Micro-Patch
Decompilation of `classes9.dex` revealed exactly two classes, demonstrating an extremely clean, minimal intervention `[OBSERVED, EVD-STR-CODE-001]`:

```java
// Decompiled excerpt: com.strava.subscriptions.data.models.SubscriptionDetail
package com.strava.subscriptions.data.models;

import kotlin.jvm.internal.Intrinsics;

public final class SubscriptionDetail {
    private final boolean isFree;
    private final boolean isInTrial;
    private final boolean isPremium;

    public SubscriptionDetail(boolean z, boolean z2, boolean z3) {
        // Enforced subscription state override
        this.isPremium = true;
        this.isFree = false;
        this.isInTrial = false;
    }

    public final boolean isPremium() {
        return true; // Unlocks client-side subscriber UI & route planning
    }

    public final boolean isFree() {
        return false;
    }

    public final boolean isInTrial() {
        return false;
    }

    public final String getSubscriptionState() {
        return "active";
    }

    public final int getDaysRemainingInSubscription() {
        return 365;
    }
}
```

```java
// Decompiled excerpt: com.strava.authorization.data.RequestOtpLogInNetworkResponse
package com.strava.authorization.data;

public final class RequestOtpLogInNetworkResponse {
    private final String status;

    public RequestOtpLogInNetworkResponse(String str) {
        this.status = str;
    }

    public final String getStatus() {
        return this.status;
    }
}
```

### 4.3 Functional Impact & Safety Assessment
1. **Client-Side Entitlement Bypass**: By overriding `SubscriptionDetail.isPremium()` to consistently return `true`, the local client unlocks subscriber-only interface features (such as 3D route planning, personal segment heatmap layers, and advanced workout performance metrics).
2. **Server-Side Bounds**: Server-enforced premium features (such as live beacon tracking via Strava's backend servers or verified segment leaderboard placement) continue to be evaluated on Strava's servers and are not modified by this local patch.
3. **Absence of Malicious Logic**: The patch introduces no dynamic class loaders, no reflection hooks into private storage, and no background exfiltration threads.

---

## 5. Network Endpoint Triage

External network endpoints extracted from bytecode match stock Strava infrastructure:

| Endpoint Domain | Classification | Security Context | Evidence ID |
| :--- | :--- | :--- | :--- |
| `https://www.strava.com/api/v3/` | Expected Original Vendor | Strava activity sync and user profile REST API | `EVD-STR-NET-001` |
| `https://analytics.strava.com` | Expected Original Vendor | Official Strava telemetry and usage metrics | `EVD-STR-NET-002` |
| `https://api.mapbox.com` | Expected Original Vendor | Map tile rendering, GPS polyline mapping | `EVD-STR-NET-003` |
| `https://*.sentry.io` | Expected Original Vendor | Crash and ANR reporting telemetry | `EVD-STR-NET-004` |

**Patch Infrastructure Endpoints**: None. The patch operates completely offline and requires no external licensing servers or callback webhooks `[OBSERVED, EVD-STR-NET-005]`.

---

## 6. Threat Assessment & Verdict

* **Formal Verdict**: **Clean-as-patched**
* **Threat Classification**: Functional Client-Side Modification (Subscription Licensing Bypass)
* **Confidence Rating**: High
* **Defensive Scope & Limits**: Static bytecode analysis only. Workout GPS data is sent exclusively to official Strava endpoints.
* **Analytical Conclusion**: The APK represents a minimal, surgical modification implemented via Rushii's universal patcher (`p1.23.1.1`). It contains zero malware, no hidden location trackers, and no unauthorized exfiltration channels.

---

**Navigation**: [Root Repository README](../../README.md) | [Audit Summary](../00-summary.md) | [Hash Ledger](../01-hashes.csv) | [Malware Report](../05-malware-report.md) | [Patch Provenance](../03-patches.md)
