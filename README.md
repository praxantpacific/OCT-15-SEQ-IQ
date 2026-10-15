# SequenceIQ — Android build via GitHub Actions

Your app is a single static `index.html` (Tailwind/Chart.js/Font Awesome loaded
from CDNs) wrapped with Capacitor so it can be packaged as a real `.apk`.
**No Android Studio, no local SDK, no npm install on your phone** — GitHub's
servers do the actual build. Termux is only used to push files to GitHub.

Note: since the CSS/JS libraries load from CDNs, the app needs an internet
connection when it runs (same as it does in a browser today).

## One-time setup (in Termux)

```bash
pkg install git -y
cd ~
# unzip the project folder you downloaded from this chat
unzip sequenceiq-capacitor-project.zip
cd sequenceiq-capacitor-project

git init
git add .
git commit -m "Initial Capacitor wrapper"

# create an empty repo on github.com first (in the browser), then:
git remote add origin https://github.com/<your-username>/<your-repo>.git
git branch -M main
git push -u origin main
```

You'll be asked for a username/password — GitHub no longer accepts your
account password for `git push`. Use a **Personal Access Token** instead
(GitHub.com → Settings → Developer settings → Personal access tokens →
generate one with `repo` scope, paste it in place of the password).

## Building the APK

Pushing to `main` automatically triggers the build
(`.github/workflows/build-apk.yml`). To watch/download it:

1. On github.com, open your repo → **Actions** tab.
2. Click the latest **Build Android APK** run and wait for it to go green
   (~3–5 minutes).
3. Scroll to **Artifacts** → download `sequenceiq-debug-apk` (a zip
   containing `app-debug.apk`).
4. On your phone, extract the zip and tap the `.apk` to install (you'll
   need to allow "install unknown apps" for your browser/file manager once).

To trigger a rebuild any time without changing code, go to **Actions →
Build Android APK → Run workflow**.

## Changing the app icon

Add your logo as `resources/icon.png` — **1024×1024px, square PNG** — then
commit and push:

```bash
cp /sdcard/Download/my-logo.png resources/icon.png   # or wherever your file is
git add resources/icon.png
git commit -m "custom app icon"
git push
```

Every build automatically detects `resources/icon.png` and generates all the
Android icon sizes (mdpi → xxxhdpi, round icons, adaptive icons) from it.
Don't try editing files under `android/res/mipmap-*` directly — that whole
`android/` folder is deleted and regenerated from scratch on every build, so
manual edits there are lost immediately.

For a nicer adaptive icon (the shape Android masks into a circle/squircle),
also add `resources/icon-foreground.png` and `resources/icon-background.png`
(both 1024×1024, logo centered in the middle ~66% of the foreground image).
See `resources/README.txt` for details.

## Making changes later

Edit `www/index.html` (that's your whole app), then from Termux:

```bash
git add -A
git commit -m "update app"
git push
```

Every push rebuilds the APK automatically.

## Notes

- This produces a **debug** APK (unsigned, installable directly, fine for
  personal use / sideloading). If you eventually want to publish to the
  Play Store you'll need a signed release build — ask and I can add that
  workflow step (it needs a keystore, which we'd store as a GitHub secret).
- The workflow regenerates the `android/` platform folder fresh on every
  run, so you never need to commit it or touch Gradle/SDK yourself.
# app-icon-seq-iq
