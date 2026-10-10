# Archive Inspector

Android app for modding car designs in **CPM**. Scans ES3 save files, unlocks their passwords, and moves designs between accounts via Shizuku.

Personal project. Not on the Play Store. Shared with friends.

---

## Install

### Step 1 — Install Shizuku

Shizuku gives the app permission to read `/Android/data` without root.

- **Download:** [github.com/RikkaApps/Shizuku/releases](https://github.com/RikkaApps/Shizuku/releases)
- **Version:** Use **13.5.4**. Do NOT use 13.6.0 — it has a known binding bug on most devices, especially MediaTek.
- **Start Shizuku** via wireless debugging or root once installed.

### Step 2 — Install Archive Inspector

- Download the latest `app-release.apk` from the [Releases page](../../releases).
- Open it and allow install from unknown sources when prompted.
- Install.

### Step 3 — Get automatic updates

Install **Obtainium** — it turns this repo into an auto-updating app store for one app.

1. Download [Obtainium](https://github.com/ImranR98/Obtainium/releases): 
2. Open Obtainium → tap **+**
3. Paste this URL: https://github.com/Jahbilei/archiveinspector
4. Tap **Add** → tap **Install**

From then on, when a new version is published here, Obtainium notifies you. Tap the notification, tap Install, done.

---

## Safety

- **The app never deletes files under `/Android/data` or `/Android/obb`.** This is enforced in code, not configurable.
- **Rename and Rekey create new files** without touching the source.
- **Move from a game folder is copy-only.** The source is not deleted.
- **Deletes in the working folder show an Undo snackbar** that restores the original bytes.
- **Crash reports stay on your device** until you choose to share them.

---

## Found a bug?

In the app: **Settings → About → Trigger test crash report → Share latest crash report**. Send it to me via [Telegram](https://t.me/@juswahhhhhhhh).

Or open an issue here with:
- What you were doing
- What happened
- What you expected

---

## Credits

- **App and design** — jah
- **ES3 crypto scheme** — alex.tusinean.ro
- **Shizuku integration** — RikkaApps
- **Car name list** — Dev maintained Google Sheet
