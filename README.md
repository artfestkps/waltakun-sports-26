# WALTAKUN 26 Control Centre

An installable, no-build PWA for the WALTAKUN 26 Sports & Arts Fest. It runs from any static web host and uses a Google Apps Script Web App as its Google Sheets API.

## Start here

1. Create (or open) the event spreadsheet and add the sheets and headers in **[Google Sheets schema](#google-sheets-schema)**.
2. In that spreadsheet choose **Extensions → Apps Script**, replace the generated `Code.gs` with [`apps-script/Code.gs`](apps-script/Code.gs), set the Script Properties described at the top of that file, then deploy it as a **Web app**.
3. Put the deployed `/exec` URL into `API_URL` at the top of [`app.js`](app.js).
4. Serve this folder over HTTPS (GitHub Pages, Netlify, Firebase Hosting, etc.). The first visit enables installation from the browser menu.

When `API_URL` is empty, the app works in a clearly labelled demo mode (password: `demo` for any role). This makes the interface reviewable before the spreadsheet is connected; the real event passwords are not exposed in the published front end.

## Google Sheets schema

Keep the three existing data sheets, with these exact headers in row 1:

| Sheet | Required columns |
|---|---|
| `Students` | `Chest No`, `Name`, `Category`, `Team` |
| `Programs` | `Category`, `Program Name`, `Count` |
| `Candidate List` | `ID`, `Team`, `Category`, `Program Name`, `Chest No`, `Student Name`, `Submission Time`, `Updated Time` |

Add these sheets:

| Sheet | Columns |
|---|---|
| `Results` | `ID`, `Program Name`, `Category`, `First Chest No`, `Second Chest No`, `Third Chest No`, `Status`, `Created Time`, `Updated Time`, `Published Time` |
| `Wall` | `ID`, `Title`, `Image URL`, `Caption`, `Sort Order`, `Enabled`, `Updated Time` |
| `Users` | `Role`, `Team`, `Password Hash`, `Enabled` |

`Count` is the maximum number of candidates **per team, per programme**. `Third Chest No` may be blank. The API will reject candidates outside the captain's own team and prevents duplicate candidate-programme pairs.

### User setup

For first setup, run `seedUsers()` from the Apps Script editor. It writes hashes, not readable passwords, for the credentials supplied in the brief:

| Role | Login name | Password |
|---|---|---|
| Captain | Qiyadah | `QYD001` |
| Captain | Rifadah | `RFD010` |
| Captain | Siyadah | `SYD100` |
| Captain | Wifadah | `WFD000` |
| Admin | Admin | `walsports26` |

Change those values before the event. Do not share the spreadsheet with captains; they only need the web app URL.

## What is included

- Captain sign-in, filtered student chooser, programme quota, saving and editing selections.
- Admin candidate directory by category and a print-focused, programme-selectable candidate list (browser **Print → Save as PDF**).
- Admin result drafting/publishing, with only published results visible to captains.
- A TV-safe display wall that rotates published podiums and optional `Wall` image slides. Open `/wall` on the television browser.
- Offline shell caching, install manifest, and responsive phone/tablet/TV layouts.

## Operational notes

Apps Script is an appropriate lightweight event backend, but it is not a high-concurrency database. The script uses a lock for writes and server-side validation. Export a Sheet backup before the event, and use the Google account owner account only for spreadsheet and Apps Script administration.
