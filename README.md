Pushup Tracker
================

Versy simple web app that tracks your pushups using the camera. You can put the phone to the floor and everytime your face comes near the camera, it counts as a pushup. The pushups are counted for that day and you can see your progress over time and for the week.

Application
- use vanilla JS as possible
- no css, js frameworks
- use local storage to store the data
- use camera to track pushups
- standardjs for formatting
- make the html and app self contained in a single file

Design
- simple and clean design
- dark design
- show the numbers for the current pushups
- show below a table with the last 7 days
- add buttons so you can correct the current count plug and minus
- add a reset button to reset the current count

---

Quick start
- Open `index.html` in a modern browser on a phone or laptop.
- Tap "Start Camera" and allow camera access.
- Put the phone on the floor with the front camera facing up; each time your face gets close, it counts one.
- Use + / − to correct the count and Reset to clear today.

Notes
- Camera access requires a secure context: open via HTTPS or `http://localhost` (file:// may not work).
- Data is stored locally in `localStorage` per browser/device.
- Face detection uses the built-in FaceDetector API when available; otherwise a brightness-based fallback is used.