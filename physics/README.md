# physics/ — physics-based arrival model

**Owner:** Dan · **Language:** Python (NumPy / SciPy)

Estimates arrival times from flight phase, distance remaining, winds aloft, and taxi time.

**Reads:** `flight_states_latest`, `flights` · **Provides:** `physics_eta` to the ML combiner (see [`docs/contracts.md`](../docs/contracts.md)).

## Setup

```bash
cd physics
python3 -m venv .venv && source .venv/bin/activate   # Windows: python -m venv .venv && .venv\Scripts\activate
pip install -r requirements.txt   # once it exists
```

MATLAB prototypes are fine to keep in `physics/prototypes/`, but the shared code is Python.
