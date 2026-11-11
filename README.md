# Shift Hourly Report (PWA)

Hourly Reactor / Plodder tables for Outlook, TFM calculation, summary tables for the
In Process Report and Daily Noodles sheets, and Export to Excel. Works offline once installed.

## The Soap QC tab

The **🧼 Soap QC** tab holds the soap calculator (Free Alkali, FFA, Brine, Chloride in Soap,
Glycerine, EDTA) **inside `index.html` itself** - it is part of the page, not a separate file and
not an iframe. Pick an hour, fill the calculator, then press **📥 Fetch → Plodder table** and the
results are written straight into that hour's Plodder tables (CL%, Gly%, EDTA%, FFA%, FA%) and the
TFM is recalculated automatically.

`soap-only.html` is kept in the repository only as a standalone copy of the calculator page;
the app itself does not load it.

## Write into the factory workbook (Daily Noodles analysis)

The Plodder sheet of this app can be written **straight into** the factory workbook
(`Daily Noodles analysis 10-2026.xlsx`):

1. Press **📥 Open my file & write into it** (Chrome or Edge on the computer).
2. The app picks the sheet that was **updated last** (the newest date inside it) and appends the
   hours of the current shift under the last row that has data - with the same formats as the
   rest of the sheet and a **thick border** around the new block.
3. The file is updated **in place** (no download). You can pick another sheet by hand from the
   **Sheet** list, or press **Open only** / **Write into the file** as two separate steps.

The rows are the same ones the **Export to Excel** produces (one row per hour:
Date, Time, Sample Point, Odor, Moisture %, FFA%, FA%, Chloride%, Glycerine%, EDTA%, EHDP,
TFM%, IV).

## Write into the In Process Report (Reactor)

The **Reactor** sheet of this app can be written straight into the factory report
(`In Process Report of Soap Noodles 10-2026.xlsx`):

1. Press **📥 Open my file & write into it** (Chrome or Edge on the computer).
2. Every day has its own sheet, named `PKOL(85-15) 9-11-2026` (product + day).
   - The sheet of the day **starts at the shift that creates it**:
     **Shift C (01:00-07:00)** → the whole day 01:00-23:00 · **Shift A (09:00-15:00)** →
     09:00-23:00 (its own hours + the hours of shift B) · **Shift B (17:00-23:00)** → 17:00-23:00
     (the day is over).
   - The sheet is a copy of the last day's sheet (same header, shapes, colours, column widths) with
     the hours of the day and the new date; only the shift that writes fills its own numbers, the
     other hours stay empty.
   - **Shift A** and **Shift B** fill their own hours of the sheet of that day; hours that are not on
     the sheet yet are added.
   - Every shift block keeps a **thick line** at its end (07:00 · 15:00 · 23:00), like the factory.
3. The file is updated **in place** (no download). The **Sheet** list lets you pick another day.

## Write into the Swing Noodles sheet

The Swing rows go into the sheet `Swing Noodles <d>-<m>-<yyyy>` of the same workbook:

1. Press **📥 Open my file & write into it**.
2. The sheet **starts at the first bag of the shift that creates it** and ends at **B200**:
   **Shift A** → B1-B200 (the whole day) · **Shift B** → B61-B200 · **Shift C** → B121-B200.
   The numbering on the left is the number of the bag.
3. The shift that writes fills **its own** bags; the rows of the other shifts keep only the
   numbering, the bag and the date.

## The rows always look like the factory ones

Every sheet that is written takes the format of the factory: each column uses the **most common**
look of its own rows, the borders of the block are the factory's (a thick line on the top of the
first row and on the bottom of the last one) and the height of the row is the factory's.
A broken format in the file — a wrong font, a colour, a *text* number format, a wrong height —
can never come into the new rows.

## Files (upload all of them to the root of the repository)

- `index.html` - the app (including the Soap QC calculator)
- `vendor/exceljs.min.js` - the Excel engine used by the "write into your workbook" button
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

1. The Firebase Realtime Database URL is **already configured** in the app, so on every device you only need a **PIN**:
   **Sync** in the header -> type a PIN (same on every device) -> *Save & sync*.
2. Tap **Copy code** and move it to the second device (WhatsApp/email/cable) and paste it there with the **same PIN**.
3. A PIN is **required**: the copy in the cloud is always encrypted (AES-GCM), never plain JSON.
   (You can still paste a different backend - e.g. a JSONBin `URL|master-key` - in the same field.)

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
