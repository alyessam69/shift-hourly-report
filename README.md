# Shift Hourly Report (PWA)

Hourly Reactor / Plodder tables for Outlook, TFM calculation, summary tables for the
In Process Report and Daily Noodles sheets, and Export to Excel. Works offline once installed.

## Files (upload all of them to the root of the repository)

- `index.html` - the app
- `manifest.webmanifest` - install information
- `sw.js` - offline cache
- `icon-192.png`, `icon-512.png`, `icon-maskable-512.png`, `apple-touch-icon.png`, `favicon.png` - icons
- `.nojekyll` - optional (empty file, tells GitHub Pages not to process the files)

## Publish on GitHub Pages

1. Create a new public repository, for example `shift-report`.
2. Upload every file above to the repository root (Add file > Upload files > Commit).
3. Settings > Pages > Build and deployment > Source: "Deploy from a branch", Branch: `main`, folder `/ (root)` > Save.
4. After about a minute the app is at `https://<your-user>.github.io/<repo-name>/`.

## Install on the phone

- Android (Chrome): open the link > menu > "Install app" / "Add to Home screen".
- iPhone (Safari): Share > "Add to Home Screen".

## Notes

- Values are saved in the browser of that phone (localStorage). They stay on the device; clearing the
  browser data for the site erases them.
- Updating the app: replace `index.html` on GitHub and change `CACHE = 'shift-report-v1'` in `sw.js`
  to `v2`, `v3` ... so installed copies pick up the new version (close and reopen the app once or twice).
- Everything is plain static files - no build step and no external libraries.

## Cloud sync (phone <-> PC)

The app works offline and stores everything on the device. To use the **same data on two devices** (phone and PC):

1. Create a free JSON bin at **jsonbin.io** (sign up, *Create new bin*) - copy the **Bin ID** and the **Master Key**.
2. On the first device: **Sync** in the header -> paste the sync code `https://api.jsonbin.io/v3/b/<BIN_ID>|<MASTER_KEY>` and choose a **PIN**, then *Save & sync*.
3. Tap **Copy code** and move it to the second device (WhatsApp/email/cable), paste it there with the **same PIN**.

What happens:
- Every change is pushed to the cloud (encrypted with the PIN) and pulled from it on open and every 45 s.
- Data is merged **per shift** by timestamp, so both devices can work without overwriting each other.
- The cloud only ever holds ciphertext (AES-GCM + PBKDF2), and the app refuses to overwrite data it cannot decrypt.
- Without a sync code the app behaves exactly as before (offline only).

### Using Firebase Realtime Database (recommended)

1. [console.firebase.google.com](https://console.firebase.google.com) -> **Add project** (a Google account is enough).
2. **Build -> Realtime Database -> Create Database** -> pick a location.
3. **Rules** tab -> paste and publish:
```json
{ "rules": { "lab": { ".read": true, ".write": true } } }
```
4. Copy the database URL, e.g. `https://PROJECT-default-rtdb.firebaseio.com` and append a secret path:
   `https://PROJECT-default-rtdb.firebaseio.com/lab/<random-string>.json`
5. In the app: **Sync** -> paste that URL as the *sync code*, choose a **PIN**, *Save & sync*.
   No API key is needed: the secret path + the PIN are the protection.

<!-- build trigger 1793524940 -->
