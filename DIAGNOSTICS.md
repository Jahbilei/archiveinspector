# Diagnostics reference

Every diagnostic string the app can report, what it means, and how to fix it.

---

## How to see diagnostics

**Transfer tab → tap the ⓘ icon in the header.**

A dialog appears with:
Installed: true/false
Running: true/false
Perm: true/false
Version: <number>
Uid: <number>
Service: bound / NULL
SvcStatus: uid=<number>


Tap **Copy** to send it to someone (or paste it into a bug report).

---

## Reading the diagnostic block

| Line | Expected | Meaning if wrong |
|---|---|---|
| `Installed` | `true` | The Shizuku APK isn't visible to Archive Inspector. Reinstall Shizuku. |
| `Running` | `true` | Shizuku itself isn't started. Open the Shizuku app and start it. |
| `Perm` | `true` | Archive Inspector isn't authorized in Shizuku. Fix below. |
| `Version` | `13.5.4` (or close) | If `13.6.0`, downgrade. If `-1`, Shizuku isn't running. |
| `Uid` | usually `2000` | Shell user. If `-1`, Shizuku isn't running. |
| `Service` | `bound` | `NULL` means the user service failed to spawn. Fix below. |
| `SvcStatus` | `uid=2000` | If error text, the shell service crashed. Restart the app. |

---

## Status strings in the header

When a pane shows **Empty**, the header subtitle tells you why.

| Status string | Meaning | Fix |
|---|---|---|
| `Shizuku not installed` | The Shizuku package isn't visible. | Install Shizuku from github.com/RikkaApps/Shizuku/releases |
| `Shizuku not running` | Shizuku APK is installed but not started. | Open the Shizuku app and start it (wireless ADB or root). |
| `Permission not granted` | App isn't authorized. | Open Shizuku → Authorized applications → toggle **Archive Inspector** ON. If it's not listed, open the app first to trigger a permission request. |
| `Service bind failed` | Shizuku couldn't spawn the shell user service. | **Most common cause: Shizuku 13.6.0.** Downgrade to 13.5.4 from GitHub releases. Force-stop the app, relaunch. |
| `Path does not exist (from service)` | The bookmark path is wrong. | Open the bookmark, verify the path matches what a file manager sees. |
| `Path exists but is not a directory` | The bookmark points at a file, not a folder. | Edit the bookmark — remove the trailing filename. |
| `Directory is empty` | The folder really has no files. | Create a placeholder in-game. |
| `IPC error: <message>` | A real bug. The message is the exception. | Screenshot it and send it. |
| `OK · N entries` | Working. | Move on. |
| `No target bookmark selected` | You haven't picked a right-pane folder. | Tap the target pill → pick or add a bookmark. |

---

## Permission not granted — full fix

1. Open the **Shizuku** app.
2. Look for **Authorized applications** (or similar — the wording varies by version).
3. Find **Archive Inspector**. Toggle it ON.
4. If it's not in the list at all:
   - Open Archive Inspector.
   - Go to the **Transfer** tab.
   - If Shizuku is running, a permission prompt should appear.
   - Tap Allow.
5. Force-stop Archive Inspector from Settings → Apps, then relaunch.

Reinstalling Archive Inspector revokes the permission silently. This is the most common cause after an update.

---

## Service bind failed — full fix

Ranked by likelihood:

**1. Wrong Shizuku version.** Version **13.6.0** has a binding bug that affects most devices, especially MediaTek. Check the diagnostic block's `Version` field. If it's 13.6.0:

- Uninstall the current Shizuku.
- Install **13.5.4** from https://github.com/RikkaApps/Shizuku/releases
- Restart Shizuku via wireless debugging or root.
- Force-stop Archive Inspector, relaunch.

**2. Stale user service.** Shizuku caches user services between app launches. Force-stop Archive Inspector from Settings → Apps, then relaunch.

**3. Shizuku needs a restart.** Open Shizuku, stop, then start again.

**4. Reboot.** Last resort but usually effective.

---

## File timestamps look wrong

Symptoms: file shows "5d ago" in Transfer even though you just modified it in-game.

**Step 1.** Long-press the pane header (the "Working" or bookmark name). The debug dialog shows three timestamps for the first file:
/storage/emulated/0/Android/data/... exists=true mtime=...
/data/media/0/Android/data/... exists=true mtime=...
/mnt/pass_through/0/emulated/0/... exists=true mtime=...


**Step 2.** Interpret:

| What you see | Meaning | Action |
|---|---|---|
| All three show the same time | File genuinely wasn't modified. | Check the game actually saved. |
| One shows a newer time | App already uses the newest. Correct. | No action. |
| `/data/media/0/...` shows `exists=false` | Shell can't reach the raw path (SELinux). | File a bug — you're on a restricted ROM. |
| All three show an old time but MT Manager shows new | Real bug. | Send the debug text. |

**Step 3.** If only `/mnt/pass_through/0/...` is correct, the fix is already in the code (candidatePaths tries it last). Restart the app.

The Transfer list is sorted newest-first. If you thought a file was stale, you may have been reading the wrong row.

---

## Files won't save to Downloads

Output folder issues.

| Symptom | Fix |
|---|---|
| Save says "Storage permission needed" | Android 8/9 only. Grant the permission when prompted, or Settings → Apps → Archive Inspector → Permissions → Storage → Allow. |
| Save says "Cannot create folder" | Change the folder name in Settings → Output Folder. Some names are rejected by MIUI. |
| Save says "MediaStore insert failed" | Folder name is invalid. Try letters and numbers only. |
| Save succeeds but file isn't visible in Files app | Pull down to refresh in Files. MediaStore indexes lazily. |
| File is in a weird location | It's in `Downloads/<your folder name>/`. Not the root of Downloads. |

---

## Shizuku banner appears on Transfer

The banner means Shizuku isn't detected on the device at all.

Fix:
1. Install Shizuku from https://github.com/RikkaApps/Shizuku/releases
2. **Use version 13.5.4.** Not 13.6.0.
3. Open Shizuku, start it.
4. Return to Archive Inspector. Tap the **Recheck** button in the banner.

If the banner persists after Recheck, tap ⓘ in the header and send the diagnostic block.

---

## App crashes on launch

1. Open Archive Inspector.
2. If it crashes immediately, go to **Settings → About** (may need to relaunch).
3. Tap **Share latest crash report**.
4. Send the file.

If the app can't stay open at all, connect the phone to a PC with USB debugging on and run:
adb logcat -d | findstr /i archiveinspector


Paste the output. The crash stack will be in there.

---

## Wrong password found

Symptoms: file unlocks with a password the app found, but the JSON looks garbled or the game rejects it.

Cause: rare false positive during the search. The app tries to confirm by checking the plaintext looks like JSON, but ~1 in 256 decryptions produce valid-looking padding by chance.

Fix:
1. Open the **Decrypt** tab.
2. Tap the key icon in the top-right.
3. If the wrong password is in Known Keys, delete it.
4. Try the decrypt again.

The app will retry the full search.

---

## Known Keys got corrupted

If the keys menu shows garbage or the app misbehaves after importing a bad file:

1. Settings → **Reset all app data**
2. Confirm

This wipes settings, keys, history, cache, and output folder choice. Your files on the device are untouched.

---

## App installs but Shizuku permission keeps revoking

This is Android behavior: every reinstall of Archive Inspector revokes Shizuku's grant.

Fix: after every update, open the app → Transfer tab → Allow the permission prompt. If the prompt doesn't appear, open Shizuku → Authorized applications → toggle Archive Inspector ON.

Obtainium updates don't re-prompt automatically.

---

## Transfer is slow

Expected behavior on large folders. Each folder listing is a Shizuku IPC round-trip. A 300-file folder takes a few seconds.

Mitigations:
- Use the recency chips (5m, 30m, 1h) to narrow the list.
- Type in the search box — filters as you type.
- The list is sorted newest-first, so recently-touched files are always at the top.

---

## "Not an ES3 file"

The file doesn't look like an encrypted ES3 save. The app checks:

- File size is a multiple of 16 bytes
- First 16 bytes (the IV) aren't all zeros
- First 16 bytes aren't all identical

If a real game save fails this check, it's either:

- Truncated (transfer interrupted)
- Actually not an ES3 file (wrong file picked)

Try the decrypt again with the original file from the game folder.

---

## "Filename isn't Base64"

The app can't derive the first 3 characters of the password. Causes:

- File was renamed to something that isn't Base64 (e.g. `my design.es3`)
- The Base64 prefix is shorter than 4 characters

Fix: rename the file to its original Base64 name. If you don't have it, the app cannot unlock this file — the first 3 password characters are unrecoverable from the file alone.

Labels in parentheses or brackets are stripped automatically, so `(Agera) <base64>.es3` works fine.

---

## Still stuck?

1. Tap ⓘ in Transfer, **Copy** the diagnostic.
2. Long-press the pane header to see the file timestamp debug.
3. Trigger a crash report: Settings → About → Trigger test crash report.
4. Send both to the maintainer.
