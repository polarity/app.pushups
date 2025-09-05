# AI Coding Agent Instructions

Project: Pushup Tracker (single-file client-only web app)

## Scope & Architecture
- Pure front-end: one primary file `index.html` containing HTML, CSS, and JavaScript (IIFE pattern). No build system, bundler, or external dependencies.
- State is entirely local: daily pushup counts stored in `localStorage` under key `pushups.v1` keyed by ISO date `YYYY-MM-DD`.
- Camera-based rep detection: prefers `FaceDetector` API; falls back to brightness sampling heuristic if unavailable or fails.
- All logic runs in-browser; no backend, no module system.

## Core Functional Areas (in `index.html` script)
1. Data persistence: `loadData()`, `saveData()`, `getTodayCount()`, `setTodayCount()`, `modifyToday()`.
2. UI update: `updateCountUI()`, `renderWeek()` (generates 7-day table, newest first).
3. Camera lifecycle: `startCamera()`, `stopCamera()`, `toggleCamera()`, secure-context & feature checks.
4. Detection loop: requestAnimationFrame `loop()` calling either `detectWithFaceDetector()` or `detectWithBrightness()`.
5. Rep edge detection: `handleNearState(near)` increments on far→near transitions with debounce (`minIntervalMs = 800ms`) + hysteresis thresholds.
6. Fallback/Polyfill: Legacy `getUserMedia` shim and secure context gating; auto downgrade if FaceDetector fails.

## Detection Logic Details
- Face mode: near when face bounding box height ratio ≥ 0.32. Hysteresis: remain near until < 0.25.
- Brightness mode: samples center 40% region downscaled to 160×120; maintains adaptive min/max; near when normalized brightness ≤ 0.25, release when > 0.40.
- Debounce: Only one increment per approach cycle (must transition far→near and exceed `minIntervalMs`).

## UI & Styling
- Dark theme using inline CSS variables; no external CSS/JS allowed (keep single-file constraint).
- Buttons: +, −, Reset, Start/Stop Camera. Disabled states used when insecure or unsupported.
- Overlay: `<canvas>` draws face bounding box (green) or brightness region highlight.
- Status chips: detector type, state (idle/near/far/error), contextual warnings.

## Conventions & Constraints
- Stay vanilla: Do NOT introduce frameworks, build tooling, module loaders, or external CDNs unless user explicitly requests.
- Keep everything in `index.html` unless the user asks to refactor structure.
- Follow semicolon-less StandardJS style already present (no trailing semicolons, single quotes, camelCase, no unused vars).
- Keep functions small and top-level inside the IIFE; avoid polluting global scope.
- Preserve existing key names and storage schema (`pushups.v1`). If migrating, implement backward compatibility.

## Extending Safely
When adding features:
- Respect offline: avoid network calls unless explicitly needed.
- Maintain single-file unless restructuring is requested.
- Use feature detection (`'FeatureName' in window`) before new APIs.
- For new settings (e.g., adjustable debounce), persist under a namespaced key (e.g., `pushups.settings`) to avoid breaking existing data.

## Potential Feature Hooks
- Audio/vibration feedback: gate with `navigator.vibrate` or user toggle.
- PWA support: Only add `manifest.json` and service worker if user opts in; document impact.
- Export data: Provide JSON download via `Blob` + anchor link.
- Threshold tuning UI: expose current NEAR/FAR ratios & interval with validation.

## Testing & Validation Guidelines
- Manual verification only (no test harness). Suggest quick sanity steps: load page, start camera, simulate hand/face approach.
- Avoid timing fragile logic; tune thresholds conservatively.

## Error Handling Patterns
- Use `alert()` sparingly for critical camera errors; update status chip for non-fatal fallbacks.
- On repeated FaceDetector failures: switch to brightness fallback and update detector chip class to `warn`.

## Performance Considerations
- rAF loop throttled by `busy` flag to prevent overlapping detection work.
- Brightness sampling: processes reduced 160×120 frame region for efficiency.
- Avoid adding heavy per-frame allocations; reuse canvases/contexts.

## Security / Privacy
- No remote transmission of video or counts; keep it that way unless explicitly changed.
- Remind users about HTTPS requirement for camera if altering hosting guidance.

## Example: Adding Audio Feedback (Pattern)
```js
// After modifyToday(1):
if (window.AudioContext) {
  const ctx = new AudioContext()
  const osc = ctx.createOscillator(); const gain = ctx.createGain()
  osc.frequency.value = 880; gain.gain.setValueAtTime(0.001, ctx.currentTime)
  gain.gain.exponentialRampToValueAtTime(0.2, ctx.currentTime + 0.01)
  gain.gain.exponentialRampToValueAtTime(0.0001, ctx.currentTime + 0.25)
  osc.connect(gain).connect(ctx.destination); osc.start(); osc.stop(ctx.currentTime + 0.3)
}
```
(Place in `handleNearState` right after counting; guard to avoid re-init each frame.)

## Do Not
- Do not add bundlers, TypeScript, React, or external dependencies without explicit user instruction.
- Do not rename existing storage key or core functions without backward compatibility.
- Do not loosen secure-context checks for camera.

## When Unsure
- Ask user before structural changes (multiple files, PWA assets, settings UI) or new dependencies.

---
Keep responses concise and focused on this project’s constraints. Provide direct code edits inside `index.html` unless told otherwise.
