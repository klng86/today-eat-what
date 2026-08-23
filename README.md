# Today Eat What 🎡

Can't decide where to eat? This is a single-page web app that finds real food spots near you (via OpenStreetMap — no API key needed), lets you filter by cuisine, distance, price, and dietary needs, then spins a wheel to make the call.

**Design preview:** https://claude.ai/code/artifact/edcdf20f-fb7d-41ec-85ea-836f96291deb — good for a quick look at the look and feel, but Claude's preview sandbox blocks outside network calls, so location lookup and "find food nearby" won't actually return results there. Use the downloaded `index.html` (below) or the GitHub Pages link once it's live for the real thing — both have normal internet access.

## How it works

- **Location** — either "Use my current location" (browser geolocation) or type an address, which is geocoded via [Nominatim](https://nominatim.org/) (OpenStreetMap's free geocoder), with [OneMap](https://www.onemap.gov.sg/) as the primary, more accurate source for Singapore postal codes.
- **Nearby food** — queried live from the [Overpass API](https://overpass-api.de/) (OpenStreetMap data): restaurants, cafés, fast food, hawker centres/food courts, pubs, bars, ice cream, bakeries, confectioneries, delis, pastry shops, and canteens — matched as points, buildings, or larger complexes. If a search comes back thin (common in less-mapped residential areas), the app automatically widens the radius up to two tiers before giving up, and tells you when it did. The manual search radius slider goes up to 15 km if you want to cast a much wider net yourself.
- **Filters** — cuisine (auto-built from what's actually nearby), a tighter "within" distance, price level, and dietary needs (halal / vegetarian / vegan).
- **Wheel** — a canvas-drawn roulette wheel picks a fair random winner from your filtered list (capped at 24 spots on screen at once; "Show a different 24" reshuffles if you have more matches).
- **Result** — name, distance, walking time, and a one-tap link to open it in Google Maps. The link uses the place's name and street address (when OpenStreetMap has one tagged) so it lands on the actual business listing rather than a generic pin — falls back to name + coordinates when no address is tagged.
- **Changing your mind** — a "Change" button on the location pill (shown on both the radius and filters screens) lets you jump straight back to the address search at any point in the flow.

No backend, no build step, no API keys required, no cost. It's one HTML file that runs entirely in the browser.

### Optional: OneMap Hawker Centres layer

OneMap (Singapore's government geocoder) doesn't offer a general restaurant/cafe search — only curated datasets, one of which is official **Hawker Centre** building locations. If you want that layer merged into your results (on top of the regular OpenStreetMap search, which stays the source for everything else), paste a OneMap API access token into the `ONEMAP_ACCESS_TOKEN` constant near the top of the `<script>` block in `index.html`.

To get a token: register a free account at [onemap.gov.sg](https://www.onemap.gov.sg/apidocs/) and `POST` your email + password to `/api/auth/post/getToken`. **Tokens expire after about 3 days.** Since this is a static site with no backend to auto-refresh one, you'll need to paste in a fresh token every few days to keep this layer active. When the field is left blank, or a token has expired, the app just quietly searches OpenStreetMap alone — nothing breaks either way.

### Data limitations (it's worth knowing)

This runs on OpenStreetMap data, which is community-maintained:
- Coverage is generally good for towns/cities but a newer or smaller place might be missing.
- **Price level** is rarely tagged in OSM, so most places will show as "Not listed."
- **Dietary tags** (halal/vegetarian/vegan) are only as good as what's been tagged — a compatible place with no tag simply won't show up when that filter is on, so the filter under-counts rather than over-counts.

If you find a favourite spot missing, you (or anyone) can add it directly on [openstreetmap.org](https://www.openstreetmap.org) — the app will pick it up on the next search once it syncs.

## Running it locally

Because browsers restrict the geolocation API on `file://` pages, don't just double-click `index.html` if you want to test "Use my current location" — geolocation may silently fail. Instead, serve it locally:

```bash
cd "today-eat-what"
python3 -m http.server 8000
# then open http://localhost:8000 in your browser
```

(Typing an address instead of using geolocation works fine even from a plain double-clicked file, since it's a normal network request.)

Once it's live on GitHub Pages (below), geolocation works normally — GitHub Pages serves over HTTPS, which is all browsers require.

## Putting it on GitHub

You don't have a repo for this yet, so here's the full path from zero.

### 1. Create the repo on GitHub

Go to [github.com/new](https://github.com/new), pick a name (e.g. `today-eat-what`), leave it **public** (required for free GitHub Pages, unless you have a paid plan), and **don't** initialize it with a README (you already have one) — then click **Create repository**.

### 2. Push this folder to it

Open a terminal in this folder (`Today eat what`) and run:

```bash
git init
git add index.html README.md
git commit -m "Add Today Eat What app"
git branch -M main
git remote add origin https://github.com/<your-username>/<your-repo-name>.git
git push -u origin main
```

Replace `<your-username>` and `<your-repo-name>` with your actual GitHub username and the repo name you picked. If this is the first time pushing from this computer, GitHub will prompt you to sign in (a browser window or a personal access token, depending on how git is configured on this machine).

### 3. Turn on GitHub Pages

1. On the repo page, go to **Settings → Pages**.
2. Under **Build and deployment → Source**, choose **Deploy from a branch**.
3. Branch: **main**, folder: **/ (root)** → **Save**.
4. Wait ~1 minute, then refresh — GitHub shows the live URL at the top of that page. It'll look like:
   `https://<your-username>.github.io/<your-repo-name>/`

That URL is now your app. Bookmark it.

### Updating it later

Whenever you (or I) change `index.html`:

```bash
git add index.html
git commit -m "Update app"
git push
```

GitHub Pages redeploys automatically within a minute or so.

## Using it on your phone

Once it's live on GitHub Pages:

- **iPhone (Safari):** open the link → Share icon → **Add to Home Screen**. It'll behave like a normal app icon.
- **Android (Chrome):** open the link → ⋮ menu → **Add to Home screen** (or **Install app**, if offered).

The first time you tap "Use my current location," your phone will ask for permission — allow it while using the app.

## Customizing

Everything — colors, fonts, search radius options, which OSM amenity types count as "food," the wheel's segment cap — lives in the single `index.html` file, in a plain `<style>` block and a plain (no-framework) `<script>` block near the top and bottom of the file, respectively. No build tools required; edit and refresh.
