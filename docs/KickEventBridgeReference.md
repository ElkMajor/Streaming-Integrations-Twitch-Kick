# Streaming Event Bridge

This service is the production event path for Kick. It receives Kick's public HTTPS webhooks, verifies the RSA-SHA256 signature and replay window, identifies authenticated game clients through Kick token introspection, and routes an event only to WebSocket clients logged in as that broadcaster.

## Local development

Run `node server.js`, expose port `17565` through an HTTPS tunnel, and configure Kick's Webhook URL as:

`https://<public-host>/webhooks/kick`

Keep Unreal's **Kick Event Bridge URL** set to:

`ws://127.0.0.1:17565/events/kick`

The Kick Events component connects after **Connect Kick Account** succeeds. It sends the access token in the WebSocket Authorization header; tokens are never placed in URLs or Blueprint pins.

## Production

Deploy this service behind a TLS reverse proxy and set:

- `STREAMING_WEBHOOK_HOST=0.0.0.0`
- `STREAMING_WEBHOOK_PORT` to the platform-provided port
- `KICK_PUBLIC_KEY_PEM` when Kick rotates its public key
- `STREAMING_WEBHOOK_MAX_AGE` to the accepted replay window in seconds

Register `https://events.your-game.example/webhooks/kick` in the publisher's Kick application. Configure Unreal with `wss://events.your-game.example/events/kick`.

The service intentionally has no anonymous broadcast endpoint. Every game WebSocket is authenticated against Kick and keyed by its connected broadcaster ID. Run multiple instances behind a load balancer only after adding shared pub/sub and replay storage (Redis, NATS, or equivalent); the included in-memory router is suitable for one instance and local development.

