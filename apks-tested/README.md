# Tested Target APK Sample Binaries

**Audit Reference**: `EVD-SAM-STORAGE-001`  
**Security Classification**: Defensive Malware Artifact Quarantine  
**Navigation**: [Overview](../README.md) | [Audit Summary](../00-summary.md) | [Hash Ledger](../01-hashes.csv) | [Manager Teardown](../02-manager.md) | [Malware Report](../05-malware-report.md)

---

## 1. Storage & Git Exclusion Policy

This directory stores raw target Android Application Packages (APKs) downloaded from `https://vanced.to` for static reverse engineering.

In accordance with **GitHub file size restrictions** (which reject pushes containing individual files exceeding 100 MiB) and defensive malware handling standards, raw application binaries are **not committed to version control**. They are ignored by the root [`.gitignore`](../.gitignore):

```gitignore
# Excerpt from root .gitignore
apks-tested/*
!apks-tested/.gitignore
!apks-tested/README.md
```

---

## 2. Cryptographic Verification & Hash Ledger

To reproduce this assessment or verify newly downloaded samples, compare computed cryptographic digests against the baseline established during this audit:

| Target Application | File Name | Size (Bytes) | SHA-256 Digest |
| :--- | :--- | :--- | :--- |
| **ReVanced Manager Plus** | `vanced.to_revanced_manager_plus_v3.6.25.apk` | 2,477,424 | `f13df8fb954e096ec4f2d79718f8161f7b771b9094bc105600a7493837045a60` |
| **Morphe Google Photos** | `vanced.to_rushi_com.google.android.apps.photos_arm64v8a_v7.98.0.1001971222_cli1.20.0.5_p1.23.1.apk` | 134,247,409 | `be9bb732f7c3f014299769a66b4f70730d08a2c310dd8f517695a92cbd4939f4` |
| **Morphe Reddit** | `vanced.to_morphe_com.reddit.frontpage_arm64v8a_v2026.40.0_cli2.2.0.12_p2.2.0.5_c90067a430.apk` | 88,417,484 | `a4468cd7924e55970303df7deca23162b5d01a8160b0ebafd9597d83fc041112` |
| **Strava** | `vanced.to_rushi_com.strava_arm64v8a_v480.15_cli1.20.0.5_p1.23.1.1.apk` | 122,829,417 | `60b3e22f226067d473adb032d83972750112ccdcb5b95bc0efe603b6650f14d8` |
| **YouTube ReVanced** | `vanced.to_revanced_com.google.android.youtube_arm64v8a_v21.16.256_cli6.0.0_p6.3.0_cb9a113e85.apk` | 195,914,241 | `e948a7ef5692c227ab7f8425cb71818e7f075f6d3bba61346fb68fc00afdfb7c` |
| **YouTube Music ReVanced** | `vanced.to_revanced_com.google.android.apps.youtube.music_arm64v8a_v9.15.51_cli6.0.0_p6.2.0_c6507b5051.apk` | 78,762,579 | `841156b72979b80ff05aafbcd9cb8269a04e5688f4c25cf500e3abbe902ee32c` |

Complete tabular details including SHA-1, MD5, signing certificate fingerprints, and download URLs are preserved in [`../01-hashes.csv`](../01-hashes.csv).

---

## 3. How to Verify Binary Hashes Locally

### PowerShell (Windows)
```powershell
Get-ChildItem -Filter *.apk | ForEach-Object {
    [PSCustomObject]@{
        FileName = $_.Name
        SHA256   = (Get-FileHash $_.FullName -Algorithm SHA256).Hash.ToLower()
    }
} | Format-Table -AutoSize
```

### Bash (Linux / macOS)
```bash
sha256sum *.apk
```

---

**Navigation**: [Overview](../README.md) | [Audit Summary](../00-summary.md) | [Hash Ledger](../01-hashes.csv) | [Manager Teardown](../02-manager.md) | [Malware Report](../05-malware-report.md)
