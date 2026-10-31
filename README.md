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
