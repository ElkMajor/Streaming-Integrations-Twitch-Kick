# Changelog

## 1.1.0 command-registration hotfix

- Fixed the combined `Streaming Events (Twitch + Kick)` component so its inherited Register, Unregister, and Clear Chat Command Blueprint nodes update the live combined subsystem as well as the component-local command table. Changing an example command such as `hello` to `play` now correctly fires for `!play`.

## 1.1.0

- Added the missing production Kick event path: a deployable signed-webhook receiver, authenticated WebSocket router, and automatic Unreal event-bridge subsystem.
- Game clients authenticate to the publisher bridge with their transient Kick bearer token in a WebSocket header; tokens are never placed in URLs, config, or Blueprint pins.
- The bridge introspects the token with Kick, resolves the connected broadcaster, and routes events only to that broadcaster's active game session.
- Kick Events and combined Streaming Events now connect automatically after account authentication, reconnect with bounded exponential backoff, expose connection state, and dispatch typed events and chat commands.
- Corrected normalization for Kick's current nested `sender`, `broadcaster`, `identity`, numeric user IDs, username colors, and badge payloads.
- Added a Docker deployment artifact, local tunnel workflow, health endpoint, signature/replay enforcement, duplicate protection, and production scaling guidance.

## 1.0.2

- Fixed Kick event subscription payloads to use the current official `events[].name` schema with a top-level broadcaster ID; fixed repeated-ID event unsubscription.
- Fixed every async Blueprint action so success and failure execution branches expose identical typed outputs. Failed provider requests now return HTTP status, code, message, request ID, retry data, and raw response content.
- Added provider-aware preflight/cancellation errors and prevented remembered valid accounts from unnecessarily requiring a live token broker.
- Updated Kick users, chat messages, bans/unbans, channel rewards, redemptions, and token introspection to the current Kick Swagger contract.
- Replaced additional Kick string inputs with typed event, chat-message-type, and redemption-status enums and added repeated-ID query support.
- Added an automated reflection guard that detects incompatible async Blueprint delegate pin layouts before release.
- Regenerated the catalogs from current official sources: 30 Kick endpoints, 10 Kick events, 149 Twitch endpoints, and 85 Twitch events.

- Replaced Kick's normal event-name/version string inputs with the documented `Kick Event Type` enum; retained a clearly labeled Advanced custom-event escape hatch.
- Added `Get Logged In Kick User`, returning a typed `Streaming User` directly.
- Replaced normal Twitch EventSub status strings with a typed status enum and moved arbitrary status/type filtering under Advanced.
- Added symmetrical `Get Logged In Twitch User` and `Get Logged In Kick User` typed actions plus cached `Get Connected Streaming User` access.
- Added opt-in remembered accounts backed by Windows Credential Manager, automatic valid-session reuse, broker refresh on expiry, and explicit forget-account cleanup.

## 1.0.1

- Added compiled `BP_StreamingManager`, `BP_TwitchStreamManager`, and `BP_KickStreamManager` examples wired exclusively from production authentication, connection, component-event, and command APIs.
- Added one-node **Connect Twitch Account** and **Connect Kick Account** desktop OAuth flows with automatic secure loopback callback capture.
- Added readable authorization presets for chat listeners, chat bots, interactive streams, broadcaster tools, moderation, identity-only, and custom flows.
- Moved raw scope strings, authorization URL, callback code, and returned-state handling under Advanced.
- Replaced profile-facing raw scope and event-type lists with typed authorization presets and normalized event features while retaining deprecated data for migration.
- Corrected ready-to-install packaging so validated UE 5.8 binaries are included and documented clean-upgrade installation.

## 1.0.0

- Shared streaming core with structured errors, rate-limit/retry metadata, cancellation, PKCE, and transient sessions.
- Twitch Helix endpoint catalog, async Blueprint requests, EventSub WebSocket transport, and signed webhook verification.
- Kick endpoint catalog, async Blueprint requests, and RSA-signed webhook verification.
- Combined provider routing with project and session defaults.
- Editor setup dashboard, secure project settings, Streaming Integration Profile DataAsset, original product icon, documentation, and automation coverage.
- Friendly typed Twitch actions for channel updates, announcements, shoutouts, raids, rewards/redemptions, polls, and predictions.
- Friendly Kick actions for users, channels, categories, livestreams, chat, moderation, rewards/redemptions, subscriptions, and ads.
- Provider-neutral user, channel, stream, and pagination response parsing.
- Simultaneous Twitch + Kick events, provider fan-out actions, drop-in event component, chat command rules, simulator nodes, and themed chat overlay assets.
- Independent edition packaging: Twitch-only and Kick-only do not depend on the combined runtime module; all three editions pass Editor, Development, and Shipping BuildPlugin validation.
- Edition-aware dashboard and content filtering, with provider-specific drop-in listener Blueprints and a shared theme asset in standalone editions.
- Shared normalized event component used by standalone Twitch and Kick components, including commands, cooldown/moderator rules, and offline simulation.
- Twitch discovery, games, clips, videos, schedule, subscriber, moderator, VIP, chatter, emote, badge, poll, and prediction lookup actions.
- Typed category, clip, video, emote, and badge response models/parsers with Blueprint support.
- Typed rewards, redemptions, subscriptions, polls, predictions, and schedule models/parsers.
- Current Twitch Create Clip title and duration request support.
- Combined fan-out nodes for users, channels, chat deletion, bans, and unbans.
- Chat overlay inline emote/badge/GIF rendering, remote-image caching, sender colors, empty states, and designer controls.
- Shared Blueprint/C++ setup diagnostics with actionable editor-dashboard reporting.
- Dashboard Event Playground for Twitch/Kick chat, follow, subscription, raid, cheer, and reward simulation during PIE.
- Friendly Twitch ads, chat settings, moderators/VIPs, creator goals, followed content, stream markers, editors, shield mode, whispers, and blocked-term actions.
- Friendly Kick category, KICKs leaderboard, reward update, token introspection, and public-key actions.
# Unreleased

- Added the typed **Subscribe to Twitch Event** Blueprint action with automatic EventSub WebSocket session transport and typed event selection.
- Fixed all friendly Twitch API actions sending an empty `Client-Id` header.
- Trimmed provider IDs, redirects, token-broker URLs, and HTTP base URLs to prevent invisible whitespace from producing malformed requests.
- Replaced the generic no-response error with an actionable endpoint-specific network/TLS/firewall/timeout error.
