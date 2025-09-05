Pushup Tracker
================

Single‑file web app that tracks your pushups using the device camera. Place the phone on the floor (screen up, front camera unobstructed). Each time your face comes near, one pushup is counted. Counts are stored per day; you can view the last 7 days and now manage multiple sessions within a day.

Demo: [https://polarity.productions/pushups/](https://polarity.productions/pushups/)

Core goals
- Vanilla JS only
- No CSS/JS frameworks
- LocalStorage persistence
- Camera-based automatic rep detection (FaceDetector API with brightness fallback)
- StandardJS style (semicolon‑less)
- Everything self‑contained in a single `index.html`

UI / Design
- Dark minimal design
- Big today total
- Adjustable via + / − buttons
- Reset clears all sessions for today
- 7‑day summary table (today first)
- Sessions panel: create multiple sessions per day, delete unwanted sessions

Sessions
- Each day can have multiple sessions (e.g. morning, afternoon, evening sets).
- "New Session" ends the current active one (if open) and starts a fresh empty session.
- Deleting a session removes only that session; the daily total updates automatically.
- Legacy data (older versions with a single numeric value per day) is auto‑migrated to a single session entry the first time the new version loads.

---

Quick start
- Open `index.html` in a modern browser on a phone or laptop.
- Tap "Start Camera" and allow camera access.
- Put the phone on the floor with the front camera facing up; each time your face gets close, it counts one.
- Use + / − to correct the active session count.
- Tap "New Session" to begin a separate session (e.g. after a rest period).
- Reset clears all sessions for today.

Notes
- Camera access requires a secure context: open via HTTPS or `http://localhost` (file:// may not work).
- Data is stored locally in `localStorage` per browser/device.
- Face detection uses the built‑in FaceDetector API when available; otherwise a brightness‑based fallback is used.
- If camera is unavailable (e.g. some iOS browsers), you can still manually track using + / −.

Data format
- LocalStorage key: `pushups.v1`.
- Legacy: `{ "YYYY-MM-DD": 23 }` (number).
- Current: `{ "YYYY-MM-DD": { sessions: [ { id, count, started, ended|null }, ... ] } }`.
- The daily total is the sum of `session.count` values.

Privacy / network
- No data leaves the device; no analytics, no remote calls.
- Only camera stream is used, never uploaded.

Ideas / extensions (not implemented by default)
- Export/import JSON data
- Adjustable detection thresholds / debounce interval
- Optional audio or vibration feedback per rep

Feel free to open an issue or make a request if you’d like one of the extensions added.