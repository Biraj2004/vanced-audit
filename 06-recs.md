# Defensive Recommendations & Reproducible Local Build Guide

**Audit Reference**: `EVD-REC-GUIDE-001`  
**Security Classification**: Defensive Mobile Threat Remediation & Hardening  
**Navigation**: [Overview](README.md) | [Audit Summary](00-summary.md) | [Hash Ledger](01-hashes.csv) | [Manager Teardown](02-manager.md) | [Patch Provenance](03-patches.md) | [Malware Report](05-malware-report.md)

---

## 1. Primary Remediation Steps

### 1.1 Immediate Uninstallation of ReVanced Manager Plus
Users who have installed `ReVanced Manager Plus` (`com.revanced.net.revancedmanager`) should remove it from their devices immediately:
1. Open Android **Settings** > **Apps** > **All Apps**.
2. Locate **RV Manager +** or **ReVanced Manager Plus**.
3. Select **Uninstall**.
4. Revoke any elevated permissions previously granted (specifically **Install unknown apps**).

**Rationale**: The utility is published by an unofficial commercial distributor (`revanced.net`) and contains no local patcher logic. Its download pipeline checks only HTTP file length and performs zero SHA-256 or certificate verification, creating an unmitigated supply-chain vulnerability.

### 1.2 Discontinue Acquiring Prebuilt Modded APKs
Never download pre-compiled modified APK binaries from third-party websites (`vanced.to`, `revanced.net`, `apkpure.com`) or untrusted Telegram channels. Even though the five prebuilt application binaries tested in this audit were clean at the time of download, third-party distribution servers can silently swap binaries with trojaned versions at any time without user notification.

---

## 2. Official Toolchain Verification & Sources

Always acquire patching software directly from verified, official source repositories:

| Project Component | Official Web Domain | Official GitHub Repository |
| :--- | :--- | :--- |
| **Official ReVanced Website** | [`https://revanced.app`](https://revanced.app) | N/A |
| **Official ReVanced Manager** | [`https://revanced.app/download`](https://revanced.app/download) | [`ReVanced/revanced-manager`](https://github.com/ReVanced/revanced-manager) |
| **Official ReVanced CLI** | N/A | [`ReVanced/revanced-cli`](https://github.com/ReVanced/revanced-cli) |
| **Official ReVanced Patches** | N/A | [`ReVanced/revanced-patches`](https://github.com/ReVanced/revanced-patches) |
| **Official Morphe Project** | [`https://morphe.software`](https://morphe.software) | [`MorpheApp/morphe-patches`](https://github.com/MorpheApp/morphe-patches) |
| **Official GmsCore / MicroG** | N/A | [`ReVanced/GmsCore`](https://github.com/ReVanced/GmsCore) |

---

## 3. Reproducible DIY Local Compilation Guide (ReVanced CLI)

Compiling patched applications locally on your own workstation or device provides complete assurance of supply-chain integrity.

### 3.1 Environment Prerequisites
* **Java Runtime Environment**: OpenJDK 17 or 21 (`java -version`).
* **Official CLI Binary**: Download the latest `revanced-cli-*-all.jar` from [`ReVanced/revanced-cli/releases`](https://github.com/ReVanced/revanced-cli/releases).
* **Official Patches Bundle**: Download `revanced-patches-*.jar` from [`ReVanced/revanced-patches/releases`](https://github.com/ReVanced/revanced-patches/releases).
* **Clean Stock APK**: Obtain a clean, unmodified stock APK from a reputable archive (such as APKMirror). Verify that the APK version matches the recommended target version in the patch release notes.

### 3.2 Step 1: Generate a Private Android Keystore
By default, the ReVanced CLI signs patched APKs with a public test key (`CN=ReVanced`). To eliminate the risk of third-party update collisions or signature spoofing, generate your own private keystore:

```bash
# Generate a private 2048-bit RSA keystore valid for 30 years
keytool -genkey -v \
  -keystore my-release-key.keystore \
  -alias my-key-alias \
  -keyalg RSA \
  -keysize 2048 \
  -validity 10000 \
  -dname "CN=PrivateUser, OU=Personal, O=Secure, C=IN"
```

### 3.3 Step 2: Compile YouTube ReVanced Locally
Execute the following command to decompile, apply verified open-source patches, and assemble the output binary:

```bash
java -jar revanced-cli-*-all.jar patch \
  --patches revanced-patches-*.jar \
  --keystore my-release-key.keystore \
  --keystore-entry-alias my-key-alias \
  --out youtube-revanced-clean.apk \
  stock-youtube-v21.16.xx.apk
```

### 3.4 Step 3: Compile YouTube Music ReVanced Locally

```bash
java -jar revanced-cli-*-all.jar patch \
  --patches revanced-patches-*.jar \
  --keystore my-release-key.keystore \
  --keystore-entry-alias my-key-alias \
  --out ytmusic-revanced-clean.apk \
  stock-ytmusic-v9.15.xx.apk
```

---

## 4. Mobile Hardening Checklist for Android Users

1. **Keep "Install Unknown Apps" Disabled**:
   * Navigate to **Settings** > **Apps** > **Special App Access** > **Install Unknown Apps**.
   * Ensure that web browsers, messaging apps, and file managers have this permission toggled **Off**. Enable it only temporarily during intentional sideloading sessions.
2. **Verify Cryptographic Hashes Before Installing**:
   * Prior to installing any APK on your device, compute its SHA-256 hash on your computer (`certutil -hashfile app.apk SHA256` on Windows or `sha256sum app.apk` on Linux/macOS).
   * Confirm the digest matches official published release hashes.
3. **Inspect Signing Certificate Fingerprints**:
   * Inspect the signing key before installation using `apksigner`:
     ```bash
     apksigner verify --print-certs app.apk
     ```
   * Confirm that the signer matches your trusted keystore rather than an unverified third party.
4. **Isolate GmsCore / MicroG**:
   * Download `GmsCore` exclusively from official repositories (`https://github.com/ReVanced/GmsCore`).
   * Never grant GmsCore unnecessary device-admin or accessibility permissions.

---

**Navigation**: [Overview](README.md) | [Audit Summary](00-summary.md) | [Hash Ledger](01-hashes.csv) | [Manager Teardown](02-manager.md) | [Patch Provenance](03-patches.md) | [Malware Report](05-malware-report.md)
