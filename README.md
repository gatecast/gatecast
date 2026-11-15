# Gatecast

**Know when your flight will really leave — before the airline tells you.**

Gatecast predicts real departure and arrival times by tracking the *inbound aircraft* that has to arrive before your flight can leave. It combines live aircraft positions, a physics-based flight model, and machine learning, then alerts travelers (and the people picking them up) early.

> 🚧 Work in progress — winter break 2026–27. Starting with flights at Columbus (CMH) and Cleveland (CLE).

## How it works

```
Live flight data ──► engine/ ──► physics/ ─┐
                    (Go)         (Python)  ├──► platform/ ──► web app + email alerts
                                 ml/ ──────┘    (FastAPI, PostgreSQL, React)
                                 (Python)
```

1. **engine/** pulls live aircraft positions every 30 s and links each plane to the next flight it will operate.
2. **physics/** estimates arrival times from flight phase, distance remaining, winds, and taxi time.
3. **ml/** predicts delays from historical data, weather, and the inbound plane's status, and combines its prediction with the physics estimate.
4. **platform/** stores everything, serves the API and web app, and sends alerts.

The data formats between these pieces are defined in [`docs/contracts.md`](docs/contracts.md).

## Team

| Area | Folder | Owner |
| --- | --- | --- |
| Real-time data engine | `engine/` | Zach |
| Physics-based arrival model | `physics/` | Dan |
| ML delay prediction + LLM explanations | `ml/` | Hari |
| API, web app, database, deployment | `platform/`, `db/migrations/` | Chris |

## Repo layout

```
engine/          Go service: live data ingestion and flight tracking
physics/         Python: physics-based ETA model
ml/              Python: delay model, combiner, LLM layer
platform/        FastAPI backend + React frontend
db/migrations/   Plain SQL schema migrations (owner: Chris)
docs/            Contracts, architecture, decisions
```

## Getting started

1. Clone the repo.
2. Copy `.env.example` to `.env` and fill in your own keys. **Never commit `.env`.**
3. See the README in each folder for setup.

Python virtual environments:

```bash
# Mac
python3 -m venv .venv && source .venv/bin/activate
# Windows
python -m venv .venv
.venv\Scripts\activate
```

## How we work

- Never commit directly to `main`. Every change goes through a pull request reviewed by a teammate.
- Branch names: `name/short-description` (e.g. `zach/opensky-poller`).
- Keep pull requests small: one feature or fix each.
- All shared data uses SI units and UTC timestamps (see `docs/contracts.md`).

## Data sources

- [OpenSky Network](https://opensky-network.org/) — live aircraft positions
- U.S. DOT Bureau of Transportation Statistics — historical on-time performance
- National Weather Service / aviationweather.gov — airport weather
- A flight-status API for schedules (TBD)

## License

MIT
