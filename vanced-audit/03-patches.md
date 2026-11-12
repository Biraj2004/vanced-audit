# Patch Provenance & Community Fork Mapping

This document details the community patch frameworks, CLI toolchain versions, commit hashes, and structural proofs recovered from the 6 audited targets downloaded from `vanced.to`.

---

## 1. Consolidated Patch Matrix

| Target Application | Upstream Patch Framework | CLI Version | Patch Version | Commit Hash | Recompilation Key Fingerprint (SHA-256) | Proof Location |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **ReVanced Manager Plus** | N/A (Standalone app store) | N/A | N/A | N/A | `74a62bdf5dc3105af7ac189364e4925d84a669ca...` | `classes.dex` / `p82.class` |
| **Morphe Google Photos** | Rushii (`RushiiMachine`) Morphe fork | `1.20.0.5` | `1.23.1` | N/A | `5d87c9bdc8e368dcf80971f33455db4c2e7dd754...` | `classes11.dex` (`app.template.extension`) |
| **Morphe Reddit** | Official Morphe (`MorpheApp`) | `2.2.0.12` | `2.2.0.5` | `c90067a430` | `5d87c9bdc8e368dcf80971f33455db4c2e7dd754...` | `classes14.dex`–`classes16.dex` |
| **Strava** | Rushii (`RushiiMachine`) micro-patch | `1.20.0.5` | `1.23.1.1` | N/A | `5d87c9bdc8e368dcf80971f33455db4c2e7dd754...` | `classes9.dex` (`SubscriptionDetail`) |
| **YouTube ReVanced** | Official ReVanced (`ReVanced`) | `6.0.0` | `6.3.0` | `cb9a113e85` | `5d87c9bdc8e368dcf80971f33455db4c2e7dd754...` | `classes.dex`–`classes8.dex` |
| **YouTube Music ReVanced** | Official ReVanced (`ReVanced`) | `6.0.0` | `6.2.0` | `c6507b5051` | `5d87c9bdc8e368dcf80971f33455db4c2e7dd754...` | `classes.dex`–`classes6.dex` |

---

## 2. Forensic Proof by Target

### 2.1 ReVanced Manager Plus (`v3.6.25`)
* **Upstream Source**: Open-source repository [`vancedto/vanced-manager-plus`](https://github.com/vancedto/vanced-manager-plus).
* **Patch Framework Relationship**: Contains **no patch framework**. The app is entirely an uncurated APK package downloader for pre-patched binaries.
* **Proof**:
  1. Complete absence of `revanced-patcher`, `smali`, `baksmali`, or Dalvik bytecode rewriting engines [OBSERVED, `EVD-PAT-MGR-001`].
  2. The application's REST endpoints directly download ready-made APK files from GitHub releases hosted under [`vancedto/vanced.to`](https://github.com/vancedto/vanced.to/releases/) [OBSERVED, `EVD-PAT-MGR-002`].

### 2.2 Morphe Google Photos (`v7.98.0.1001971222`)
* **Upstream Source**: Rushii's Morphe / ReVanced patch bundle fork ([`RushiiMachine/revanced-patches`](https://github.com/RushiiMachine)).
* **Toolchain Identifiers**:
  * CLI Version: `1.20.0.5`
  * Patch Release: `1.23.1`
* **Proof**:
  1. Package naming `app.template.extension.extension` matches the standard multi-target extension repository template maintained by Rushii [OBSERVED, `EVD-PAT-GPH-001`].
  2. The presence of `PixelSpoofHelper` and `PhotosDcimFolderPatch` classes in `classes11.dex` verifies the exact mechanism used to unlock original-quality cloud backup by spoofing Pixel device identifiers [OBSERVED, `EVD-PAT-GPH-002`].
  3. Additional multi-app helpers bundled in the same extension DEX (`AmazonHelper`, `CitymapperHelper`, `FlightradarHelper`, `FuelioHelper`, `MoovitHelper`, `NetMonsterHelper`, `TransitHelper`, `UDiscHelper`) are identical to Rushii's shared universal extension library [OBSERVED, `EVD-PAT-GPH-003`].

### 2.3 Morphe Reddit (`v2026.40.0`)
* **Upstream Source**: Official Morphe Patches repository ([`MorpheApp/morphe-patches`](https://github.com/MorpheApp/morphe-patches)).
* **Toolchain Identifiers**:
  * CLI Version: `2.2.0.12`
  * Patch Version: `2.2.0.5`
  * Git Commit: `c90067a430`
* **Proof**:
  1. 591 classes under package `app.morphe.extension.reddit.patches.*` across `classes14.dex` through `classes16.dex` [OBSERVED, `EVD-PAT-RED-001`].
  2. Hard-coded strings reference `https://api.morphe.software/v2`, `https://morphe.software/credits/`, and `https://github.com/MorpheApp/morphe-patches` [OBSERVED, `EVD-PAT-RED-002`].
  3. Git tree commit `c90067a430` is explicitly encoded in the build artifact name and matches the Morphe repository release tags [OBSERVED, `EVD-PAT-RED-003`].

### 2.4 Strava (`v480.15`)
* **Upstream Source**: Rushii / community subscription bypass script.
* **Toolchain Identifiers**:
  * CLI Version: `1.20.0.5`
  * Patch Version: `1.23.1.1`
* **Proof**:
  1. The modification does not inject an expansive extension library; instead, it compiles an isolated supplemental DEX (`classes9.dex`, 13,804 bytes) [OBSERVED, `EVD-PAT-STR-001`].
  2. `classes9.dex` contains an overridden implementation of `com.strava.subscriptions.data.models.SubscriptionDetail`, which hardcodes subscriber state returns (`isPremium() = true`, `isFree() = false`, `isInTrial() = false`) [OBSERVED, `EVD-PAT-STR-002`].

### 2.5 YouTube ReVanced (`v21.16.256`)
* **Upstream Source**: Official ReVanced project repository ([`ReVanced/revanced-patches`](https://github.com/ReVanced/revanced-patches)).
* **Toolchain Identifiers**:
  * ReVanced CLI Version: `6.0.0`
  * ReVanced Patches Version: `6.3.0`
  * Git Commit: `cb9a113e85`
* **Proof**:
  1. 1,903 classes located in `app.revanced.extension.youtube.*` [OBSERVED, `EVD-PAT-YTB-001`].
  2. Patch configuration endpoints reference `https://api.revanced.app/v4`, `https://api.revanced.app/v5`, and official SponsorBlock/RYD mirrors [OBSERVED, `EVD-PAT-YTB-002`].
  3. Git tree commit `cb9a113e85` corresponds to the official ReVanced patch set `v6.3.0` release tag [OBSERVED, `EVD-PAT-YTB-003`].

### 2.6 YouTube Music ReVanced (`v9.15.51`)
* **Upstream Source**: Official ReVanced project repository ([`ReVanced/revanced-patches`](https://github.com/ReVanced/revanced-patches)).
* **Toolchain Identifiers**:
  * ReVanced CLI Version: `6.0.0`
  * ReVanced Patches Version: `6.2.0`
  * Git Commit: `c6507b5051`
* **Proof**:
  1. 1,106 classes located in `app.revanced.extension.music.*` [OBSERVED, `EVD-PAT-YTM-001`].
  2. Features strictly implement standard ReVanced audio patches: ad blocking, background playback, and the dedicated audio-only stream switch [OBSERVED, `EVD-PAT-YTM-002`].
  3. Git tree commit `c6507b5051` matches the official ReVanced patch set `v6.2.0` release tag [OBSERVED, `EVD-PAT-YTM-003`].

---

## 3. Supply-Chain Differential Assessment

Comparing the prebuilt binaries distributed by `vanced.to` against the official community patch sources demonstrates that:

1. **Clean Bytecode Provenance for the 5 Prebuilt Apps**:
   * The actual code injected into YouTube, YouTube Music, Reddit, Google Photos, and Strava matches the corresponding open-source patch repositories.
   * `vanced.to` acts as an automated compilation and mirroring host for these 5 APKs.
   * No secondary malware loaders, commercial adware SDKs, or surveillance spyware were added to these 5 prebuilt applications.

2. **Supply-Chain Anomaly in ReVanced Manager Plus**:
   * Unlike the 5 patched apps, `ReVanced Manager Plus` is **not** an official ReVanced build.
   * It is an independent application authored by the maintainers of `vanced.to` / `revanced.net`.
   * It uses branding and nomenclature that confuse users into believing it is the official ReVanced Manager, whilst omitting all patcher logic and deploying insecure update mechanisms.

