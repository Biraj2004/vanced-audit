# Patch Provenance & Community Fork Mapping

**Audit Reference**: `EVD-PAT-MAPPING-001`  
**Security Classification**: Defensive Malware & Supply-Chain Assessment  
**Navigation**: [Overview](README.md) | [Audit Summary](00-summary.md) | [Hash Ledger](01-hashes.csv) | [Manager Teardown](02-manager.md) | [Network Triage](04-network.md) | [Malware Report](05-malware-report.md)

---

## 1. Executive Overview & Methodology

Modified Android applications distributed through unvetted third-party hubs present severe supply-chain integrity concerns. Without rigorous bytecode diffing, users cannot ascertain whether an APK contains authentic open-source community patches or weaponised backdoors.

This forensic analysis mapped the exact toolchains, patch versions, git commits, and extension classes injected into each binary obtained from `vanced.to`.

---

## 2. Consolidated Patch Provenance Matrix

| Target Application | Package Name | Upstream Patch Framework | CLI Toolchain | Patch Version | Git Commit | Signing Key Fingerprint (SHA-256) | Injected Class Scope | Verification Status |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **ReVanced Manager Plus** | `com.revanced.net.revancedmanager` | Standalone App Store (No Patcher) | N/A | N/A | N/A | `74a62bdf5dc3...` (`CN=revanced.net`) | `classes.dex` / `zh.class` | **Proprietary Downloader** |
| **Morphe Google Photos** | `app.morphe.android.apps.photos` | Rushii Morphe Fork (`RushiiMachine`) | `1.20.0.5` | `1.23.1` | Tagged Release | `5d87c9bdc8e3...` (`CN=ReVanced`) | `classes11.dex` (Multi-app helpers) | **Verified Community Code** |
| **Morphe Reddit** | `com.reddit.frontpage` | Official Morphe (`MorpheApp`) | `2.2.0.12` | `2.2.0.5` | `c90067a430` | `5d87c9bdc8e3...` (`CN=ReVanced`) | `classes14.dex`–`classes16.dex` (591 classes) | **Verified Community Code** |
| **Strava** | `com.strava` | Rushii Micro-Patch (`RushiiMachine`) | `1.20.0.5` | `1.23.1.1` | Tagged Release | `5d87c9bdc8e3...` (`CN=ReVanced`) | `classes9.dex` (2 classes) | **Verified Community Code** |
| **YouTube ReVanced** | `app.revanced.android.youtube` | Official ReVanced (`ReVanced`) | `6.0.0` | `6.3.0` | `cb9a113e85` | `5d87c9bdc8e3...` (`CN=ReVanced`) | `classes.dex`–`classes8.dex` (1,903 classes) | **Verified Community Code** |
| **YouTube Music ReVanced** | `app.revanced.android.apps.youtube.music` | Official ReVanced (`ReVanced`) | `6.0.0` | `6.2.0` | `c6507b5051` | `5d87c9bdc8e3...` (`CN=ReVanced`) | `classes.dex`–`classes6.dex` (1,106 classes) | **Verified Community Code** |

---

## 3. Deep Forensic Provenance Teardown by Target

### 3.1 ReVanced Manager Plus (`v3.6.25`)
* **Upstream Source Repository**: [`https://github.com/vancedto/vanced-manager-plus`](https://github.com/vancedto/vanced-manager-plus) `[OBSERVED, EVD-PAT-MGR-001]`.
* **Toolchain / Framework**: Native Kotlin application utilizing Jetpack Compose, Retrofit 2, and AndroidX WorkManager.
* **Patcher Analysis**: The binary contains **zero patching libraries**. It includes neither `revanced-patcher`, `smali`, `baksmali`, nor `dexlib2`. It cannot decompile, modify, or assemble APKs on device `[OBSERVED, EVD-PAT-MGR-002]`.
* **Supply-Chain Role**: It acts as a proprietary downloader and automated package installer querying remote prebuilt application manifests hosted at `https://vanced.to/apps-v3.json` `[OBSERVED, EVD-PAT-MGR-003]`.

### 3.2 Morphe Google Photos (`v7.98.0.1001971222`)
* **Upstream Source Repository**: Rushii's multi-target patch repository ([`https://github.com/RushiiMachine/revanced-patches`](https://github.com/RushiiMachine/revanced-patches)) `[OBSERVED, EVD-PAT-GPH-001]`.
* **Toolchain Identifiers**:
  * ReVanced CLI Toolchain Version: `1.20.0.5`
  * Universal Patch Bundle Version: `1.23.1`
* **Forensic Bytecode Proofs**:
  1. Package naming `app.template.extension.extension` matches the universal multi-target template maintained in Rushii's patch bundle.
  2. Classes `PixelSpoofHelper` and `PhotosDcimFolderPatch` located in `classes11.dex` implement the exact device emulation routines (Pixel XL profile) required to unlock unlimited cloud storage.
  3. Shared multi-application helpers bundled within `classes11.dex` (`AmazonHelper`, `CitymapperHelper`, `FlightradarHelper`, `FuelioHelper`, `MoovitHelper`, `NetMonsterHelper`, `TransitHelper`, `UDiscHelper`) are bitwise identical to the upstream release artefacts.

### 3.3 Morphe Reddit (`v2026.40.0`)
* **Upstream Source Repository**: Official Morphe Patches ([`https://github.com/MorpheApp/morphe-patches`](https://github.com/MorpheApp/morphe-patches)) `[OBSERVED, EVD-PAT-RED-001]`.
* **Toolchain Identifiers**:
  * Morphe CLI Version: `2.2.0.12`
  * Morphe Patches Version: `2.2.0.5`
  * Git Tree Commit: `c90067a430`
* **Forensic Bytecode Proofs**:
  1. 591 compiled classes injected into `classes14.dex`, `classes15.dex`, and `classes16.dex` under namespace `app.morphe.extension.reddit.patches.*`.
  2. Hard-coded endpoints reference `https://api.morphe.software/v2` and `https://morphe.software/credits/`.
  3. Commit hash `c90067a430` in the build metadata corresponds to the exact Git commit tagged `v2.2.0.5` in the official Morphe repository.

### 3.4 Strava (`v480.15`)
* **Upstream Source Repository**: Rushii's universal patch bundle ([`https://github.com/RushiiMachine/revanced-patches`](https://github.com/RushiiMachine/revanced-patches)) `[OBSERVED, EVD-PAT-STR-001]`.
* **Toolchain Identifiers**:
  * CLI Toolchain Version: `1.20.0.5`
  * Patch Bundle Version: `1.23.1.1`
* **Forensic Bytecode Proofs**:
  1. Unlike complex multi-class extensions, this modification introduces a standalone, minimal DEX: `classes9.dex` (size: 13,804 bytes).
  2. `classes9.dex` contains an overridden implementation of `com.strava.subscriptions.data.models.SubscriptionDetail` that enforces `isPremium() = true`.
  3. Zero extraneous classes, hooks, or analytics SDKs were bundled.

### 3.5 YouTube ReVanced (`v21.16.256`)
* **Upstream Source Repository**: Official ReVanced Patches ([`https://github.com/ReVanced/revanced-patches`](https://github.com/ReVanced/revanced-patches)) `[OBSERVED, EVD-PAT-YTB-001]`.
* **Toolchain Identifiers**:
  * ReVanced CLI Version: `6.0.0`
  * ReVanced Patches Version: `6.3.0`
  * Git Tree Commit: `cb9a113e85`
* **Forensic Bytecode Proofs**:
  1. 1,903 compiled classes injected into `classes.dex` through `classes8.dex` under namespace `app.revanced.extension.youtube.*`.
  2. Verified implementations of `VideoAdsPatch`, `SponsorBlockPatch`, `ReturnYouTubeDislikePatch`, `DeArrowPatch`, and `MicroGSupportPatch`.
  3. Git tree commit `cb9a113e85` matches the upstream Git commit tagged `v6.3.0` on the official ReVanced repository.

### 3.6 YouTube Music ReVanced (`v9.15.51`)
* **Upstream Source Repository**: Official ReVanced Patches ([`https://github.com/ReVanced/revanced-patches`](https://github.com/ReVanced/revanced-patches)) `[OBSERVED, EVD-PAT-YTM-001]`.
* **Toolchain Identifiers**:
  * ReVanced CLI Version: `6.0.0`
  * ReVanced Patches Version: `6.2.0`
  * Git Tree Commit: `c6507b5051`
* **Forensic Bytecode Proofs**:
  1. 1,106 compiled classes injected into `classes.dex` through `classes6.dex` under namespace `app.revanced.extension.music.*`.
  2. Verified implementations of `AudioAdsPatch`, `BackgroundPlaybackPatch`, and `ExclusiveAudioModePatch`.
  3. Git tree commit `c6507b5051` matches the official ReVanced patch set `v6.2.0` release tag.

---

## 4. Supply-Chain Differential Assessment

Comparing the prebuilt binaries distributed by `vanced.to` against official community releases establishes that:

1. **Authentic Community Lineage for the 5 Prebuilt Apps**:
   * The bytecode added to YouTube, YouTube Music, Reddit, Google Photos, and Strava matches the corresponding open-source patch projects.
   * `vanced.to` acts solely as an automated builder and hosting mirror for these 5 APKs.
   * No trojans, credential stealers, dynamic loaders, or surveillance libraries were introduced into these applications.

2. **Supply-Chain Exposure in ReVanced Manager Plus**:
   * Unlike the 5 patched applications, `ReVanced Manager Plus` is **not** an authentic ReVanced product.
   * It is an independent application published by the operators of `vanced.to` / `revanced.net`.
   * By pairing deceptive branding with an unauthenticated sideloading pipeline lacking cryptographic integrity checks, it introduces severe systemic risk.

---

**Navigation**: [Overview](README.md) | [Audit Summary](00-summary.md) | [Hash Ledger](01-hashes.csv) | [Manager Teardown](02-manager.md) | [Network Triage](04-network.md) | [Malware Report](05-malware-report.md)
