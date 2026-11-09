# GridFlex Egypt

A single-page academic sustainability concept for Group 31, built with Vue 3, Vite, Tailwind CSS 4 and Lucide icons. Includes an appliance calculator in the six-step walkthrough, sample charts, four sector choices and a before/after scheduling toggle.

## Run locally

Requires Node.js 22 or later.

```sh
npm ci
npm run dev
```

## Production build

```sh
npm run build
npm run preview
```

The build is in `dist/`. Vite uses a relative base so assets work on a GitHub Pages project URL as well as a custom domain. No backend, API keys or environment variables are needed.

## Publish with GitHub Pages

1. Create a public GitHub repository named `gridflex-egypt` (or another name).
2. Push this folder's contents, including `.github/workflows/deploy.yml`, to its `main` branch. Do not upload `node_modules`.
3. In the repository, open **Settings → Pages → Build and deployment → Source** and choose **GitHub Actions**.
4. Run **Actions → Deploy GridFlex to GitHub Pages → Run workflow**, or push a commit to `main`.
5. After the workflow succeeds, use the deployment URL displayed by GitHub Pages.

The workflow installs locked dependencies, builds the site, uploads `dist/`, and deploys it with the official GitHub Pages actions. Repository creation and the Pages setting must be completed on the destination GitHub account; the source archive alone does not publish a site.

## Content and editing

- `src/App.vue`: all page copy, walkthrough state and illustrative appliance data.
- `src/EnergyChart.vue`: accessible SVG chart component.
- `src/style.css`: Tailwind import, visual system and responsive layouts.
- `public/solar-field.jpg`: bundled solar-field photograph sourced from Unsplash (https://images.unsplash.com/photo-1508514177221-188b1cf16e9d).
- `public/favicon.svg`: original GridFlex icon.

Fonts are requested from Google Fonts with local sans-serif fallbacks. The SDG panel is an original SDG-inspired treatment, not the official UN logo. No affiliation is implied.

## Scope and data

All charts, values and schedules are illustrative, not real Egyptian grid forecasts. Sector selection changes explanatory guidance; subsequent steps intentionally share one household example. The 4 kWh figure is the sum of two hypothetical shifted loads (2 kW × 1 hour and 1 kW × 2 hours), not measured energy savings. Chart curves are normalised concept illustrations, not quantitative load simulations. Essential equipment remains unchanged. No device control, user data collection, guaranteed bill savings or emissions claims are included.

## Validation

Production build passed. Desktop browser rendering, sector selection, walkthrough navigation and the impact toggle were checked. Responsive CSS is included for mobile/tablet/desktop; a mobile-device browser pass was not performed. Public GitHub Pages deployment requires a connected GitHub account and has not yet been verified.
# gridflex-egypt
