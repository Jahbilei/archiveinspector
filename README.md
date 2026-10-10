# Archive Inspector

Android app for modding car designs in **Car Parking Multiplayer**. Scans ES3 save files, unlocks their passwords, and moves designs between accounts via Shizuku.

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

1. Download Obtainium: [github.com/ImranR98/Obtainium/releases](https://github.com/ImranR98/Obtainium/releases)
2. Open Obtainium → tap **+**
3. Paste this URL: https://github.com/Jahbilei/archiveinspector
4. Tap **Add** → tap **Install**

From then on, when a new version is published here, Obtainium notifies you. Tap the notification, tap Install, done.

---

## First launch

The app walks you through:

1. **First-run setup** — accent color, row density, search threads
2. **Tutorial** — 4 pages covering passwords, the Transfer screen, and how to move a design between accounts

On the Transfer tab, tap the **CPM ES3** bookmark — it's created automatically, no path typing needed.

When you first scan a game folder, Shizuku will ask for permission. Tap **Allow**.

---

## What works without Shizuku

- Scanning zips
- Decrypting ES3 files
- Viewing, editing, exporting decrypted JSON
- Rekeying (changing a file's password/filename)

## What needs Shizuku

- Reading the game's real folder under `/Android/data`
- Transferring files into the game
- Any operation on the CPM ES3 folder

---

## Safety

- **The app never deletes files under `/Android/data` or `/Android/obb`.** This is enforced in code, not configurable.
- **Rename and Rekey create new files** without touching the source.
- **Move from a game folder is copy-only.** The source is not deleted.
- **Deletes in the working folder show an Undo snackbar** that restores the original bytes.
- **Crash reports stay on your device** until you choose to share them.

---

## How passwords work

Every design file is encrypted with a 6-character password.

- **First 3 characters** come from the filename. If you rename a file, the app can no longer derive them.
- **Last 3 characters** are secret. The app searches for them.
- Once found, the last 3 characters are saved as a **known key** — future files from the same account unlock instantly.

**Do not rename ES3 files arbitrarily.** The filename is half the password.

---

## Cross-account design transfer

Moving a design from account A to account B:

1. **Placeholder** — On account B, add a placeholder vinyl to the recipient car. The game creates an ES3 file for it.
2. **Copy filename** — Copy the filename the game just created.
3. **Decrypt** — In the Decrypt tab, pick the design from account A. It unlocks.
4. **Rekey** — Tap Re-encrypt. Enter account B's password and the filename from step 2.
5. **Transfer** — In the Transfer tab, copy the rekeyed file into account B's game folder.
6. **Done** — Open the game. The design is on the recipient car.

The tutorial in-app walks through this step by step.

---

## Troubleshooting

**Folder shows empty even though files exist**
Shizuku permission is missing. Open the Shizuku app → Authorized applications → toggle Archive Inspector ON.

**"Service bind failed"**
You're on Shizuku 13.6.0. Downgrade to 13.5.4.

**File times look wrong**
Long-press a pane header in Transfer to open the diagnostics dialog. If raw paths show different times, FUSE caching is the cause — the app already works around it.

**"Not an ES3 file"**
The file isn't a valid encrypted ES3. Check its header starts with a random 16-byte IV and its length is divisible by 16.

**"Filename isn't Base64"**
The app can't derive the first 3 password characters from this filename. Rename it back to its original form, or attach a known key via the Keys menu.

**Updates fail to install**
Same keystore was used for both versions? versionCode increased? Both must be true. Otherwise uninstall and reinstall.

---

## Found a bug?

In the app: **Settings → About → Trigger test crash report → Share latest crash report**. Send it to me.

Or open an issue here with:
- What you were doing
- What happened
- What you expected

---

## Credits

- **App and design** — jah
- **ES3 crypto scheme** — tusinean.ro
- **Shizuku integration** — RikkaApps
- **Car name list** — Dev maintained Google Sheet
