# Video Downloader Pro — Android

An original Android app inspired by the general workflow of the supplied All Video Downloader & Player reference. It does not reuse that app's source code, branding, icons, or proprietary assets.

## Version 1.5.0 features
- Dark four-tab interface: Home, Browser, Downloads, Library.
- In-app WebView browser with back, forward, reload, address navigation, and page-load progress.
- **Find video** scans HTML5 `<video>` and `<source>` elements for media URLs explicitly exposed by the current page.
- Direct media-file downloads through Android DownloadManager, including WebView user-agent and cookies where available.
- Direct-file download status, progress, cancel/remove, and Play/Share for completed files.
- Local video discovery through MediaStore, thumbnail previews, and runtime media-library permission.
- Media3 ExoPlayer playback with HLS and DASH playback support, ±10-second seeking, 0.5×–2× speed, Fit/Fill/Zoom, and fullscreen.
- **HLS/DASH offline downloads** through Media3 DownloadService and DownloadManager for accessible HTTPS `.m3u8` and `.mpd` streams.
- Offline stream progress, downloaded bytes, cache usage, pause/resume, remove, and Play offline.
- WebView cookies are forwarded to stream manifest/segment requests where Android exposes them.

## HLS/DASH offline download: how to use
1. Open a direct HTTPS `.m3u8` (HLS) or `.mpd` (DASH) URL on Home, or use Browser → **Find video** on a page that exposes the stream URL in its HTML5 video elements.
2. Choose **Download offline** in the stream dialog.
3. Allow notification permission when prompted so Android can show download notifications.
4. Open Downloads → **HLS / DASH offline downloads**. Tap Refresh to update the list and progress.
5. When the state is **Completed**, tap **Play offline**.
6. Use **Pause**, **Resume**, or **Remove** to manage the stream.

The first implementation downloads all available renditions/tracks by default. A master playlist can therefore use substantial storage. Offline data stays in app-private storage until the download is removed. Clearing app data or uninstalling the app also deletes offline downloads.

## HLS/DASH limitations
- Only accessible HTTPS, non-DRM streams are supported. The app does not bypass DRM, paywalls, geo-restrictions, authentication requirements, or website rules.
- Some sites require custom headers, expiring tokens, signed URLs, or session behavior beyond WebView cookies; those streams may fail.
- Live or dynamically changing playlists may not be suitable for offline download.
- Background scheduling is not configured. Media3 runs active downloads in a foreground service, but Android battery/background policies can still affect long transfers.
- The cache has no automatic size eviction so completed offline items are not silently removed. Remove downloads you no longer need to free storage.

## Build APK automatically with GitHub Actions (recommended)

This repository includes `.github/workflows/android-apk.yml`. GitHub builds a debug APK in the cloud; Android Studio and the Android SDK are not required on your computer.

1. Sign in to [GitHub](https://github.com/) and create a **new empty repository** (for example, `VideoDownloaderPro`). Do not initialize it with a README or license.
2. Extract this project ZIP on your computer.
3. Upload the **contents** of the `VideoDownloaderPro` folder to the root of your GitHub repository. Make sure `.github/workflows/android-apk.yml` is included. On GitHub, use **Add file → Upload files**, drag the extracted files and folders, then **Commit changes**.
4. Open the repository's **Actions** tab and open the run triggered by your commit.
5. Wait for the job to finish with a green check. Open that run and find **Artifacts** at the bottom; download `VideoDownloaderPro-debug-apk`.
6. Extract the downloaded artifact ZIP. It contains `app-debug.apk`. Transfer it to your Android phone and install it. If Android asks, allow installation from that source.

Every push to `main` or `master` and every pull request targeting those branches triggers a build. You can also run it manually from **Actions → Build Android APK → Run workflow**.

### Create a GitHub Release with the APK attached

After pushing the project, create and push a version tag from a terminal in the repository:

```bash
git tag v1.5.0
git push origin v1.5.0
```

The same workflow builds the APK and attaches `app-debug.apk` to the GitHub Release for that tag. A tag build is still a **debug-signed APK**, not a Play Store production release.

## Build locally (optional)

Requirements: JDK 17, Gradle 8.9, Android SDK Platform 35 and Build Tools 35.0.0.

```bash
gradle --no-daemon clean assembleDebug
```

The output is `app/build/outputs/apk/debug/app-debug.apk`.

## App identity
- Application ID: `com.videodownloader.pro`
- Version: `1.5.0` (versionCode 5)
- Minimum Android: API 23 (Android 6.0)
- Target Android: API 35

## Important implementation status
The project files and Android manifest have been checked for basic integrity, and the Media3 implementation follows the documented DownloadService/DownloadManager APIs. However, a full Android Gradle build and real-device HLS/DASH tests have **not** been run in this environment. Build it with GitHub Actions first, then test with your own accessible, non-DRM HTTPS HLS and DASH streams. If GitHub Actions reports a compile error, share the failing log so it can be corrected.
