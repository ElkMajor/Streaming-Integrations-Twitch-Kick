# Architecture

`StreamingCore` owns shared types, settings, PKCE, authentication state, HTTP transport, errors, cancellation, and DataAssets. `TwitchIntegration` and `KickIntegration` own only provider-specific headers, endpoint catalogs, and event transports. `MultiStreamingIntegration` routes to either provider and depends on both. `StreamingIntegrationEditor` supplies the dashboard and settings workflow.

This dependency direction keeps one maintained networking/authentication implementation while allowing three commercial packages. Provider endpoint catalogs are generated, so newly documented operations can be added without hand-writing one Blueprint node per REST route.

The client is intentionally not a public webhook server or confidential OAuth client. Twitch WebSocket EventSub is suitable for a packaged desktop/game client. Twitch webhook/conduit infrastructure and Kick webhook reception belong on a backend; the plugin supplies verification/parsing helpers and API subscription operations.
