# db/migrations/ — database schema

**Owner:** Chris

Plain SQL files, applied in order. Name them with a number and a short description:

```
001_create_flight_states.sql
002_create_flights.sql
003_create_predictions.sql
```

Never edit a migration that has already been merged. Add a new one instead. Tables and columns must match [`docs/contracts.md`](../../docs/contracts.md).
