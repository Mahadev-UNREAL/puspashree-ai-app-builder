# Puspashree AI Studio APK Builder — starter prototype

A mobile-first PWA interface designed for Android Chrome. It can inspect ZIP entry names in the browser, detect common native Android/web project markers, and save project metadata locally.

## Important: this is not yet a compiler

The UI intentionally disables cloud compilation until a real backend is connected. It does not upload ZIP files, execute source code, create APKs, or fake download links. The project ZIP itself is not stored by the current prototype; only its metadata is saved in the browser's local storage.

## Open it on your phone

For a quick preview, upload these files to a static hosting service that supports HTTPS, or deploy the repository with GitHub Pages. The app uses JSZip from jsDelivr for ZIP inspection, so initial inspection requires internet access.

## What must be built next

A secure backend must:
1. Authenticate users and accept uploads with strict size limits.
2. Store ZIPs privately and create a build job.
3. Inspect project contents safely; never execute uploaded scripts on the API server.
4. Start a disposable, isolated cloud runner with CPU, memory, time and network limits.
5. For native Android projects, use a compatible JDK, Android SDK and Gradle.
6. For web projects, build the app and package it with a compatible Android wrapper (for example, Capacitor), then use Gradle to produce an APK.
7. Validate the output APK and return a short-lived download URL.
8. Delete temporary source and artifacts according to a retention policy.

## Suggested API contract

- `POST /api/v1/projects` — multipart ZIP upload; returns `{ "projectId": "...", "detectedType": "native|web|mixed|unknown" }`
- `POST /api/v1/projects/{projectId}/builds` — starts a build; returns `{ "buildId": "...", "status": "queued" }`
- `GET /api/v1/builds/{buildId}` — returns `{ "status": "queued|running|succeeded|failed", "logs": [], "error": null }`
- `GET /api/v1/builds/{buildId}/artifact` — returns a short-lived download URL only after the server confirms a real APK exists.

All endpoints require authenticated access, validation, rate limiting and authorization checks. Keep tokens and signing credentials server-side.

## Free-tier reality

GitHub Actions can be useful for cloud builds, but free usage is limited and depends on account/repository type. Do not enable paid overages. Do not promise unlimited free compilation. Arbitrary ZIP projects may require extra Android SDK downloads, dependencies, storage and build time.

## Detection markers

- Native Android: `settings.gradle` / `settings.gradle.kts`, Android module Gradle files, `AndroidManifest.xml`, Gradle wrapper.
- Web: `package.json` and a supported framework/build configuration.
- Mixed/ambiguous projects must be reviewed rather than guessed.

## Security notes

Uploaded projects are untrusted. Never run their Gradle scripts or npm lifecycle scripts on a long-lived application server. Use ephemeral isolated runners, least-privilege credentials, restricted network access, quotas and timeouts.
