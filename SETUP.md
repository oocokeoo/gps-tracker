# GPS Location Tracker — Setup Guide

A simple web app that displays GPS points from a Google Sheet on Google Maps. It includes a **dual-handle time range slider** so you can scrub through the day and watch locations move.

---

## What you get

- `index.html` — the complete web app (single self-contained file)
- Reads data directly from your Google Sheet (no backend, no server)
- Auto-refreshes every 15 seconds → new rows appear on the map without reloading
- Filter by **date**, **device**, and a **time range (minute to minute)**
- **Play** button animates the time window forward so you can see the path unfold
- Multiple devices supported (color-coded), with a legend

---

## Part 1 — Prepare your Google Sheet

**Expected columns (first row must be headers):**

| Timestamp | Lat | Lng | Device |
|-----------|-----|-----|--------|
| 2026-04-18 09:15:00 | 13.7563 | 100.5018 | phone-01 |

Column header names are matched case-insensitively. These aliases also work:
- **Timestamp**: `time`, `datetime`, `date`
- **Lat**: `latitude`
- **Lng**: `lon`, `long`, `longitude`
- **Device**: `name`, `id`, `user` (optional — if missing, everything uses `default`)

### Share the sheet publicly (view-only)

1. Open your sheet → click **Share** (top right).
2. Under "General access", change to **Anyone with the link**.
3. Set role to **Viewer**.
4. Click **Done**.

That's it — no "Publish to web" needed. The app reads via Google's built-in CSV export endpoint.

---

## Part 2 — Get a free Google Maps API key

1. Go to <https://console.cloud.google.com/>.
2. Sign in with your Google account.
3. Create a new project (top bar → "Select a project" → "New project"). Name it anything (e.g., "gps-tracker").
4. In the left menu: **APIs & Services → Library**.
5. Search for **Maps JavaScript API** → click it → **Enable**.
6. Left menu: **APIs & Services → Credentials**.
7. Click **+ CREATE CREDENTIALS → API key**.
8. Copy the key (looks like `AIzaSy...`).

### Restrict the key (important for security)

After creating, click the key name to edit it:
- **Application restrictions** → "Websites" → add your site URL(s):
  - `http://localhost:*` (for testing)
  - `https://<your-github-username>.github.io/*` (for GitHub Pages)
- **API restrictions** → "Restrict key" → select **Maps JavaScript API**.
- **Save**.

### About cost / billing

Google Maps requires a billing account on file, but includes **$200 free credit per month** which covers ~28,000 map loads. For a personal GPS tracker this is effectively free. You can set a budget alert in the Cloud Console to make sure you never get charged unexpectedly.

**If you'd rather not set up billing at all**, I can rebuild this using Leaflet + OpenStreetMap (totally free, no key) — just ask.

---

## Part 3 — Configure the app

1. Open `index.html` in any text editor.
2. Near the top, find the `CONFIG` block:

   ```js
   const GOOGLE_MAPS_API_KEY = "YOUR_GOOGLE_MAPS_API_KEY_HERE";
   const SHEET_ID = "1iouXFBmine7sGY9ViluOPUWTBa6wOqMUmipSQwHhKMo";
   const SHEET_GID = "0";
   ```

3. Replace `YOUR_GOOGLE_MAPS_API_KEY_HERE` with your actual key.
4. `SHEET_ID` is already set to your sheet. `SHEET_GID` is `0` (the first tab) — change it if your data is on a different tab.

---

## Part 4 — Test locally

Just double-click `index.html` to open it in your browser. It should:
- Show the map
- Populate the **Date** and **Device** dropdowns from your sheet
- Draw your GPS points and connect them with lines

If you see "Could not load sheet", re-check the share setting in Part 1.

---

## Part 5 — Deploy to GitHub Pages (free, public URL)

1. Create a GitHub account if you don't have one: <https://github.com/signup>.
2. Create a new repository: <https://github.com/new>
   - Name: `gps-tracker` (or anything)
   - Set to **Public**
   - Check **Add a README**
   - Click **Create repository**.
3. On your new repo page, click **Add file → Upload files**.
4. Drag `index.html` into the browser and click **Commit changes**.
5. Go to **Settings → Pages** (left sidebar).
6. Under "Build and deployment":
   - Source: **Deploy from a branch**
   - Branch: **main** / folder **/ (root)** → **Save**.
7. Wait ~1 minute. Refresh the Pages settings page — you'll see:
   > Your site is live at `https://<your-username>.github.io/gps-tracker/`

8. **Important**: go back to your Google Maps API key and add this URL to the allowed websites (Part 2 step "Restrict the key").

---

## How the time slider works

The slider has **two handles** that define a time window in minutes-of-the-day (00:00 → 23:59). Only points inside `[min, max]` are shown. Drag either handle to change the window.

Click **▶ Play** to slide the window forward automatically (1 minute every 300 ms). The window keeps the same width, so you see a moving view of location over time. Click **⏸ Pause** to stop.

---

## Troubleshooting

**"Could not load sheet"** → The sheet isn't public. Fix share permissions in Part 1.

**"Could not find required columns"** → Your header row doesn't contain recognizable names for Timestamp/Lat/Lng. Rename columns in row 1 to `Timestamp`, `Lat`, `Lng`, `Device`.

**Map is blank / "For development purposes only"** → API key missing, invalid, or hasn't had "Maps JavaScript API" enabled.

**"RefererNotAllowedMapError"** → Your current URL isn't in the API key's allowed websites list. Add it (Part 2).

**No points appear** → Check the **Date** dropdown — the app defaults to the latest date found in your sheet. Also try widening the time slider to 00:00 → 23:59.

**New rows don't appear** → The app fetches the sheet every 15 seconds. Wait, or reload the page. If you want faster refresh, change `REFRESH_INTERVAL_MS` at the top of `index.html`.
