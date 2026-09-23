# Kirana Smart Manager

Single-page kirana/grocery store management app — inventory, POS billing, purchases,
udhaar (customer credit). All data is stored locally in the device/browser
(`localStorage`) — nothing is sent to a server.

## Files

- `www/index.html` — the entire app (HTML + CSS + JS, self-contained).
- `capacitor.config.json` — Capacitor config that wraps `www/index.html` into an
  Android app shell.
- `package.json` — Capacitor dependencies.
- `.github/workflows/build-apk.yml` — GitHub Actions workflow that builds a debug
  APK automatically.

## How the GitHub Action works

1. Push this whole folder to a GitHub repo (root of the repo, so
   `.github/workflows/build-apk.yml` sits at `<repo>/.github/workflows/build-apk.yml`).
2. On every push to `main`/`master` (or manually via "Run workflow"), the Action:
   - installs Node + Java
   - runs `npx cap add android` (creates the native `android/` project — only on
     first run, since after that the folder exists and gets committed... note: this
     repo does NOT commit the android/ folder, so it's regenerated fresh each run,
     which is fine and simpler to maintain)
   - runs `npx cap sync android` to copy `www/index.html` into the native project
   - builds `app-debug.apk` with Gradle
   - uploads it as a workflow artifact — download it from the Actions run page
     under "Artifacts" → `kirana-smart-manager-debug-apk`

## Building locally (optional)

```bash
npm install
npx cap add android
npx cap sync android
cd android
./gradlew assembleDebug
# APK at android/app/build/outputs/apk/debug/app-debug.apk
```

## Notes

- This produces a **debug** APK (unsigned, installable for testing via
  "Install from unknown sources"). For a Play Store release build you'd add a
  signing config and change the Gradle task to `assembleRelease`.
- The app icon/splash are Capacitor defaults — customize via
  `npx cap` resource tooling or by editing `android/app/src/main/res/` if wanted.
