# ml/ — delay model, combiner, LLM layer

**Owner:** Hari · **Language:** Python (pandas, scikit-learn / LightGBM)

- Delay model trained on BTS on-time performance data for CMH and CLE.
- Combiner that merges the ML estimate with Dan's physics ETA into a `Prediction`.
- LLM layer that answers plain-English questions for `POST /ask`.

**Reads:** `flights`, `flight_states_latest`, physics ETA · **Writes:** `predictions` (see [`docs/contracts.md`](../docs/contracts.md)).

## Setup

```bash
cd ml
python3 -m venv .venv && source .venv/bin/activate   # Windows: python -m venv .venv && .venv\Scripts\activate
pip install -r requirements.txt   # once it exists
```

Raw data goes in `ml/data/` and trained models in `ml/models/`. Both are ignored by Git, so write down in this README how to download or rebuild them.
