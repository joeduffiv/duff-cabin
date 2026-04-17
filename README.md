# Duff Cabin

A mobile-first PWA for managing cabin departures — departure checklist with photo capture, shared shopping and project lists, weather forecast, and hunting season RSVPs.

## Stack

- Vanilla HTML/CSS/JavaScript (no build step, no framework)
- [Firebase Realtime Database](https://firebase.google.com/docs/database) — shared lists and RSVPs
- [Open-Meteo](https://open-meteo.com/) — free weather API
- `localStorage` — checklist state and offline queue

## Local Development

1. Clone the repo
2. Copy `config.example.js` → `config.js` and fill in your values (see Configuration below)
3. Open `index.html` in a browser, or serve with any static server:
   ```bash
   npx serve .
   # or
   python3 -m http.server
   ```

> **Note:** `config.js` is excluded from version control. Never commit it.

## Configuration

Copy `config.example.js` to `config.js` and set each value:

| Variable | Description |
|---|---|
| `FIREBASE_URL` | Your Firebase Realtime Database URL |
| `OWNER_NAME` | Name of the cabin owner (enables admin controls) |
| `OWNER_EMAIL` | Email address for departure report mailto link |
| `OWNER_PHONE` | Phone number for departure report SMS link |
| `WX_LAT` / `WX_LON` | Decimal coordinates for the weather forecast |

If `FIREBASE_URL` is missing or left as the placeholder, the app runs in **local-only mode** — all list data stays in `localStorage`.

## Firebase Setup

1. Create a project at [console.firebase.google.com](https://console.firebase.google.com/)
2. Add a Realtime Database and note its URL
3. Set security rules — at minimum, restrict writes to authenticated users or a known origin. Example rules (tighten before production):
   ```json
   {
     "rules": {
       ".read": true,
       ".write": true
     }
   }
   ```
4. Paste the database URL into `config.js`

## Deployment

The app is a single HTML file and deploys anywhere that serves static files.

**GitHub Pages:**
1. Push to `main`
2. Go to Settings → Pages → Source: `main` / `/ (root)`
3. Because `config.js` is gitignored, add a GitHub Actions step to create it from repository secrets before deploying:
   ```yaml
   - name: Write config
     run: |
       cat > config.js <<EOF
       const FIREBASE_URL = '${{ secrets.FIREBASE_URL }}';
       const OWNER_NAME   = '${{ secrets.OWNER_NAME }}';
       const OWNER_EMAIL  = '${{ secrets.OWNER_EMAIL }}';
       const OWNER_PHONE  = '${{ secrets.OWNER_PHONE }}';
       const WX_LAT       = ${{ secrets.WX_LAT }};
       const WX_LON       = ${{ secrets.WX_LON }};
       EOF
   ```

## Offline Support

The app queues Firebase writes when offline and flushes them on reconnect via `flushQueue()`. Photos are stored as base64 in `localStorage` (subject to ~5-10 MB browser limit).
