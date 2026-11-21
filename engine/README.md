# engine/ — real-time data engine

**Owner:** Zach · **Language:** Go

Pulls live aircraft positions from OpenSky every 30 seconds for the Ohio area (CMH, CLE), keeps the latest state of every tracked aircraft, and links each plane to the next flight it will operate.

**Writes:** `flight_states`, `flight_states_latest`, `flights` (see [`docs/contracts.md`](../docs/contracts.md)).

## Setup

1. Install Go: https://go.dev/dl/
2. Put your OpenSky and flight-status API keys in the repo-root `.env` (copy `.env.example`).
3. Run instructions go here once the service exists.
