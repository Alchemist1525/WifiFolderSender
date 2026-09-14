# WiFi Folder Sender (Android app)

A small native Android app that uploads a whole folder to your Windows 11
PC over WiFi, using the same server as `wifi_transfer.py` — no changes
needed on the PC side.

## Why a native app instead of the browser

The browser page works, but Android **suspends JavaScript in background or
locked-screen tabs**, so an upload can stall or drop the moment your phone
screen turns off. This app runs as a **foreground service** instead — once
you tap Start Upload, you can lock your phone or switch apps and the
transfer keeps going, with live progress in the notification shade.

It also matches the PC app's newer behavior:
- **Skips files that already exist** on the PC (checked by name + size, in
  one batched request up front — fast even for folders with hundreds of
  files already copied).
- **Resumes interrupted files** from the exact byte they stopped at,
  instead of re-sending the whole file, if a transfer gets cut off.
- Shows a running **"Sent X of Y files"** count, both in the app and in the
  notification.

## Build it — Option A: no Android Studio needed (GitHub Actions)

The project includes a GitHub Actions workflow that builds the APK for you
in the cloud, using just a web browser. You'll end up with an installable
`app-debug.apk` file to download.

1. Create a free GitHub account at https://github.com if you don't have one.
2. Create a new repository (public or private, doesn't matter) — click the
   **+** in the top right → **New repository** → give it any name → **Create**.
3. On the new repo's page, click **uploading an existing file** (or use
   **Add file → Upload files**). Unzip `WifiFolderSender.zip` on your
   computer first, then drag the *contents* of the unzipped folder in
   (the `app` folder, `build.gradle`, `.github` folder, etc. — not the zip
   itself). Commit the changes.
4. Click the **Actions** tab at the top of the repo. A workflow called
   **"Build APK"** should already be running (it starts automatically on
   upload). If it's not running, click it in the list, then **Run workflow**.
5. Wait for the green checkmark (usually 3-5 minutes for the first run).
6. Click into the completed run, scroll to **Artifacts**, and download
   **WifiFolderSender-debug-apk** — it's a zip containing `app-debug.apk`.
7. Get that APK onto your phone any way you like — email it to yourself,
   upload to Google Drive, or use the WiFi Transfer PC→phone download
   feature you already have (put it in the shared folder, download it from
   the phone's browser).
8. On your phone, open the downloaded APK to install it. Android will warn
   about installing from an unknown source the first time — tap **Settings**
   in that prompt and allow it for the app you used to open the file (e.g.
   Files, Chrome, or Drive).

This APK is a debug build, auto-signed by Android's build tools with a
throwaway key — fine for installing on your own phone, just not for
publishing to the Play Store.

## Build it — Option B: with Android Studio

Prefer building locally instead of using GitHub Actions? This is the
traditional route.

1. Install **Android Studio** (free): https://developer.android.com/studio
2. Unzip `WifiFolderSender.zip` somewhere on your PC.
3. In Android Studio: **File → Open** → select the unzipped `WifiFolderSender`
   folder.
4. Let it sync (downloads Gradle + dependencies the first time — needs
   internet). This can take a few minutes.
5. Plug your Android phone into the PC via USB with **USB debugging**
   enabled (Settings → About phone → tap "Build number" 7 times → Developer
   options → USB debugging), or use Android Studio's wireless debugging.
6. Click the green **Run ▶** button, pick your phone, and it installs and
   launches automatically.

You only need to do this once — after that the app just lives on your
phone like any other.

## Use it

1. On the PC, run `wifi_transfer.py` and click **Start Server** — note the
   address it shows, e.g. `192.168.1.42:8000`.
2. On the phone, open **WiFi Folder Sender**.
3. Type the PC's address into the text field (the `http://` part is
   optional, the app adds it automatically).
4. Tap **Choose Folder to Send** and pick the folder.
5. Tap **Start Upload**. A notification appears showing overall progress
   ("Sending files to PC — 3/40 files"); you can lock the phone or switch
   apps and it keeps running. Reopen the app any time to see the per-file
   list and the same "Sent X of Y" count.
6. Tap **Cancel** on the notification to stop an upload in progress.

If the connection drops partway through, just tap **Start Upload** again
with the same folder selected once you're reconnected. Files already fully
copied are skipped instantly, and the one file that was mid-transfer picks
up from where it stopped rather than re-sending everything.

## Notes

- First launch asks for a notification permission (Android 13+) — needed
  so you can see upload progress; allow it.
- The app talks to the PC over plain HTTP (matching the existing server),
  so both devices must be on the same WiFi network.
- If Android Studio prompts to upgrade the Android Gradle Plugin or Kotlin
  version on first sync, accepting the suggested upgrade is fine.
- To change the app icon or name, use Android Studio's **Image Asset**
  tool (right-click `res` → New → Image Asset) — the project currently
  uses a system default icon to keep the source simple.
