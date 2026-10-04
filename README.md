# Fortify Health – Premix Batch Scanner (version 3)

Mobile web app (PWA) for recording the premix batch available at a mill.

**Mill Code → Scan label (or select vendor and batch) → Enter premix available (kg) → Submit**

## What the app records
| Field | Where it comes from |
| --- | --- |
| Mill Code | Entered once on the home screen (kept on the phone until changed) |
| Premix Vendor | Read from the label, or selected from the list |
| Batch Code | Read from the label, or selected from the list |
| Manufacturing Date | Read from the label (month of production from the sheet when selected from the list) |
| Expiry Date | Read from the label, or filled automatically from the sheet |
| Premix Available (kg) | Entered by the user |

Batch codes, vendors and expiry dates come from the **Premix Testing Results** spreadsheet, tab
**Premix testing (From Jan 2026)**: column C (vendor), column D (batch), column J (expiry), plus column B (month of production)
and column G (Ferrozine status, shown as a warning when not "Cleared").
The list refreshes when the app opens and every 30 minutes while online, or any time with **Refresh Batch Data**.

Checks before submitting: mill code present, batch found in the testing sheet, expiry status (near expiry ≤ 120 days, expired),
label expiry vs sheet expiry, Ferrozine status, quantity valid, duplicate within 15 minutes, and confirmation that label details were checked.
Warnings are saved with the record in the Warnings column.

## Files
| File | Purpose |
| --- | --- |
| `index.html` | The whole app |
| `google-apps-script/Code.gs` | Reads the Premix testing tab and writes records to Google Sheets |
| `manifest.webmanifest`, `sw.js`, `icons/` | Home-screen install and offline support |
| `sample-label.jpg` | Example label for testing |
| `.nojekyll` | Needed by GitHub Pages |

## 1. Update GitHub
Replace all files in the repository with the files in this folder. The app is live at the same link after a minute or two.

## 2. Set up the Google Sheet (replaces the version 2 script)
1. Create a new Google Sheet for records, for example **Premix Scanner Records**.
   (You can keep using the version 2 sheet. Records go to a new tab called **Scanner Records**.)
2. Extensions → Apps Script. Replace all code with `google-apps-script/Code.gs`. Save.
3. In the function list at the top, select **doGet** and click **Run**. Allow access. This lets the script read the Premix testing sheet.
4. Deploy → New deployment (or Manage deployments → Edit → Version: New version if you are updating) → Web app.
   Execute as: **Me**. Who has access: **Anyone**. Deploy and copy the URL ending in `/exec`.
5. In the app: Settings (gear icon) → Google Sheet → paste the URL → **Test Connection** → **Save**.

The person who deploys the script must have access to the Premix Testing Results spreadsheet.
Field staff do not need access to that spreadsheet; the script reads it for them.

**Security:** anyone with the `/exec` URL can read the batch list and add records. Share it only with your team,
and set `ACCESS_KEY` in `Code.gs` with the same key in the app for extra protection.

## Updating
- If the testing sheet columns move, change `SOURCE_COLS` at the top of `Code.gs` and redeploy a new version.
- After changing `index.html`, change `VERSION` in `sw.js` so phones pick up the new app.
