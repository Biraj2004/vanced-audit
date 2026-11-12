# Security Recommendations & Safe Patching Guide

## 1. Supply-Chain Risk: Prebuilt Patched APKs vs Local Compilation

Downloading prebuilt modified binaries from third-party distribution websites (such as `vanced.to`, `revanced.net`, or unofficial Telegram channels) poses severe security risks:

* **No Immutable Provenance**: Users cannot verify whether the downloaded APK was built directly from open-source code or if malicious code was injected prior to packaging.
* **Shared Signing Keys**: Prebuilt APKs are signed with well-known public test keys (`CN=ReVanced`). Anyone possessing this public key can craft an update that Android will accept as an upgrade over the existing installation.
* **Lack of Independent Verification**: Prebuilt download hubs can replace binaries at any time on their servers without warning.

---

## 2. Official Project Sources vs Third-Party Impersonators

| Role | Official Source | Known Unofficial Mirrors / Impersonators (Avoid) |
| :--- | :--- | :--- |
| **ReVanced Project** | [revanced.app](https://revanced.app)<br>[github.com/ReVanced](https://github.com/ReVanced) | `vanced.to`, `revanced.net`, `revanced.io`, `revancedmanager.com` |
| **Morphe Project** | [morphe.software](https://morphe.software)<br>[github.com/MorpheApp](https://github.com/MorpheApp) | Third-party Telegram aggregators, unvetted GitHub forks |
| **MicroG / GmsCore** | [github.com/ReVanced/GmsCore](https://github.com/ReVanced/GmsCore) | Random APK hosting sites |

---

## 3. Recommended Safe Workflow: Patching Locally via ReVanced Manager

The safest method to use ReVanced applications is to compile them on your own device using the official **ReVanced Manager**.

### Step 1: Install Official ReVanced Manager
1. Download the latest official release APK strictly from [revanced.app](https://revanced.app) or the official repository [`ReVanced/revanced-manager`](https://github.com/ReVanced/revanced-manager/releases).
2. Verify the signing certificate belongs to the ReVanced team.

### Step 2: Acquire Clean Original Application Binaries
1. Obtain the recommended target version of the unmodified stock APK from a reputable archive (such as APKMirror). Ensure you download the **nodpi universal APK**, not an APK bundle or split APK.
2. Do not install the stock APK; save it to your device's internal storage.

### Step 3: Patch and Compile Locally
1. Open **ReVanced Manager**.
2. Navigate to **Patcher** > **Select an application**.
3. Tap **Storage** and select the clean stock APK downloaded in Step 2.
4. Review the recommended patch list.
5. Tap **Patch**. The manager will decompile, modify Dalvik bytecode, and repackage the binary locally in device memory.
6. When compilation completes, tap **Install**.

---

## 4. Advanced: Reproducible Building via ReVanced CLI (Command Line)

For security professionals and advanced users, building with **ReVanced CLI** on a computer ensures full control, custom private keystores, and reproducible auditing.

### Prerequisites
* Java Development Kit (JDK) 17 or 21 installed.
* Target clean unmodified APK (e.g., `youtube.apk`).

### Commands

```bash
# 1. Download official tools from github.com/ReVanced
curl -L -O https://github.com/ReVanced/revanced-cli/releases/latest/download/revanced-cli.jar
curl -L -O https://github.com/ReVanced/revanced-patches/releases/latest/download/revanced-patches.jar

# 2. (Optional) Generate your own private signing keystore
keytool -genkey -v -keystore my-personal-key.keystore -alias my-key \
  -keyalg RSA -keysize 2048 -validity 10000

# 3. Patch the clean stock APK
java -jar revanced-cli.jar patch \
  --patches revanced-patches.jar \
  --keystore my-personal-key.keystore \
  --out youtube-patched-clean.apk \
  youtube-stock.apk
```

### Benefits of Custom Key Signing
* Your patched app is signed with a private key known only to you.
* No malicious third party or prebuilt distribution service can deliver an update that overwrites your installation.

