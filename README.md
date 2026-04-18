# GPS Location Tracker

A simple web app that displays GPS points from a Google Sheet on Google Maps, with a time-range slider to scrub through the day and see location movement.

**Live demo:** enable GitHub Pages in this repo's Settings → Pages.

## Features

- Reads data directly from a public Google Sheet (no backend)
- Auto-refreshes every 15 seconds for realtime updates
- Filter by **date** and **device**
- **Dual-handle time range slider** (minute to minute) — drag to scrub through the day
- **▶ Play** button animates the window forward so you can watch the path unfold
- Multiple devices supported with color-coded paths and a legend

## Setup

See [`SETUP.md`](./SETUP.md) for step-by-step instructions:

1. Share your Google Sheet as "Anyone with the link can view"
2. Get a free Google Maps API key
3. Paste the key into `index.html`
4. Deploy to GitHub Pages

## Expected sheet columns

| Timestamp | Lat | Lng | Device |
|-----------|-----|-----|--------|
| 2026-04-18 09:15:00 | 13.7563 | 100.5018 | phone-01 |

## Files

- `index.html` — the complete web app (single self-contained file)
- `SETUP.md` — setup and deployment guide
