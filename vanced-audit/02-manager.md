# Forensic Architectural Teardown: ReVanced Manager Plus

**Audit Reference**: `EVD-MGR-DOSSIER-001`  
**Security Classification**: Defensive Malware & Supply-Chain Assessment  
**Navigation**: [Root Repository README](../README.md) | [Audit Summary](00-summary.md) | [Hash Ledger](01-hashes.csv) | [Malware Report](05-malware-report.md) | [Patch Provenance](03-patches.md) | [Network Triage](04-network.md)

---

## 1. Executive Summary & Identity

| Parameter | Observed Technical Value | Epistemic Status & Evidence ID |
| :--- | :--- | :--- |
| **Application Label** | ReVanced Manager Plus (RV Manager +) | `[OBSERVED, EVD-MGR-ID-001]` |
| **Package Name** | `com.revanced.net.revancedmanager` | `[OBSERVED, EVD-MGR-ID-002]` |
| **Version Name** | `3.6.25` | `[OBSERVED, EVD-MGR-ID-003]` |
| **Version Code** | `385` | `[OBSERVED, EVD-MGR-ID-004]` |
| **Minimum Android SDK** | `API 26` (Android 8.0 Oreo) | `[OBSERVED, EVD-MGR-ID-005]` |
| **Target Android SDK** | `API 34` (Android 14) | `[OBSERVED, EVD-MGR-ID-006]` |
| **Binary File Name** | `vanced.to_revanced_manager_plus_v3.6.25.apk` | `[OBSERVED, EVD-MGR-BIN-001]` |
| **Binary File Size** | 2,477,424 bytes (~2.36 MiB) | `[OBSERVED, EVD-MGR-BIN-002]` |
| **SHA-256 Digest** | `f13df8fb954e096ec4f2d79718f8161f7b771b9094bc105600a7493837045a60` | `[OBSERVED, EVD-MGR-BIN-003]` |
| **SHA-1 Digest** | `070e2ce46747efc07d10e8cf80fa6805a9710be3` | `[OBSERVED, EVD-MGR-BIN-004]` |
| **MD5 Digest** | `e4347927d830cb44601079d5107047e0` | `[OBSERVED, EVD-MGR-BIN-005]` |
| **Audit Verdict** | **Suspicious** (High Supply-Chain Risk / Misleading Impersonation) | `[OBSERVED, EVD-MGR-VRD-001]` |

---

## 2. Signing Certificate Analysis & Impersonation Verification

Inspection of the APK Signing Block (magic string: `APK Sig Block 42`) revealed the package signing parameters:

* **Signature Schemes Active**:
  * **v1 JAR Scheme**: Not present.
  * **v2 Scheme (ID: `0x7109871a`)**: Active and cryptographically valid `[OBSERVED, EVD-MGR-SIG-001]`.
  * **Source Stamp Block (ID: `0x504b4453`)**: Present `[OBSERVED, EVD-MGR-SIG-002]`.
* **Certificate Subject Distinguished Name**:  
  `CN=revanced.net, OU=ReVanced, O=ReVanced, L=San Francisco, ST=California, C=US` `[OBSERVED, EVD-MGR-SIG-003]`.
* **Certificate Issuer Distinguished Name**:  
  `CN=revanced.net, OU=ReVanced, O=ReVanced, L=San Francisco, ST=California, C=US` `[OBSERVED, EVD-MGR-SIG-004]`.
* **Certificate Serial Number**: `6945915007771991341` `[OBSERVED, EVD-MGR-SIG-005]`.
* **Validity Horizon**: `2023-08-13T01:56:48Z` to `2050-12-29T01:56:48Z` `[OBSERVED, EVD-MGR-SIG-006]`.
* **Certificate Fingerprints**:
  * **SHA-256**: `74a62bdf5dc3105af7ac189364e4925d84a669ca21abe3bc709d61a3f48e1379` `[OBSERVED, EVD-MGR-SIG-007]`.
  * **SHA-1**: `099262933c3887e928bf64257a8e90d29b94249e` `[OBSERVED, EVD-MGR-SIG-008]`.
  * **MD5**: `842c556fe7bb1ff86ce3c32f44764ce8` `[OBSERVED, EVD-MGR-SIG-009]`.

### Discrepancy vs Authentic ReVanced Project
1. **Author Divergence**: The official ReVanced project distributes `ReVanced Manager` under the package name `app.revanced.manager.flutter`, developed in Flutter / Dart and signed by the ReVanced team's private release key.
2. **Third-Party Commercial Signer**: This utility is signed by `CN=revanced.net`. The domain `revanced.net` is a commercial mirror and search-engine scraper site that is **not affiliated** with the official ReVanced organisation (`https://revanced.app`).
3. **Misleading Nomenclature**: By calling itself "ReVanced Manager Plus", the application deceives end users into believing it is an official enhanced edition of the ReVanced patcher.

---

## 3. Decompiled Architecture: Absence of Patcher Capabilities

A complete decompilation pass using JADX `1.5.1` and Dalvik string indexing revealed that **ReVanced Manager Plus contains zero patching capability** `[OBSERVED, EVD-MGR-CODE-001]`:

* **Missing Patcher Libraries**: The binary contains no bytecode manipulation toolchains. There is no trace of `revanced-patcher`, `smali`, `baksmali`, `dexlib2`, `aapt2`, or APK alignment engines.
* **Inability to Patch Stock Apps**: The application cannot accept a clean stock APK from storage, decompile its DEX files, apply patches, and assemble a new package.
* **Actual Architectural Identity**: The binary is a lightweight native Kotlin client utilizing:
  * **Jetpack Compose** for user interface rendering.
  * **Retrofit 2 & OkHttp3** for HTTP communications.
  * **AndroidX WorkManager** (`DownloadWorker`, `InstallWorker`, `UpdateCheckWorker`) for background task orchestration.
  * **Room Persistence Library** for local application caching.

```
+-----------------------------------------------------------------------------------+
|               REVANCED MANAGER PLUS TRUE ARCHITECTURAL FLOW                       |
+-----------------------------------------------------------------------------------+
|                                                                                   |
|  [User Interface: Jetpack Compose]                                               |
|             │                                                                     |
|             ▼                                                                     |
|  [AppRepositoryImpl (zh.java)] ──GET──> https://vanced.to/apps-v3.json            |
|             │                           (Remote Prebuilt APK Catalogue)           |
|             ▼                                                                     |
|  [DownloadWorker.java]         ──GET──> https://github.com/vancedto/vanced.to/    |
|             │                           (Unverified Binary Sideload Payload)      |
|             ▼                                                                     |
|  [Verification: Length Only]   ──(j6 == j4)  NO SHA-256 CHECK                     |
|             │                                NO SIGNATURE VALIDATION              |
|             ▼                                                                     |
|  [InstallWorker.java]          ───────> Android PackageInstaller Session          |
|                                         REQUEST_INSTALL_PACKAGES                  |
|                                         UPDATE_PACKAGES_WITHOUT_USER_ACTION       |
+-----------------------------------------------------------------------------------+
```

---

## 4. Insecure Downloader Implementation & Integrity Bypass

Forensic analysis of `com.revanced.net.revancedmanager.data.manager.DownloadWorker` (lines 657–715) identified a critical vulnerability in binary verification:

```java
// Decompiled excerpt: com.revanced.net.revancedmanager.data.manager.DownloadWorker
Log.i("DownloadWorker", "Download completed: " + str8 + " -> " + absolutePath + 
      " bytes=" + j5 + " size=" + j6 + " expected=" + j4);

// Vulnerability: Length check only
if (j4 > 0 || j6 == j4) {
    file2.delete();
    if (((File) a01Var.g).renameTo(file2)) {
        // Renames temporary download file to final APK path
        // Passes directly to installation worker without SHA-256 comparison
        return new ad1("file_path", file2.getAbsolutePath());
    }
}
```

### Critical Security Defects:
1. **Zero Cryptographic Hash Check**: The application does **not** compare the SHA-256 digest of the downloaded APK against any catalogue value `[OBSERVED, EVD-MGR-SEC-001]`.
2. **Zero Signer Validation**: The utility does not verify that the downloaded APK was signed with an authentic community key (`CN=ReVanced`) `[OBSERVED, EVD-MGR-SEC-002]`.
3. **HTTP Content-Length Fallback**: The only validation executed is confirming that the downloaded byte count (`j6`) matches the HTTP `content-length` header (`j4`) `[OBSERVED, EVD-MGR-SEC-003]`.

---

## 5. Manifest Permissions & Exploitation Attack Surface

The application requests 12 permissions in its manifest, several of which possess high security sensitivity:

| Permission Name | Level | Requested Purpose | Supply-Chain Threat Assessment |
| :--- | :--- | :--- | :--- |
| `android.permission.REQUEST_INSTALL_PACKAGES` | High | Invokes Android package installation UI | Enables sideloading downloaded binaries |
| `android.permission.UPDATE_PACKAGES_WITHOUT_USER_ACTION` | High | Unattended updates on Android 12+ | Permits silent updates without user confirmation |
| `android.permission.REQUEST_DELETE_PACKAGES` | Medium | Uninstalls existing applications | Used to remove stock apps before sideloading |
| `android.permission.QUERY_ALL_PACKAGES` | High | Scans all installed packages | Inventories device software state |
| `android.permission.RECEIVE_BOOT_COMPLETED` | Medium | Spawns `UpdateCheckWorker` on boot | Establishes persistent background checking |
| `android.permission.FOREGROUND_SERVICE` | Medium | Persistent worker execution | Prevents process termination by Android OS |
| `android.permission.FOREGROUND_SERVICE_DATA_SYNC` | Medium | Background file download sync | Background worker status |
| `android.permission.INTERNET` | Normal | Network access | Fetches catalogue and downloads APKs |
| `android.permission.ACCESS_NETWORK_STATE` | Normal | Checks network state | Detects active connections |
| `android.permission.WAKE_LOCK` | Normal | Prevents CPU sleep during download | Ensures completion of large transfers |
| `android.permission.POST_NOTIFICATIONS` | Normal | Download progress alerts | Displays status bars |

---

## 6. Threat Scenario: Supply-Chain Weaponisation via Sideloading

The combination of ReVanced Manager Plus's architecture creates a critical supply-chain attack surface:

```
[Attacker Vector]
       │
       ▼
[Compromise vanced.to API or vancedto GitHub Releases]
       │
       ▼
[Inject Weaponised Trojaned APK into apps-v3.json]
       │
       ▼
[Client DownloadWorker fetches APK without SHA-256 check]
       │
       ▼
[Client invokes UPDATE_PACKAGES_WITHOUT_USER_ACTION]
       │
       ▼
[Silent Execution of Malicious Payload on Victim Device]
```

Because `DownloadWorker.java` performs no cryptographic integrity validation, any party capable of altering the JSON manifest on `vanced.to` or pushing a malicious release tag to `github.com/vancedto/vanced.to` could distribute weaponised malware (such as banking trojans or surveillance spyware) directly to all users of ReVanced Manager Plus.

---

## 7. MITRE ATT&CK for Mobile Mapping

| Technique ID | Technique Name | Context in ReVanced Manager Plus |
| :--- | :--- | :--- |
| **T1444** | Masquerading | Impersonates official ReVanced project branding and naming |
| **T1407** | Download New Code at Runtime | Downloads external APK binaries from remote endpoints |
| **T1476** | Supply Chain Compromise | Insecure distribution channel lacking cryptographic integrity validation |
| **T1538** | Application Sideloading | Requests elevated permissions to install arbitrary packages |
| **T1418** | Application Discovery | Requests `QUERY_ALL_PACKAGES` to inventory all installed software |

---

## 8. Network Infrastructure Ledger

| Endpoint | Protocol | Classification | Evidence ID |
| :--- | :--- | :--- | :--- |
| `https://vanced.to/` | HTTPS | Unofficial Catalog & Infrastructure | `EVD-MGR-NET-001` |
| `https://vanced.to/apps-v3.json` | HTTPS | Prebuilt APK Manifest Catalog | `EVD-MGR-NET-002` |
| `https://vanced.to/revanced-apps.json` | HTTPS | Fallback App Manifest Catalog | `EVD-MGR-NET-003` |
| `https://vanced.to/donate-redir` | HTTPS | Commercial / Donation Redirect | `EVD-MGR-NET-004` |
| `https://github.com/vancedto/vanced-manager-plus/` | HTTPS | Source Code Repository | `EVD-MGR-NET-005` |
| `https://github.com/vancedto/vanced.to/releases/` | HTTPS | APK Binary Payload Distribution | `EVD-MGR-NET-006` |

---

## 9. Verdict & Recommendations

* **Verdict**: **Suspicious** (High Supply-Chain Risk)
* **Confidence Rating**: High
* **Defensive Scope & Limits**: Bounded by static decompilation. Dynamic server-side API responses may change over time.
* **Remediation**: Users should immediately uninstall `com.revanced.net.revancedmanager` and transition to the authentic, open-source **ReVanced Manager** (`https://revanced.app`).

---

**Navigation**: [Root Repository README](../README.md) | [Audit Summary](00-summary.md) | [Hash Ledger](01-hashes.csv) | [Malware Report](05-malware-report.md) | [Patch Provenance](03-patches.md) | [Network Triage](04-network.md)
