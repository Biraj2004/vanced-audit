# Consolidated Network Endpoint & Domain Audit

**Audit Reference**: `EVD-NET-LEDGER-001`  
**Security Classification**: Defensive Malware & Supply-Chain Assessment  
**Navigation**: [Root Repository README](../README.md) | [Audit Summary](00-summary.md) | [Hash Ledger](01-hashes.csv) | [Manager Teardown](02-manager.md) | [Patch Provenance](03-patches.md) | [Malware Report](05-malware-report.md)

---

This document inventories and classifies all 435 external network domains and endpoints extracted from Dalvik bytecode string pools across the 6 audited targets.

## 1. Classification Summary

| Domain / Host | Classification | Associated Applications | Risk Level | Operational Purpose |
| :--- | :--- | :--- | :--- | :--- |
| `vanced.to` | **Suspicious / Unofficial Mirror** | ReVanced Manager Plus | **High** | Third-party APK catalog, metadata API, and donation redirects |
| `github.com` | **Third-Party Build Repo** | Morphe Google Photos, Morphe Reddit, ReVanced Manager Plus, Strava, YouTube Music ReVanced, YouTube ReVanced | **Medium** | Prebuilt APK binary hosting repository |
| `api2.branch.io` | **Analytics / Telemetry** | Strava | **Low** | Standard application crash reporting and usage analytics (inherited from original APKs) |
| `app-measurement.com` | **Analytics / Telemetry** | Morphe Reddit, Strava, YouTube Music ReVanced, YouTube ReVanced | **Low** | Standard application crash reporting and usage analytics (inherited from original APKs) |
| `cdn.branch.io` | **Analytics / Telemetry** | Strava | **Low** | Standard application crash reporting and usage analytics (inherited from original APKs) |
| `docs.sentry.io` | **Analytics / Telemetry** | Strava | **Low** | Standard application crash reporting and usage analytics (inherited from original APKs) |
| `doubleclick.net` | **Analytics / Telemetry** | Morphe Google Photos | **Low** | Standard application crash reporting and usage analytics (inherited from original APKs) |
| `firebase-settings.crashlytics.com` | **Analytics / Telemetry** | Morphe Reddit | **Low** | Standard application crash reporting and usage analytics (inherited from original APKs) |
| `firebase.google.com` | **Analytics / Telemetry** | Morphe Google Photos, Morphe Reddit, Strava, YouTube Music ReVanced, YouTube ReVanced | **Low** | Standard application crash reporting and usage analytics (inherited from original APKs) |
| `firebase.googleapis.com` | **Analytics / Telemetry** | Morphe Google Photos | **Low** | Standard application crash reporting and usage analytics (inherited from original APKs) |
| `firebaseinstallations.googleapis.com` | **Analytics / Telemetry** | Morphe Google Photos, Morphe Reddit, Strava | **Low** | Standard application crash reporting and usage analytics (inherited from original APKs) |
| `googleads.g.doubleclick.net` | **Analytics / Telemetry** | YouTube Music ReVanced, YouTube ReVanced | **Low** | Standard application crash reporting and usage analytics (inherited from original APKs) |
| `googleads.g.doubleclick.net,https:` | **Analytics / Telemetry** | YouTube Music ReVanced, YouTube ReVanced | **Low** | Standard application crash reporting and usage analytics (inherited from original APKs) |
| `ssl.google-analytics.com` | **Analytics / Telemetry** | Morphe Google Photos | **Low** | Standard application crash reporting and usage analytics (inherited from original APKs) |
| `stats.g.doubleclick.net` | **Analytics / Telemetry** | Morphe Google Photos | **Low** | Standard application crash reporting and usage analytics (inherited from original APKs) |
| `www.google-analytics.com` | **Analytics / Telemetry** | Morphe Google Photos | **Low** | Standard application crash reporting and usage analytics (inherited from original APKs) |
| `*.google.com` | **Expected Original Vendor** | Morphe Google Photos | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `*.googlevideo.com` | **Expected Original Vendor** | Morphe Google Photos | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `accounts.google.com` | **Expected Original Vendor** | Morphe Google Photos, Morphe Reddit, Strava, YouTube Music ReVanced, YouTube ReVanced | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `accounts.reddit.com` | **Expected Original Vendor** | Morphe Reddit | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `accounts.sandbox.google.com` | **Expected Original Vendor** | YouTube ReVanced | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `ads.reddit.com` | **Expected Original Vendor** | Morphe Reddit | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `alb.reddit.com` | **Expected Original Vendor** | Morphe Reddit | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `amp.reddit.com` | **Expected Original Vendor** | Morphe Reddit | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `analytics.google.com` | **Expected Original Vendor** | Morphe Google Photos | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `answers.reddit.com` | **Expected Original Vendor** | Morphe Reddit | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `ap2.googleusercontent.com` | **Expected Original Vendor** | Morphe Google Photos | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `api-free.deepl.com` | **Expected Original Vendor** | Morphe Reddit | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `api.deepl.com` | **Expected Original Vendor** | Morphe Reddit | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `api.mapbox.com` | **Expected Original Vendor** | Strava | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `api.redgifs.com` | **Expected Original Vendor** | Morphe Reddit, YouTube Music ReVanced, YouTube ReVanced | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `apis.google.com` | **Expected Original Vendor** | Morphe Google Photos | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `applink.reddit.com` | **Expected Original Vendor** | Morphe Reddit | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `apps.mapbox.com` | **Expected Original Vendor** | Strava | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `autopush-notifications-pa.sandbox.googleapis.com:443` | **Expected Original Vendor** | Morphe Google Photos, YouTube Music ReVanced, YouTube ReVanced | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `autopush-qual-playground-notifications-pa.sandbox.googleapis.com:443` | **Expected Original Vendor** | Morphe Google Photos, YouTube Music ReVanced, YouTube ReVanced | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `b.thumbs.redditmedia.com` | **Expected Original Vendor** | Morphe Reddit | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `c2paregistration.pa.googleapis.com` | **Expected Original Vendor** | Morphe Google Photos | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `c2paregistration.pa.googleapis.com:443` | **Expected Original Vendor** | Morphe Google Photos | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `cdn-1.strava.com` | **Expected Original Vendor** | Strava | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `cf.gql-fed.reddit.com` | **Expected Original Vendor** | Morphe Reddit | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `cf.preview.redd.it` | **Expected Original Vendor** | Morphe Reddit | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `chat.google.com` | **Expected Original Vendor** | Morphe Google Photos | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `chat.reddit.com` | **Expected Original Vendor** | Morphe Reddit | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `config.mapbox.com` | **Expected Original Vendor** | Strava | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `consent.google.com` | **Expected Original Vendor** | YouTube ReVanced | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `content-a.strava.com` | **Expected Original Vendor** | Strava | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `coretimestamping.pa.googleapis.com` | **Expected Original Vendor** | Morphe Google Photos | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `crowdsource-pa.clients6.google.com` | **Expected Original Vendor** | Morphe Google Photos | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `crowdsource.google.com` | **Expected Original Vendor** | Morphe Google Photos | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `csi.gstatic.com` | **Expected Original Vendor** | YouTube Music ReVanced, YouTube ReVanced | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `csp.withgoogle.com` | **Expected Original Vendor** | YouTube ReVanced | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `dev-notifications-pa.corp.googleapis.com:443` | **Expected Original Vendor** | Morphe Google Photos, YouTube Music ReVanced, YouTube ReVanced | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `developers.google.com` | **Expected Original Vendor** | Morphe Google Photos, Morphe Reddit, YouTube Music ReVanced | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `developers.reddit.com` | **Expected Original Vendor** | Morphe Reddit | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `docs.google.com` | **Expected Original Vendor** | Morphe Google Photos, Morphe Reddit | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `docs.mapbox.com` | **Expected Original Vendor** | Strava | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `drive-thirdparty.googleusercontent.com` | **Expected Original Vendor** | Morphe Google Photos | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `drive.google.com` | **Expected Original Vendor** | Morphe Google Photos | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `emoji.redditmedia.com` | **Expected Original Vendor** | Morphe Reddit | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `encrypted-tbn0.gstatic.com` | **Expected Original Vendor** | YouTube ReVanced | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `encrypted-tbn1.gstatic.com` | **Expected Original Vendor** | YouTube ReVanced | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `encrypted-tbn2.gstatic.com` | **Expected Original Vendor** | YouTube ReVanced | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `encrypted-tbn3.gstatic.com` | **Expected Original Vendor** | YouTube ReVanced | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `events.mapbox.com` | **Expected Original Vendor** | Strava | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `families.youtube.com` | **Expected Original Vendor** | YouTube Music ReVanced, YouTube ReVanced | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `fcmregistrations.googleapis.com` | **Expected Original Vendor** | Morphe Google Photos, Morphe Reddit | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `feedback-pa.clients6.google.com` | **Expected Original Vendor** | Morphe Google Photos | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `feedback.googleusercontent.com` | **Expected Original Vendor** | Morphe Google Photos | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `fonts.googleapis.com` | **Expected Original Vendor** | Morphe Google Photos | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `fonts.gstatic.com` | **Expected Original Vendor** | Morphe Google Photos, YouTube ReVanced | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `foo.googlevideo.com` | **Expected Original Vendor** | YouTube Music ReVanced, YouTube ReVanced | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `gemini.google.com` | **Expected Original Vendor** | Morphe Google Photos | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `google.com` | **Expected Original Vendor** | Morphe Google Photos, Morphe Reddit, Strava, YouTube Music ReVanced, YouTube ReVanced | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `goto.google.com` | **Expected Original Vendor** | YouTube ReVanced | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `gql-fed.reddit.com` | **Expected Original Vendor** | Morphe Reddit | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `graphql-clients.prod.strava.com` | **Expected Original Vendor** | Strava | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `graphql.staging.strava.com` | **Expected Original Vendor** | Strava | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `graphql.strava.com` | **Expected Original Vendor** | Strava | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `green-youtubei.sandbox.googleapis.com` | **Expected Original Vendor** | YouTube Music ReVanced, YouTube ReVanced | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `gstatic.com` | **Expected Original Vendor** | Morphe Google Photos | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `heatmap-external-a.strava.com` | **Expected Original Vendor** | Strava | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `heatmap-external-c.strava.com` | **Expected Original Vendor** | Strava | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `heatmap-origin-k8s.strava.com` | **Expected Original Vendor** | Strava | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `history.google.com` | **Expected Original Vendor** | YouTube Music ReVanced | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `home.google.com` | **Expected Original Vendor** | Morphe Google Photos | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `htapi.google.com` | **Expected Original Vendor** | Morphe Google Photos | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `i.redd.it` | **Expected Original Vendor** | Morphe Reddit | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `imasdk.googleapis.com` | **Expected Original Vendor** | YouTube Music ReVanced, YouTube ReVanced | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `issuetracker.google.com` | **Expected Original Vendor** | Morphe Google Photos, Morphe Reddit, Strava, YouTube Music ReVanced, YouTube ReVanced | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `lh3.googleusercontent.com` | **Expected Original Vendor** | Morphe Google Photos, YouTube Music ReVanced, YouTube ReVanced | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `lh4.googleusercontent.com` | **Expected Original Vendor** | Morphe Google Photos, YouTube ReVanced | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `lh5.googleusercontent.com` | **Expected Original Vendor** | Morphe Google Photos, YouTube ReVanced | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `lh6.googleusercontent.com` | **Expected Original Vendor** | Morphe Google Photos, YouTube ReVanced | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `m.reddit.com` | **Expected Original Vendor** | Morphe Reddit | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `m.strava.com` | **Expected Original Vendor** | Strava | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `m.youtube.com` | **Expected Original Vendor** | YouTube ReVanced | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `maps.googleapis.com` | **Expected Original Vendor** | Morphe Google Photos | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `mcp.staging.strava.com` | **Expected Original Vendor** | Strava | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `mcp.strava.com` | **Expected Original Vendor** | Strava | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `mod.reddit.com` | **Expected Original Vendor** | Morphe Reddit | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `music.youtube.com` | **Expected Original Vendor** | YouTube Music ReVanced | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `myaccount-staging.corp.google.com` | **Expected Original Vendor** | YouTube ReVanced | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `myaccount.google.com` | **Expected Original Vendor** | Morphe Google Photos, YouTube Music ReVanced, YouTube ReVanced | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `myactivity.google.com` | **Expected Original Vendor** | Morphe Google Photos, YouTube Music ReVanced | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `new.reddit.com` | **Expected Original Vendor** | Morphe Reddit | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `nft.reddit.com` | **Expected Original Vendor** | Morphe Reddit | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `notifications-pa.googleapis.com:443` | **Expected Original Vendor** | Morphe Google Photos, YouTube Music ReVanced, YouTube ReVanced | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `np.reddit.com` | **Expected Original Vendor** | Morphe Reddit | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `ns.google.com` | **Expected Original Vendor** | Morphe Google Photos | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `oauth-redirect.googleusercontent.com` | **Expected Original Vendor** | YouTube ReVanced | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `old.reddit.com` | **Expected Original Vendor** | Morphe Reddit | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `one-autopush.sandbox.google.com` | **Expected Original Vendor** | Morphe Google Photos | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `one-staging.sandbox.google.com` | **Expected Original Vendor** | Morphe Google Photos | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `one.google.com` | **Expected Original Vendor** | Morphe Google Photos | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `pay.google.com` | **Expected Original Vendor** | Morphe Google Photos | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `people-pa.clients6.google.com` | **Expected Original Vendor** | Morphe Google Photos | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `personal-heatmaps-external.strava.com` | **Expected Original Vendor** | Strava | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `photos.fife.usercontent.google.com` | **Expected Original Vendor** | Morphe Google Photos | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `photos.google.com` | **Expected Original Vendor** | Morphe Google Photos | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `photos.googleapis.com` | **Expected Original Vendor** | Morphe Google Photos | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `pixel-config.reddit.com` | **Expected Original Vendor** | Morphe Reddit | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `play-lh.googleusercontent.com` | **Expected Original Vendor** | YouTube ReVanced | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `play.google.com` | **Expected Original Vendor** | Morphe Google Photos, Morphe Reddit, Strava, YouTube Music ReVanced, YouTube ReVanced | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `policies.google.com` | **Expected Original Vendor** | Morphe Google Photos | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `preview.redd.it` | **Expected Original Vendor** | Morphe Reddit | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `recap-static.strava.com` | **Expected Original Vendor** | Strava | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `recap.strava.com` | **Expected Original Vendor** | Strava | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `redd.it` | **Expected Original Vendor** | Morphe Reddit | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `reddit.com` | **Expected Original Vendor** | Morphe Reddit | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `reddit.com%s` | **Expected Original Vendor** | Morphe Reddit | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `release-youtubei.sandbox.googleapis.com` | **Expected Original Vendor** | YouTube Music ReVanced, YouTube ReVanced | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `reportingwidget.google.com` | **Expected Original Vendor** | Morphe Google Photos | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `s.youtube.com` | **Expected Original Vendor** | Morphe Google Photos, YouTube Music ReVanced | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `share.redd.it` | **Expected Original Vendor** | Morphe Reddit | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `ssl.gstatic.com` | **Expected Original Vendor** | Morphe Google Photos | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `staging-notifications-pa.sandbox.googleapis.com:443` | **Expected Original Vendor** | Morphe Google Photos, YouTube Music ReVanced, YouTube ReVanced | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `staging-qual-c2paregistration.sandbox.googleapis.com` | **Expected Original Vendor** | Morphe Google Photos | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `staging-qual-qa-notifications-pa.sandbox.googleapis.com:443` | **Expected Original Vendor** | Morphe Google Photos, YouTube Music ReVanced, YouTube ReVanced | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `strava.com` | **Expected Original Vendor** | Strava | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `styles.redditmedia.com` | **Expected Original Vendor** | Morphe Reddit | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `suggestqueries.google.com` | **Expected Original Vendor** | YouTube ReVanced | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `support.google.com` | **Expected Original Vendor** | Morphe Google Photos, YouTube Music ReVanced, YouTube ReVanced | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `support.strava.com` | **Expected Original Vendor** | Strava | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `tessa-labs.strava.com` | **Expected Original Vendor** | Strava | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `test-youtubei.sandbox.googleapis.com` | **Expected Original Vendor** | YouTube Music ReVanced, YouTube ReVanced | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `tiles.strava.com` | **Expected Original Vendor** | Strava | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `tools.google.com` | **Expected Original Vendor** | Morphe Google Photos | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `translate-pa.googleapis.com` | **Expected Original Vendor** | Morphe Reddit | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `translate.googleapis.com` | **Expected Original Vendor** | Morphe Reddit | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `upload.youtube.com` | **Expected Original Vendor** | YouTube Music ReVanced, YouTube ReVanced | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `uytfe.sandbox.google.com` | **Expected Original Vendor** | YouTube Music ReVanced, YouTube ReVanced | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `v.redd.it` | **Expected Original Vendor** | Morphe Reddit | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `www.business.reddit.com` | **Expected Original Vendor** | Morphe Reddit | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `www.google.com` | **Expected Original Vendor** | Morphe Google Photos, Morphe Reddit, Strava, YouTube Music ReVanced, YouTube ReVanced | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `www.google.com.au` | **Expected Original Vendor** | Morphe Reddit | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `www.google.com.ph` | **Expected Original Vendor** | Morphe Reddit | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `www.google.com.sg` | **Expected Original Vendor** | Morphe Reddit | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `www.googleapis.com` | **Expected Original Vendor** | Morphe Google Photos, Morphe Reddit, Strava, YouTube Music ReVanced, YouTube ReVanced | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `www.gstatic.com` | **Expected Original Vendor** | Morphe Google Photos, Morphe Reddit, YouTube Music ReVanced, YouTube ReVanced | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `www.mapbox.com` | **Expected Original Vendor** | Strava | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `www.reddit.com` | **Expected Original Vendor** | Morphe Reddit | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `www.staging.strava.com` | **Expected Original Vendor** | Strava | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `www.strava.com` | **Expected Original Vendor** | Strava | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `www.support.google.com` | **Expected Original Vendor** | Morphe Google Photos | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `www.youtube.com` | **Expected Original Vendor** | Morphe Reddit, YouTube Music ReVanced, YouTube ReVanced | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `youtube.com` | **Expected Original Vendor** | YouTube Music ReVanced, YouTube ReVanced | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `youtubei.googleapis.com` | **Expected Original Vendor** | YouTube Music ReVanced, YouTube ReVanced | **Low** | Authentic core application APIs, authentication, media streaming, and maps |
| `api.github.com` | **Expected Patch Framework** | YouTube Music ReVanced, YouTube ReVanced | **Low** | Open-source repository references, translation dictionaries, and asset hosting |
| `api.morphe.software` | **Expected Patch Framework** | Morphe Reddit | **Low** | Community patch services (SponsorBlock, Dislikes, DeArrow, ReVanced API, Morphe API) |
| `api.revanced.app` | **Expected Patch Framework** | YouTube Music ReVanced, YouTube ReVanced | **Low** | Community patch services (SponsorBlock, Dislikes, DeArrow, ReVanced API, Morphe API) |
| `dearrow.ajay.app` | **Expected Patch Framework** | YouTube ReVanced | **Low** | Community patch services (SponsorBlock, Dislikes, DeArrow, ReVanced API, Morphe API) |
| `morphe.software` | **Expected Patch Framework** | Morphe Reddit | **Low** | Community patch services (SponsorBlock, Dislikes, DeArrow, ReVanced API, Morphe API) |
| `raw.githubusercontent.com` | **Expected Patch Framework** | Morphe Reddit | **Low** | Open-source repository references, translation dictionaries, and asset hosting |
| `returnyoutubedislikeapi.com` | **Expected Patch Framework** | YouTube ReVanced | **Low** | Community patch services (SponsorBlock, Dislikes, DeArrow, ReVanced API, Morphe API) |
| `revanced.app` | **Expected Patch Framework** | Morphe Reddit, YouTube Music ReVanced, YouTube ReVanced | **Low** | Community patch services (SponsorBlock, Dislikes, DeArrow, ReVanced API, Morphe API) |
| `sb.ltn.fi` | **Expected Patch Framework** | YouTube ReVanced | **Low** | Community patch services (SponsorBlock, Dislikes, DeArrow, ReVanced API, Morphe API) |
| `sponsor.ajay.app` | **Expected Patch Framework** | YouTube ReVanced | **Low** | Community patch services (SponsorBlock, Dislikes, DeArrow, ReVanced API, Morphe API) |
| `wiki.sponsor.ajay.app` | **Expected Patch Framework** | YouTube ReVanced | **Low** | Community patch services (SponsorBlock, Dislikes, DeArrow, ReVanced API, Morphe API) |
| `%s` | **Expected Vendor / Infrastructure** | Morphe Google Photos, YouTube Music ReVanced, YouTube ReVanced | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `%s:%d` | **Expected Vendor / Infrastructure** | YouTube Music ReVanced, YouTube ReVanced | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `%sadrevenue.%s` | **Expected Vendor / Infrastructure** | Morphe Reddit | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `%sapp.%s` | **Expected Vendor / Infrastructure** | Morphe Reddit | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `%sattr.%s` | **Expected Vendor / Infrastructure** | Morphe Reddit | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `%scdn-%ssettings.%s` | **Expected Vendor / Infrastructure** | Morphe Reddit | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `%scdn-%stestsettings.%s` | **Expected Vendor / Infrastructure** | Morphe Reddit | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `%sconversions.%s` | **Expected Vendor / Infrastructure** | Morphe Reddit | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `%simpression.%s` | **Expected Vendor / Infrastructure** | Morphe Reddit | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `%sinapps.%s` | **Expected Vendor / Infrastructure** | Morphe Reddit | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `%slaunches.%s` | **Expected Vendor / Infrastructure** | Morphe Reddit | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `%smonitorsdk.%s` | **Expected Vendor / Infrastructure** | Morphe Reddit | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `%sregister.%s` | **Expected Vendor / Infrastructure** | Morphe Reddit | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `'` | **Expected Vendor / Infrastructure** | Morphe Reddit | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `'.` | **Expected Vendor / Infrastructure** | Morphe Reddit | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `(yt3` | **Expected Vendor / Infrastructure** | YouTube ReVanced | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `)` | **Expected Vendor / Infrastructure** | Strava | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `*` | **Expected Vendor / Infrastructure** | YouTube ReVanced | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `.*` | **Expected Vendor / Infrastructure** | YouTube Music ReVanced, YouTube ReVanced | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `.facebook.com` | **Expected Vendor / Infrastructure** | Strava | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `10.0.2.2:8969` | **Expected Vendor / Infrastructure** | Strava | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `127.0.0.1:8787` | **Expected Vendor / Infrastructure** | YouTube Music ReVanced, YouTube ReVanced | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `a-` | **Expected Vendor / Infrastructure** | Strava | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `a.klaviyo.com` | **Expected Vendor / Infrastructure** | Morphe Google Photos | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `accounts(` | **Expected Vendor / Infrastructure** | YouTube ReVanced | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `admob-gmats.uc.r.appspot.com` | **Expected Vendor / Infrastructure** | YouTube Music ReVanced, YouTube ReVanced | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `aisloplist.com` | **Expected Vendor / Infrastructure** | Morphe Reddit | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `ajax.aspnetcdn.com` | **Expected Vendor / Infrastructure** | Morphe Google Photos | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `amp-reddit-com.cdn.ampproject.org` | **Expected Vendor / Infrastructure** | Morphe Reddit | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `aomedia.org` | **Expected Vendor / Infrastructure** | Morphe Google Photos, Morphe Reddit, Strava, YouTube Music ReVanced, YouTube ReVanced | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `apache.org` | **Expected Vendor / Infrastructure** | Morphe Google Photos | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `api-cdn.yotpo.com` | **Expected Vendor / Infrastructure** | Morphe Google Photos | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `api-events-config-staging.tilestream.net` | **Expected Vendor / Infrastructure** | Strava | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `api-events-staging.tilestream.net` | **Expected Vendor / Infrastructure** | Strava | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `api-iam.intercom.io` | **Expected Vendor / Infrastructure** | Morphe Google Photos | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `api-js.datadome.co` | **Expected Vendor / Infrastructure** | Morphe Google Photos | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `api.giphy.com` | **Expected Vendor / Infrastructure** | Morphe Reddit | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `api.iterable.com` | **Expected Vendor / Infrastructure** | Strava | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `app.checkout.us-central1.gcp.commercetools.com` | **Expected Vendor / Infrastructure** | Morphe Google Photos | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `app.remaker.co.uk` | **Expected Vendor / Infrastructure** | Strava | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `app.whoop.com` | **Expected Vendor / Infrastructure** | Strava | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `appleid.apple.com` | **Expected Vendor / Infrastructure** | Morphe Reddit | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `applink.coros.com` | **Expected Vendor / Infrastructure** | Strava | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `appservice.azureedge.net` | **Expected Vendor / Infrastructure** | Morphe Google Photos | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `arstechnica.com` | **Expected Vendor / Infrastructure** | Morphe Reddit | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `assets-` | **Expected Vendor / Infrastructure** | Morphe Google Photos | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `assets-og2gclcytz9856bpwzytc5ia.assets.us-central1.gcp.4.sandbox.commercetools.app` | **Expected Vendor / Infrastructure** | Morphe Google Photos | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `auto-medienportal.net` | **Expected Vendor / Infrastructure** | Morphe Reddit | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `b.stripecdn.com` | **Expected Vendor / Infrastructure** | Morphe Google Photos | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `business.reddithelp.com` | **Expected Vendor / Infrastructure** | Morphe Reddit | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `c.bing.com` | **Expected Vendor / Infrastructure** | Morphe Google Photos | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `ca.slack-edge.com` | **Expected Vendor / Infrastructure** | Strava | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `caliber.app.link` | **Expected Vendor / Infrastructure** | Strava | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `capcut-yt.onelink.me` | **Expected Vendor / Infrastructure** | Morphe Google Photos | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `caranddriver.com` | **Expected Vendor / Infrastructure** | Morphe Reddit | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `cdn-widget-assets.yotpo.com` | **Expected Vendor / Infrastructure** | Morphe Google Photos | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `cdn-widgetsrepository.yotpo.com` | **Expected Vendor / Infrastructure** | Morphe Google Photos | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `cdn.arstechnica.net` | **Expected Vendor / Infrastructure** | Morphe Reddit | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `cdn.cookielaw.org` | **Expected Vendor / Infrastructure** | Morphe Google Photos | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `cdn.example.com` | **Expected Vendor / Infrastructure** | Morphe Reddit | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `cdn.jotfor.ms` | **Expected Vendor / Infrastructure** | Morphe Google Photos | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `cdn.vox-cdn.com` | **Expected Vendor / Infrastructure** | Morphe Reddit | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `charts.camelcamelcamel.com` | **Expected Vendor / Infrastructure** | Morphe Google Photos | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `checkout.us-central1.gcp.commercetools.com` | **Expected Vendor / Infrastructure** | Morphe Google Photos | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `cipa.jp` | **Expected Vendor / Infrastructure** | Morphe Google Photos | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `client.px-cloud.net` | **Expected Vendor / Infrastructure** | Morphe Google Photos | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `cloudfront-staging.tilestream.net` | **Expected Vendor / Infrastructure** | Strava | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `club.runna.com` | **Expected Vendor / Infrastructure** | Strava | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `com.reddit` | **Expected Vendor / Infrastructure** | Morphe Reddit | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `com.reddit,` | **Expected Vendor / Infrastructure** | Morphe Reddit | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `corporate.walmart.com` | **Expected Vendor / Infrastructure** | Morphe Google Photos | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `crbug.com` | **Expected Vendor / Infrastructure** | Morphe Google Photos, Morphe Reddit, YouTube Music ReVanced, YouTube ReVanced | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `creativecommons.org` | **Expected Vendor / Infrastructure** | Morphe Reddit | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `cs.android.com` | **Expected Vendor / Infrastructure** | Strava | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `customlink.com` | **Expected Vendor / Infrastructure** | Morphe Reddit | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `cv.iptc.org` | **Expected Vendor / Infrastructure** | Morphe Google Photos | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `d3nn82uaxijpm6.cloudfront.net` | **Expected Vendor / Infrastructure** | Strava | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `dashif.org` | **Expected Vendor / Infrastructure** | Morphe Google Photos, Morphe Reddit, YouTube Music ReVanced, YouTube ReVanced | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `dearrow-thumb.ajay.app` | **Expected Vendor / Infrastructure** | YouTube ReVanced | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `default.url` | **Expected Vendor / Infrastructure** | Morphe Google Photos, YouTube Music ReVanced, YouTube ReVanced | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `developer.android.com` | **Expected Vendor / Infrastructure** | Morphe Google Photos, Morphe Reddit, ReVanced Manager Plus, Strava, YouTube Music ReVanced, YouTube ReVanced | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `developer.apple.com` | **Expected Vendor / Infrastructure** | Morphe Google Photos, Morphe Reddit, Strava, YouTube Music ReVanced, YouTube ReVanced | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `developers.facebook.com` | **Expected Vendor / Infrastructure** | Strava | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `dgalywyr863hv.cloudfront.net` | **Expected Vendor / Infrastructure** | Strava | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `dontkillmyapp.com` | **Expected Vendor / Infrastructure** | YouTube Music ReVanced, YouTube ReVanced | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `drive-thirdparty` | **Expected Vendor / Infrastructure** | Morphe Google Photos | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `etherscan.io` | **Expected Vendor / Infrastructure** | Morphe Reddit | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `example.com` | **Expected Vendor / Infrastructure** | Morphe Reddit, Strava | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `facebook.com` | **Expected Vendor / Infrastructure** | Strava | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `fast.a.klaviyo.com` | **Expected Vendor / Infrastructure** | Morphe Google Photos | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `fblitho.com` | **Expected Vendor / Infrastructure** | YouTube Music ReVanced, YouTube ReVanced | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `form.jotform.com` | **Expected Vendor / Infrastructure** | Morphe Google Photos | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `forms.gle` | **Expected Vendor / Infrastructure** | Morphe Google Photos | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `forms.gle)` | **Expected Vendor / Infrastructure** | Morphe Reddit | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `fsf.org` | **Expected Vendor / Infrastructure** | Morphe Reddit | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `fujifilmprint.bynder.com` | **Expected Vendor / Infrastructure** | Morphe Google Photos | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `fujifilmsandbox.bynder.com` | **Expected Vendor / Infrastructure** | Morphe Google Photos | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `g.co` | **Expected Vendor / Infrastructure** | Morphe Google Photos, Morphe Reddit, Strava, YouTube Music ReVanced, YouTube ReVanced | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `g1-trigger.localhost` | **Expected Vendor / Infrastructure** | Morphe Google Photos | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `getgymbros.com` | **Expected Vendor / Infrastructure** | Strava | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `giphy.com` | **Expected Vendor / Infrastructure** | Morphe Reddit | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `github.blog` | **Expected Vendor / Infrastructure** | Morphe Reddit | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `github.githubassets.com` | **Expected Vendor / Infrastructure** | Morphe Reddit | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `gmsb` | **Expected Vendor / Infrastructure** | Morphe Google Photos | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `go` | **Expected Vendor / Infrastructure** | YouTube Music ReVanced, YouTube ReVanced | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `go.fitbod.link` | **Expected Vendor / Infrastructure** | Strava | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `goo.gl` | **Expected Vendor / Infrastructure** | Morphe Reddit, Strava, YouTube Music ReVanced, YouTube ReVanced | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `goo.gle` | **Expected Vendor / Infrastructure** | Morphe Google Photos, Morphe Reddit, ReVanced Manager Plus, Strava | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `graph-video.%s` | **Expected Vendor / Infrastructure** | Strava | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `graph.%s` | **Expected Vendor / Infrastructure** | Strava | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `graph.keepa.com` | **Expected Vendor / Infrastructure** | Morphe Google Photos | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `graphql-router-` | **Expected Vendor / Infrastructure** | Strava | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `grayjay.app` | **Expected Vendor / Infrastructure** | YouTube ReVanced | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `hcaptcha.com` | **Expected Vendor / Infrastructure** | Morphe Google Photos | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `help-printstore.googlephotos.fujifilm.com` | **Expected Vendor / Infrastructure** | Morphe Google Photos | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `help.walmart.com` | **Expected Vendor / Infrastructure** | Morphe Google Photos | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `hevy.com` | **Expected Vendor / Infrastructure** | Strava | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `i.ytimg.com` | **Expected Vendor / Infrastructure** | YouTube ReVanced | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `iamcache.braze` | **Expected Vendor / Infrastructure** | Strava | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `images` | **Expected Vendor / Infrastructure** | YouTube ReVanced | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `ingesteer.services-prod.nsvcs.net` | **Expected Vendor / Infrastructure** | Morphe Google Photos | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `intercom.help` | **Expected Vendor / Infrastructure** | Strava | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `internet-up-realtime.intercom-messenger.com` | **Expected Vendor / Infrastructure** | Morphe Google Photos | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `ipfs.io` | **Expected Vendor / Infrastructure** | Morphe Reddit | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `iptc.org` | **Expected Vendor / Infrastructure** | Morphe Google Photos | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `iquilezles.org` | **Expected Vendor / Infrastructure** | YouTube ReVanced | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `je.fit` | **Expected Vendor / Infrastructure** | Strava | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `join.runna.com` | **Expected Vendor / Infrastructure** | Strava | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `js.intercomcdn.com` | **Expected Vendor / Infrastructure** | Morphe Google Photos | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `js.monitor.azure.com` | **Expected Vendor / Infrastructure** | Morphe Google Photos | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `jsoup.org` | **Expected Vendor / Infrastructure** | Morphe Reddit | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `keepa.com` | **Expected Vendor / Infrastructure** | Morphe Google Photos | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `kotl.in` | **Expected Vendor / Infrastructure** | Morphe Reddit, Strava | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `learn.more.url` | **Expected Vendor / Infrastructure** | Morphe Reddit | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `libretube.dev` | **Expected Vendor / Infrastructure** | YouTube ReVanced | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `license` | **Expected Vendor / Infrastructure** | Morphe Reddit | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `localhost` | **Expected Vendor / Infrastructure** | Morphe Reddit | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `localhost:8969` | **Expected Vendor / Infrastructure** | Strava | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `m.stripe.network` | **Expected Vendor / Infrastructure** | Morphe Google Photos | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `matrix` | **Expected Vendor / Infrastructure** | Morphe Reddit | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `media.giphy.com` | **Expected Vendor / Infrastructure** | Morphe Reddit | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `media.wired.com` | **Expected Vendor / Infrastructure** | Morphe Reddit | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `mediation.goog` | **Expected Vendor / Infrastructure** | YouTube Music ReVanced, YouTube ReVanced | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `members.onepeloton.com` | **Expected Vendor / Infrastructure** | Strava | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `motra.com` | **Expected Vendor / Infrastructure** | Strava | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `my.lifetime.life` | **Expected Vendor / Infrastructure** | Strava | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `myaccount(` | **Expected Vendor / Infrastructure** | YouTube ReVanced | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `newpipe.net` | **Expected Vendor / Infrastructure** | YouTube ReVanced | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `nextdoor.com` | **Expected Vendor / Infrastructure** | Morphe Reddit | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `ns.adobe.com` | **Expected Vendor / Infrastructure** | Morphe Google Photos, Morphe Reddit, ReVanced Manager Plus, Strava, YouTube Music ReVanced, YouTube ReVanced | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `ns.useplus.org` | **Expected Vendor / Infrastructure** | Morphe Google Photos | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `otlp.googlephotos.fujifilm.com` | **Expected Vendor / Infrastructure** | Morphe Google Photos | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `p.yotpoapi.com` | **Expected Vendor / Infrastructure** | Morphe Google Photos | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `pagead2.googlesyndication.com` | **Expected Vendor / Infrastructure** | Morphe Reddit, Strava, YouTube Music ReVanced, YouTube ReVanced | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `photos.app.goo.gl` | **Expected Vendor / Infrastructure** | Morphe Google Photos | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `picsum.photos` | **Expected Vendor / Infrastructure** | Strava | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `pipepipe.dev` | **Expected Vendor / Infrastructure** | YouTube ReVanced | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `prints.googlephotos.fujifilm.com` | **Expected Vendor / Infrastructure** | Morphe Google Photos | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `privacyportal-eu.onetrust.com` | **Expected Vendor / Infrastructure** | Morphe Google Photos | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `publicobject.com` | **Expected Vendor / Infrastructure** | Strava | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `purl.org` | **Expected Vendor / Infrastructure** | Morphe Google Photos | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `redditforcommunity.com` | **Expected Vendor / Infrastructure** | Morphe Reddit | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `report.clarity.ms` | **Expected Vendor / Infrastructure** | Morphe Google Photos | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `res.cloudinary.com` | **Expected Vendor / Infrastructure** | Morphe Google Photos | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `returnyoutubedislike.com` | **Expected Vendor / Infrastructure** | YouTube ReVanced | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `s3` | **Expected Vendor / Infrastructure** | Strava | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `safety.google` | **Expected Vendor / Infrastructure** | Morphe Google Photos | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `scalar-staging.riot.im` | **Expected Vendor / Infrastructure** | Morphe Reddit | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `scalar-staging.vector.im` | **Expected Vendor / Infrastructure** | Morphe Reddit | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `scalar.vector.im` | **Expected Vendor / Infrastructure** | Morphe Reddit | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `schemas.microsoft.com` | **Expected Vendor / Infrastructure** | Morphe Google Photos, YouTube Music ReVanced, YouTube ReVanced | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `scripts.clarity.ms` | **Expected Vendor / Infrastructure** | Morphe Google Photos | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `sdk.iad-01.braze.com` | **Expected Vendor / Infrastructure** | Strava | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `server-side-tagging-xls6abnoha-uc.a.run.app` | **Expected Vendor / Infrastructure** | Morphe Google Photos | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `service-` | **Expected Vendor / Infrastructure** | Morphe Google Photos | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `service-i6pksvbxa4qs0g7vlyi3elrq.us-central1.gcp.4.sandbox.commercetools.app` | **Expected Vendor / Infrastructure** | Morphe Google Photos | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `shealth.samsung.com` | **Expected Vendor / Infrastructure** | Strava | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `sigr-hub-designer-prod-centralus-001.service.signalr.net` | **Expected Vendor / Infrastructure** | Morphe Google Photos | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `sigr-hub-designer-prod-westeurope-001.service.signalr.net` | **Expected Vendor / Infrastructure** | Morphe Google Photos | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `sondheim.braze.com` | **Expected Vendor / Infrastructure** | Strava | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `stackoverflow.blog` | **Expected Vendor / Infrastructure** | Morphe Reddit | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `stackoverflow.com` | **Expected Vendor / Infrastructure** | YouTube Music ReVanced, YouTube ReVanced | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `stapecdn.com` | **Expected Vendor / Infrastructure** | Morphe Google Photos | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `static-forms.klaviyo.com` | **Expected Vendor / Infrastructure** | Morphe Google Photos | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `static-tracking.klaviyo.com` | **Expected Vendor / Infrastructure** | Morphe Google Photos | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `static.intercomassets.com` | **Expected Vendor / Infrastructure** | Morphe Google Photos | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `static.klaviyo.com` | **Expected Vendor / Infrastructure** | Morphe Google Photos | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `sthubimageprodusc001.blob.core.windows.net` | **Expected Vendor / Infrastructure** | Morphe Google Photos | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `strava.zendesk.com` | **Expected Vendor / Infrastructure** | Strava | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `support.reddithelp.com` | **Expected Vendor / Infrastructure** | Morphe Reddit | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `t.zepp.com` | **Expected Vendor / Infrastructure** | Strava | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `techcrunch.com` | **Expected Vendor / Infrastructure** | Morphe Reddit | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `telemetry.ffvoyager.dev` | **Expected Vendor / Infrastructure** | Morphe Google Photos | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `telemetry.prod.ffvoyager.dev` | **Expected Vendor / Infrastructure** | Morphe Google Photos | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `thebookofshaders.com` | **Expected Vendor / Infrastructure** | YouTube ReVanced | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `tools.android.com` | **Expected Vendor / Infrastructure** | Strava | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `undefined` | **Expected Vendor / Infrastructure** | Morphe Reddit | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `upload.fujifilmesys.com` | **Expected Vendor / Infrastructure** | Morphe Google Photos | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `us-zipcode.api.smartystreets.com` | **Expected Vendor / Infrastructure** | Morphe Google Photos | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `via.placeholder.com` | **Expected Vendor / Infrastructure** | ReVanced Manager Plus | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `vignette.wikia.nocookie.net` | **Expected Vendor / Infrastructure** | Strava | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `wa.me` | **Expected Vendor / Infrastructure** | Morphe Google Photos | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `weatherkit.apple.com` | **Expected Vendor / Infrastructure** | Strava | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `widget.intercom.io` | **Expected Vendor / Infrastructure** | Morphe Google Photos | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `withpersona.com` | **Expected Vendor / Infrastructure** | Morphe Reddit | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `www` | **Expected Vendor / Infrastructure** | YouTube Music ReVanced, YouTube ReVanced | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `www.` | **Expected Vendor / Infrastructure** | Morphe Google Photos | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `www.aiim.org` | **Expected Vendor / Infrastructure** | Morphe Google Photos | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `www.apache.org` | **Expected Vendor / Infrastructure** | Morphe Reddit | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `www.braze.com` | **Expected Vendor / Infrastructure** | Strava | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `www.ccil.org` | **Expected Vendor / Infrastructure** | Morphe Reddit | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `www.clarity.ms` | **Expected Vendor / Infrastructure** | Morphe Google Photos | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `www.com` | **Expected Vendor / Infrastructure** | YouTube Music ReVanced, YouTube ReVanced | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `www.crisistextline.org` | **Expected Vendor / Infrastructure** | Morphe Reddit | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `www.cvs.com` | **Expected Vendor / Infrastructure** | Morphe Google Photos | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `www.example.com` | **Expected Vendor / Infrastructure** | Morphe Reddit, Strava, YouTube Music ReVanced, YouTube ReVanced | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `www.facebook.com` | **Expected Vendor / Infrastructure** | YouTube ReVanced | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `www.fitbit.com` | **Expected Vendor / Infrastructure** | Strava | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `www.fujifilm.com` | **Expected Vendor / Infrastructure** | Morphe Google Photos | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `www.getstream.io` | **Expected Vendor / Infrastructure** | Strava | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `www.google.ca` | **Expected Vendor / Infrastructure** | Morphe Google Photos, Morphe Reddit | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `www.google.co.in` | **Expected Vendor / Infrastructure** | Morphe Reddit | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `www.google.co.nl` | **Expected Vendor / Infrastructure** | Morphe Reddit | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `www.google.co.uk` | **Expected Vendor / Infrastructure** | Morphe Reddit | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `www.google.de` | **Expected Vendor / Infrastructure** | Morphe Reddit | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `www.google.fr` | **Expected Vendor / Infrastructure** | Morphe Reddit | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `www.googleadservices.com` | **Expected Vendor / Infrastructure** | Morphe Reddit, Strava, YouTube Music ReVanced, YouTube ReVanced | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `www.googletagmanager.com` | **Expected Vendor / Infrastructure** | Morphe Google Photos | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `www.ifit.com` | **Expected Vendor / Infrastructure** | Strava | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `www.npes.org` | **Expected Vendor / Infrastructure** | Morphe Google Photos | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `www.placebear.com` | **Expected Vendor / Infrastructure** | Morphe Reddit | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `www.printing.ne.jp` | **Expected Vendor / Infrastructure** | Morphe Google Photos | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `www.recaptcha.net` | **Expected Vendor / Infrastructure** | Morphe Reddit, Strava | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `www.reddithelp.com` | **Expected Vendor / Infrastructure** | Morphe Reddit | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `www.redditinc.com` | **Expected Vendor / Infrastructure** | Morphe Reddit | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `www.redditstatic.com` | **Expected Vendor / Infrastructure** | Morphe Reddit | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `www.theverge.com` | **Expected Vendor / Infrastructure** | Morphe Reddit | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `www.tiktok.com` | **Expected Vendor / Infrastructure** | Morphe Reddit | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `www.time.com` | **Expected Vendor / Infrastructure** | Morphe Reddit | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `www.topografix.com` | **Expected Vendor / Infrastructure** | Strava | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `www.twitter.com` | **Expected Vendor / Infrastructure** | YouTube ReVanced | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `www.unicode.org` | **Expected Vendor / Infrastructure** | Morphe Reddit | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `www.walgreens.com` | **Expected Vendor / Infrastructure** | Morphe Google Photos | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `www.wired.com` | **Expected Vendor / Infrastructure** | Morphe Reddit | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `www.zendesk.com` | **Expected Vendor / Infrastructure** | Strava | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `www.zwift.com` | **Expected Vendor / Infrastructure** | Strava | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `x` | **Expected Vendor / Infrastructure** | Morphe Google Photos, YouTube Music ReVanced, YouTube ReVanced | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `xerces.apache.org` | **Expected Vendor / Infrastructure** | Morphe Google Photos | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `xml.org` | **Expected Vendor / Infrastructure** | Morphe Google Photos, Morphe Reddit, YouTube Music ReVanced, YouTube ReVanced | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `xmlpull.org` | **Expected Vendor / Infrastructure** | Morphe Google Photos, Morphe Reddit, Strava | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `your-server.example.com` | **Expected Vendor / Infrastructure** | Strava | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `youtrack.jetbrains.com` | **Expected Vendor / Infrastructure** | Morphe Google Photos, Morphe Reddit, ReVanced Manager Plus, Strava, YouTube ReVanced | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `youtu.be` | **Expected Vendor / Infrastructure** | YouTube ReVanced | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `youtubei` | **Expected Vendor / Infrastructure** | YouTube Music ReVanced, YouTube ReVanced | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `yt4.ggpht.com` | **Expected Vendor / Infrastructure** | YouTube ReVanced | **Low** | Standard web standards, CDN routing, or vendor infrastructure |
| `ytdlnis.org` | **Expected Vendor / Infrastructure** | YouTube ReVanced | **Low** | Standard web standards, CDN routing, or vendor infrastructure |

---

## 2. In-Depth Domain Analysis

### 2.1 Unofficial Infrastructure & Supply-Chain Endpoints (High / Medium Risk)

* **`vanced.to`**:
  * **Observed In**: ReVanced Manager Plus
  * **Endpoints**: `https://vanced.to/apps-v3.json`, `https://vanced.to/revanced-apps.json`, `https://vanced.to/donate-redir`
  * **Risk Assessment**: Acts as the central command server for ReVanced Manager Plus. Serves dynamic JSON manifests pointing to third-party prebuilt APK binaries. Lacks cryptographic hash or signing key verification.

* **`github.com/vancedto`**:
  * **Observed In**: ReVanced Manager Plus, vanced.to release manifests
  * **Risk Assessment**: Distribution repository where prebuilt patched binaries are hosted and downloaded. Not affiliated with official ReVanced or Morphe GitHub organisations.

### 2.2 Community Patch Infrastructure (Expected / Low Risk)

* **`api.revanced.app`** (`v4`, `v5`): Official ReVanced configuration and update announcement API.
* **`sponsor.ajay.app`**: Legitimate, open-source crowdsourced SponsorBlock database API.
* **`returnyoutubedislikeapi.com`**: Legitimate public YouTube dislike restore metrics endpoint.
* **`dearrow.ajay.app`**: Legitimate crowdsourced video title and thumbnail replacement API.
* **`api.morphe.software`** (`v2`): Official Morphe patch settings and version management API.
* **`api.redgifs.com`**: Used by Reddit client patch to authenticate and fetch animated media directly.
* **`api.deepl.com`**: Used by Morphe Reddit for on-demand comment translation.

### 2.3 Original Vendor Endpoints (Expected / Low Risk)

* **Google / YouTube / Photos**: `youtubei.googleapis.com`, `googlevideo.com`, `lh3.googleusercontent.com`.
* **Reddit**: `oauth.reddit.com`, `gateway.reddit.com`, `styles.redditmedia.com`.
* **Strava**: `www.strava.com/api/v3/`, `analytics.strava.com`, `api.mapbox.com`.

### 2.4 Unexplained / Malicious Traffic Findings

* **C2 & Bot Traffic**: Zero Telegram bots (`api.telegram.org`), Discord webhooks (`discord.com/api/webhooks`), or IRC/custom TCP C2 protocols were identified in any of the 6 APKs.
* **Adware Networks**: Zero aggressive third-party ad networks (AppLovin, IronSource, UnityAds) were injected into any of the applications.

---

**Navigation**: [Root Repository README](../README.md) | [Audit Summary](00-summary.md) | [Hash Ledger](01-hashes.csv) | [Manager Teardown](02-manager.md) | [Patch Provenance](03-patches.md) | [Malware Report](05-malware-report.md)
