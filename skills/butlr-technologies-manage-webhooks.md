---
name: Subscribe to Butlr real-time occupancy webhooks
description: Register and manage real-time occupancy/traffic webhooks through the Butlr GraphQL API and handle the delivered events.
api: Butlr GraphQL API v3
source: https://docs.butlr.io/real-time-occupancy/manage-webhooks.md
operations:
  - "POST https://api.butlr.io/api/v2/clients/login"
  - "POST https://api.butlr.io/api/v3/graphql (createWebhooks)"
  - "POST https://api.butlr.io/api/v3/graphql (listWebhooks)"
  - "POST https://api.butlr.io/api/v3/graphql (updateWebhooks)"
  - "POST https://api.butlr.io/api/v3/graphql (deleteWebhooks)"
method: generated
generated: '2026-07-18'
---

# Subscribe to Butlr real-time occupancy webhooks

Use this skill to receive real-time occupancy and traffic events (floor/room/zone
occupancy, entryway traffic, area detections, PIR motion) via HTTP POST webhooks.

## 1. Authenticate

Mint a bearer token via `POST https://api.butlr.io/api/v2/clients/login`
(client credentials grant) and send it as `Authorization: Bearer <token>`.

## 2. Create a webhook (GraphQL)

Webhooks are self-service and managed through the GraphQL API at
`https://api.butlr.io/api/v3/graphql`. Create, update, list, and delete webhook
subscriptions specifying the event types you want. Available event types include:

- `Floor Occupancy`, `Room Occupancy`, `Zone Occupancy` (per-change)
- `FLOOR_OCCUPANCY_1M`, `ROOM_OCCUPANCY_1M`, `ZONE_OCCUPANCY_1M` (1-minute rollups)
- `Entryway Traffic`, `Area Detections`, `Motion Detection`, `No Motion Detection`

## 3. Tune delivery

- Set `filters` to restrict events to specific floor/room/zone ids.
- Set `send_on_value_change: true` to emit only when the occupancy value changes (reduces noise).

## 4. Handle delivered events

Each message carries customer-context ids (`floor_custom_id`, `room_custom_id`,
`zone_custom_id`) when present — use them to map events to your own locations.
Prefer the `*_1M` rollup events for stable time-series analysis; per-change events
may update prior minutes within a rolling 5-minute look-back window.
