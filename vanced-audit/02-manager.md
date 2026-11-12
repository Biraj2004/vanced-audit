# ReVanced Manager Plus Analysis

## 1. Executive Summary & Identity

| Attribute | Details | Evidence ID |
| :--- | :--- | :--- |
| **Application Name** | ReVanced Manager Plus (RV Manager +) | `EVD-MGR-ID-001` |
| **Package Name** | `com.revanced.net.revancedmanager` | `EVD-MGR-ID-002` |
| **Version Name** | `3.6.25` | `EVD-MGR-ID-003` |
| **Version Code** | `385` | `EVD-MGR-ID-004` |
| **File Size** | 2,477,424 bytes (~2.36 MiB) | `EVD-MGR-BIN-001` |
| **SHA-256** | `f13df8fb954e096ec4f2d79718f8161f7b771b9094bc105600a7493837045a60` | `EVD-MGR-BIN-002` |
| **SHA-1** | `070e2ce46747efc07d10e8cf80fa6805a9710be3` | `EVD-MGR-BIN-003` |
| **MD5** | `e4347927d830cb44601079d5107047e0` | `EVD-MGR-BIN-004` |
| **Verdict** | **Suspicious** (High Supply-Chain Risk / Misleading Impersonation) | `EVD-MGR-VRD-001` |

---

## 2. Signing Certificate Analysis

`ReVanced Manager Plus` is signed exclusively with the **APK Signature Scheme v2** (along with an embedded Source Stamp block `0x504b4453`). It possesses no v1 JAR signature.

* **Subject / Issuer**: `CN=revanced.net, OU=ReVanced, O=ReVanced, L=San Francisco, ST=California, C=US` [OBSERVED, `EVD-MGR-SIG-001`]
* **Serial Number**: `6945915007771991341` [OBSERVED, `EVD-MGR-SIG-002`]
* **Validity Period**: `2023-08-13T01:56:48Z` to `2050-12-29T01:56:48Z` [OBSERVED, `EVD-MGR-SIG-003`]
* **Certificate SHA-256**: `74a62bdf5dc3105af7ac189364e4925d84a669ca21abe3bc709d61a3f48e1379` [OBSERVED, `EVD-MGR-SIG-004`]
* **Certificate SHA-1**: `099262933c3887e928bf64257a8e90d29b94249e` [OBSERVED, `EVD-MGR-SIG-005`]
* **Certificate MD5**: `842c556fe7bb1ff86ce3c32f44764ce8` [OBSERVED, `EVD-MGR-SIG-006`]

### Discrepancy vs Official ReVanced
* The official ReVanced project distributes `ReVanced Manager` under the package name `app.revanced.manager.flutter`, built using the Flutter framework, and signed by official ReVanced release keys.
* This application uses package name `com.revanced.net.revancedmanager` and is signed with a certificate tied explicitly to the domain `revanced.net`, an unofficial mirror and commercial distributor unaffiliated with the ReVanced organisation (`revanced.app`).

---

## 3. Architecture & Functional Analysis

### 3.1 Patcher Absence
Static decompilation via JADX (`1.5.1`) revealed that **ReVanced Manager Plus contains zero patching capability** [OBSERVED, `EVD-MGR-CODE-001`]:
* Absence of `revanced-patcher`, `smali`, `baksmali`, `dexlib2`, or `aapt2`.
* The package does not accept target APKs or modify bytecode locally.
* Instead of being a patch orchestrator, it is a lightweight Kotlin application built using **Jetpack Compose**, **OkHttp3**, **Retrofit**, and **AndroidX WorkManager**.

### 3.2 True Function: Prebuilt APK Downloader & Installer
The application functions entirely as an uncurated third-party application store client:
1. **Catalog Retrieval**: `AppRepositoryImpl` (`zh.java`) queries Retrofit interface `p82.java`:
   * `GET https://vanced.to/apps-v3.json`
   * `GET https://vanced.to/revanced-apps.json`
   * `GET https://vanced.to/revanced-apps-arm64-v8a.json`
2. **Download Execution**: When a user selects an application, `DownloadWorker` (`com.revanced.net.revancedmanager.data.manager.DownloadWorker`) is scheduled via WorkManager.
3. **Storage Target**: Files are downloaded directly from the GitHub releases of repository `vancedto/vanced.to` into:
   * `Context.getExternalFilesDir(null)/downloads/<package_name>.apk`

---

## 4. Verification & Integrity Flaws

Decompilation of `DownloadWorker.java` (lines 657–715) revealed a critical security deficiency in how downloaded APKs are handled:

```java
// Observed from com.revanced.net.revancedmanager.data.manager.DownloadWorker
Log.i("DownloadWorker", "Download completed: " + str8 + " -> " + absolutePath + 
      " bytes=" + j5 + " size=" + j6 + " expected=" + j4);

if (j4 > 0 || j6 == j4) {
    file2.delete();
    if (((File) a01Var.g).renameTo(file2)) {
        // Promoted to ready APK for installation
        return new ad1("file_path", file2.getAbsolutePath());
    }
}
```

* **No Cryptographic Hash Validation**: The application does **not** check SHA-256 or MD5 hashes of downloaded binaries before prompting installation [OBSERVED, `EVD-MGR-SEC-001`].
* **No Signing Certificate Validation**: The installer does not verify the signing certificate or signer lineage of the downloaded file [OBSERVED, `EVD-MGR-SEC-002`].
* **Length Check Only**: The sole verification performed is verifying that the downloaded byte count (`j6`) matches the HTTP content-length header (`j4`) [OBSERVED, `EVD-MGR-SEC-003`].

---

## 5. Permissions & Attack Surface

The application requests 12 permissions in its manifest:

| Permission | Risk Level | Purpose in App | Finding |
| :--- | :--- | :--- | :--- |
| `android.permission.REQUEST_INSTALL_PACKAGES` | High | Triggers Android Package Installer | Required for sideloading downloads |
| `android.permission.UPDATE_PACKAGES_WITHOUT_USER_ACTION` | High | Unattended updates on Android 12+ | Sideload automation |
| `android.permission.REQUEST_DELETE_PACKAGES` | Medium | Uninstallation handling | Application lifecycle management |
| `android.permission.QUERY_ALL_PACKAGES` | High | Scans all installed packages | Detects installed versions |
| `android.permission.RECEIVE_BOOT_COMPLETED` | Medium | Runs `UpdateCheckWorker` on boot | Background update checking |
| `android.permission.FOREGROUND_SERVICE` | Medium | Runs persistent background sync | Prevents system termination |
| `android.permission.FOREGROUND_SERVICE_DATA_SYNC` | Medium | File download worker sync | Foreground notifications |
| `android.permission.INTERNET` | Normal | Network connectivity | Catalog and APK downloads |
| `android.permission.ACCESS_NETWORK_STATE` | Normal | Checks active network connection | Connectivity monitoring |
| `android.permission.WAKE_LOCK` | Normal | Keeps CPU awake during download | Download persistence |
| `android.permission.POST_NOTIFICATIONS` | Normal | User notifications | Download progress alerts |
| `DYNAMIC_RECEIVER_NOT_EXPORTED_PERMISSION` | Internal | Android 14 receiver isolation | Standard framework security |

---

## 6. Network Endpoints

| Endpoint | Protocol | Classification | Evidence ID |
| :--- | :--- | :--- | :--- |
| `https://vanced.to/` | HTTPS | Unofficial Catalog & Infrastructure | `EVD-MGR-NET-001` |
| `https://vanced.to/apps-v3.json` | HTTPS | Prebuilt APK Manifest Catalog | `EVD-MGR-NET-002` |
| `https://vanced.to/revanced-apps.json` | HTTPS | Fallback App Manifest Catalog | `EVD-MGR-NET-003` |
| `https://vanced.to/donate-redir` | HTTPS | Commercial / Donation Redirect | `EVD-MGR-NET-004` |
| `https://github.com/vancedto/vanced-manager-plus/` | HTTPS | Source Code Repository | `EVD-MGR-NET-005` |
| `https://github.com/vancedto/vanced.to/releases/` | HTTPS | APK Binary Payload Distribution | `EVD-MGR-NET-006` |

No ad networks (e.g. Google AdMob, UnityAds, AppLovin) or commercial trackers (e.g. AppsFlyer, Adjust) were observed in the DEX string tables or class hierarchy.

---

## 7. Verdict & Analytical Conclusion

* **Verdict**: **Suspicious** (High Supply-Chain Risk)
* **Confidence**: High
* **Limitations**: Static inspection only; server-side payloads distributed by `vanced.to` can mutate over time without notice.
* **Key Reason**: The application presents itself as "ReVanced Manager Plus", creating a misleading impression of being an official tool or enhanced patcher. In reality, it is a proprietary downloader for prebuilt modified APKs distributed by an unvetted third party. Crucially, it completely lacks cryptographic integrity checks (SHA-256 or signature validation) on downloaded binaries, creating a severe supply-chain exposure whereby any server-side compromise would directly compromise the end user.

