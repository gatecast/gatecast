# Contracts — v1 (approved Nov 15, 2026)

These are the agreed data formats between the parts of Gatecast. If you need to change one, open a pull request that edits this file and get approval from everyone whose code it touches.

## Shared conventions

- **Time:** UTC, ISO 8601 (`2026-12-20T18:32:01Z`). The app converts to local time for display only.
- **Airports:** IATA codes (`CMH`, `CLE`).
- **Flight numbers:** IATA form (`UA1423`).
- **Units:** SI in all shared data — meters, meters per second, degrees. OpenSky already sends meters and m/s. The app converts to feet and knots for display only.
- **Unknown values:** `null`, never `0`.
- **Integration (v1):** one shared PostgreSQL database. Zach writes flight data, Dan and Hari read it and write predictions, Chris's API reads everything.

## Decisions

| Question | Decision |
| --- | --- |
| Units | SI |
| Polling rate (OpenSky) | Every 30 seconds |
| Prediction refresh | Every 15 minutes |
| Alert threshold | 15 minutes |
| Database migrations owner | Chris |

## 1. FlightState

Written by **engine/** (Zach). One row per aircraft update in table `flight_states`; latest row per aircraft also kept in `flight_states_latest`.

```json
{
  "icao24": "a1b2c3",
  "callsign": "UAL1423",
  "flight_number": "UA1423",
  "registration": "N12345",
  "lat": 41.41,
  "lon": -81.85,
  "altitude_m": 3810,
  "ground_speed_mps": 159.5,
  "heading_deg": 274,
  "vertical_rate_mps": -6.1,
  "on_ground": false,
  "origin": "ORD",
  "destination": "CLE",
  "updated_at": "2026-12-20T18:32:01Z",
  "source": "opensky"
}
```

## 2. Flight

The scheduled flight a user tracks. Written by **engine/** (Zach) from the flight-status API. Table `flights`.

```json
{
  "flight_number": "UA2210",
  "date": "2026-12-20",
  "origin": "CLE",
  "destination": "DEN",
  "scheduled_departure": "2026-12-20T23:40:00Z",
  "scheduled_arrival": "2026-12-21T02:15:00Z",
  "airline_estimated_departure": "2026-12-20T23:40:00Z",
  "airline_estimated_arrival": "2026-12-21T02:15:00Z",
  "actual_departure": null,
  "actual_arrival": null,
  "registration": "N12345",
  "inbound_flight_number": "UA1423",
  "status": "scheduled"
}
```

`airline_estimated_*` and `actual_*` are how we measure whether we beat the airline.

`status` values: `scheduled`, `boarding`, `departed`, `landed`, `cancelled`, `diverted`.

## 3. Prediction

Written by **ml/** (Hari's combiner), using Dan's physics ETA and Hari's ML model. Table `predictions` — a new row on every refresh, so we can chart accuracy over time.

```json
{
  "flight_number": "UA2210",
  "date": "2026-12-20",
  "predicted_departure": "2026-12-21T00:20:00Z",
  "predicted_arrival": "2026-12-21T02:50:00Z",
  "arrival_range": { "earliest": "2026-12-21T02:35:00Z", "latest": "2026-12-21T03:10:00Z" },
  "delay_minutes": 40,
  "confidence": 0.72,
  "components": { "physics_eta": "2026-12-21T02:46:00Z", "ml_eta": "2026-12-21T02:53:00Z" },
  "reason": "Inbound aircraft UA1423 is still 290 km out and landing about 30 minutes late.",
  "model_version": "v0.1",
  "created_at": "2026-12-20T21:05:00Z"
}
```

## 4. API endpoints

Built by **platform/** (Chris).

| Method | Path | What it does | Main data from |
| --- | --- | --- | --- |
| GET | `/flights/{flight_number}?date=` | Flight details + latest prediction | Zach, Hari |
| GET | `/flights/{flight_number}/predictions?date=` | Prediction history (accuracy charts) | Hari |
| GET | `/live?airport=CLE` | Aircraft near an airport, for the map | Zach |
| POST | `/track` | Body: `flight_number`, `date`, `email` → tracking id; starts alerts | Chris |
| DELETE | `/track/{id}` | Stop alerts | Chris |
| POST | `/ask` | Body: `flight_number`, `question` → plain-English answer | Hari |
| GET | `/health` | Is every service running? | Everyone |

## Alert rule (v1)

Email when the predicted departure moves 15+ minutes from the last value we sent, and once more 2 hours before departure.
