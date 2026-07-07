# sports-events-climate-skill

An [agent skill](https://agentskills.io/home) that scaffolds **"weather at a
sports event"** web apps and deploys them to GitHub Pages — a date-pickable
[MapLibre](https://maplibre.org/) map with one temperature-colored pin per event
and per-variable forecast charts.

It generalizes the [world-cup-climate](https://github.com/aaronspring/world-cup-climate)
app into a reusable generator: give it a sport and a schedule, and it emits a
complete, runnable, deployable project.

## What it makes

```
data/*.json  →  backend/recompute.py  →  static JSON contract  →  React + MapLibre SPA  →  GitHub Pages
 (schedule)      (weather layer)          (frontend/public/data)   (Vite build)
```

- **Python recompute backend** — a synthetic **demo** source (zero auth) *and* a
  real **ECMWF IFS** forecast source (via [Arraylake](https://docs.earthmover.io)),
  both writing the same static JSON contract. Heat-stress indices (NOAA heat
  index, WBGT) included.
- **React + TypeScript + MapLibre + Recharts SPA** — date strip, temperature
  pins, click-through card with per-variable forecast charts. Plain CSS, robust
  build.
- **GitHub Pages workflow** — builds on every push (demo source needs no secrets,
  so the site works out of the box).

## The core idea: one abstraction for every sport

Everything reduces to an **occasion** — a dated thing with a time, one primary
map `location`, and 0..N `compare` locations. One codebase therefore serves:

| Sport | occasion | `location` (pin) | `compare` |
| --- | --- | --- | --- |
| Football (World Cup, Euro) | a match | the venue | the two teams' home cities |
| Cycling (Tour de France, Giro) | a stage | the finish | the start |
| Marathon / F1 / tennis / … | a race / session | the course / circuit | usually none |

## Install as a Claude Code skill

```bash
git clone https://github.com/aaronspring/sports-events-climate-skill \
  .claude/skills/sports-weather-app
```

Then invoke it by describing the app you want (e.g. *"build a Tour de France 2026
weather app for repo owner/tdf-weather"*), or run the generator directly.

## Use directly (without Claude)

```bash
python scaffold.py \
  --out ../tour-de-france-weather \
  --slug tour-de-france-weather \
  --title "Tour de France Weather" \
  --repo aaronspring/tour-de-france-weather \
  --sport cycling            # football | cycling | generic

cd ../tour-de-france-weather
uv run python backend/recompute.py --source demo   # writes frontend/public/data
uv run python backend/test_recompute.py            # self-checks
cd frontend && npm install && npm run dev          # http://localhost:5173
```

Edit `data/events.json` + `data/locations.json` for the real event, then push to
GitHub and set **Settings → Pages → Source → GitHub Actions**.

## Contents

| Path | What |
| --- | --- |
| `SKILL.md` | Skill entry point: the domain abstraction + step-by-step workflow. |
| `scaffold.py` | The generator (pure stdlib) — emits the whole project. |
| `references/architecture.md` | System shape (backend, frontend, optional raster overlay). |
| `references/data-model.md` | The two input files + the output JSON contract. |
| `references/weather-data.md` | Demo vs real ECMWF IFS; Arraylake auth; adding variables. |
| `references/deploy.md` | GitHub Pages setup and switching to live data. |

## License

MIT
