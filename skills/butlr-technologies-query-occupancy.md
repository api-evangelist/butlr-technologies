---
name: Query historical occupancy from Butlr
description: Authenticate with the Butlr API and query time-series floor/room/zone occupancy and traffic via the Reporting API v3.
api: Butlr Reporting API v3
source: https://docs.butlr.io/historical-occupancy/reporting-api-overview.md
operations:
  - "POST https://api.butlr.io/api/v2/clients/login"
  - "POST https://api.butlr.io/api/v3/reporting"
method: generated
generated: '2026-07-18'
---

# Query historical occupancy from Butlr

Use this skill to pull historical, time-series occupancy and traffic data for a
space (floor, room, or zone) from Butlr's RESTful Reporting API.

## 1. Mint an access token (client credentials)

Create a Client ID / Client Secret in the Butlr Web App (Account Settings → API
tokens), then exchange them for a bearer token:

```
POST https://api.butlr.io/api/v2/clients/login
Content-Type: application/json

{ "client_id": "...", "client_secret": "...", "audience": "https://butlrauth/", "grant_type": "client_credentials" }
```

Use the returned `access_token` as `Authorization: Bearer <access_token>` on all
subsequent calls. Tokens expire (`expires_in`); re-mint as needed.

## 2. Query the Reporting API

```
POST https://api.butlr.io/api/v3/reporting
```

Body shape (see docs for the full parameter set):

```json
{
  "window": { "every": "1h", "function": "max", "timezone": "America/New_York", "create_empty": true },
  "filter": { "start": "2024-01-01T04:00:00Z", "stop": "2024-01-02T04:00:00Z",
              "measurements": ["traffic_floor_occupancy"],
              "spaces": { "eq": ["space_..."] }, "value": { "gte": 0 } },
  "group_by": { "order": ["time"] }
}
```

## Rules (from Butlr conventions)

- Always supply an explicit `timezone`; do not use relative time (e.g. `-5m`) — use absolute `start`/`stop`.
- Aggregation function: `median` for 1-minute intervals, `mean`/`max` for larger intervals, `sum` for traffic.
- Include `"create_empty": true` with `"value": { "gte": 0 }` so empty periods return 0 instead of being omitted.
- Keep 1-minute-interval queries under 24h for performance.
- No idempotency key is required (reads); the API has no documented standardized error envelope, so branch on HTTP status.
