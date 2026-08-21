# AccessTrip — Travel without barriers

A travel planner that scores hotels, attractions, and routes by **real accessibility
needs** (ramps, elevators, quiet hours, step-free paths) instead of just star ratings —
and explains every recommendation in plain language.

Built for a hackathon. Frontend-only demo with mock data, structured so the scoring
and recommendation logic can be swapped for a real backend/API later without touching
the UI.

## Features

- **Accessibility profile picker** — wheelchair, elderly, visual/hearing impaired, stroller, quiet-environment.
- **Accessibility Score** — a transparent 0–100 score per listing (`ramp×20 + elevator×20 + bathroom×20 + entrance×10 + parking×10 + quiet×10 + transport×10`).
- **Personalized match %** — ranks listings by how well they meet *your* selected needs, separate from the raw score.
- **AI-style recommendation text** — plain-language explanation of why a place was suggested (rule-based today; swap in a real LLM call in one place, see below).
- **Accessibility mismatch detection** — flags listings whose description ("heritage", "multi-storey"...) implies stairs/no elevator when that isn't confirmed.
- **Accessibility-aware route planner** — compares a "fastest" vs "most accessible" route as a visual ribbon of waypoints, and recommends the accessible one whenever a need is selected, even if it's longer.

## Tech stack

- React 18 + Vite (no backend required for the demo — all data is in `src/data/mockData.js`)
- Plain CSS with design tokens in `src/index.css`

## Run locally

```bash
npm install
npm run dev
```

Open the URL Vite prints (usually http://localhost:5173).

## Project structure

```
src/
  data/mockData.js       destinations, hotels, routes (swap for a real API/DB later)
  utils/scoring.js        accessibility score + personal match % calculations
  utils/recommend.js      rule-based "AI" explanation + mismatch detection
  components/             UI pieces (ProfileSelector, ResultCard, RouteCompare, ...)
  App.jsx                 wires it all together
```

### Swapping in a real AI/LLM call

`src/utils/recommend.js` exports `explainMatch(place, selectedProfiles)`. Right now
it's a template-based function. To use a real model, replace its body with a call to
your backend (which calls the Anthropic API or similar) and keep the same signature —
no other file needs to change.

### Swapping in real data

Replace the contents of `src/data/mockData.js` with data pulled from a real source
(Google Places / OpenStreetMap accessibility tags, partner hotel APIs, user-submitted
reports, etc). The shape each place needs is:

```js
{
  id, name, price, description,
  attrs: { ramp, elevator, accessibleBathroom, wideEntrance, accessibleParking, quietEnvironment, accessibleTransport } // booleans
}
```

---

## Upload to GitHub

From inside the `accesstrip` folder:

```bash
git init
git add .
git commit -m "Initial commit: AccessTrip hackathon MVP"
```

Create a new empty repo on GitHub (github.com → **New repository** → don't initialize
with a README since you already have one), then:

```bash
git branch -M main
git remote add origin https://github.com/<your-username>/<your-repo>.git
git push -u origin main
```

If you're prompted for a password, GitHub no longer accepts your account password for
this — use a **Personal Access Token** (GitHub → Settings → Developer settings →
Personal access tokens) as the password, or push using GitHub Desktop / the `gh` CLI
(`gh auth login` then `gh repo create`).

## Deploy tonight (fastest: Vercel)

1. Go to **vercel.com** → sign in with GitHub.
2. **Add New… → Project**, import the repo you just pushed.
3. Vercel auto-detects Vite. Leave the defaults:
   - Build command: `npm run build`
   - Output directory: `dist`
4. Click **Deploy**. You'll have a live `https://<project>.vercel.app` URL in ~1 minute.

### Alternative: Netlify

1. **netlify.com** → **Add new site → Import an existing project** → pick your GitHub repo.
2. Build command: `npm run build`, publish directory: `dist`.
3. Deploy — you'll get a `https://<project>.netlify.app` URL.

### Alternative: GitHub Pages (no extra sign-up)

```bash
npm install --save-dev gh-pages
```

Add to `package.json`:
```json
"homepage": "https://<your-username>.github.io/<your-repo>",
"scripts": {
  "predeploy": "npm run build",
  "deploy": "gh-pages -d dist"
}
```

Then:
```bash
npm run deploy
```

Enable Pages in the repo's **Settings → Pages** if it isn't live automatically.

---

Good luck at the demo. 🥇
