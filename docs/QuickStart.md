# Streaming Integrations Quick Start

## 1. Install

Close Unreal Editor before installing. When upgrading, remove the entire previous Streaming Integrations plugin folder first; do not merge-extract over it because obsolete binaries can survive. Extract the selected ready-to-install product folder into your project's `Plugins` directory, enable it, and restart Unreal Editor. Open **Tools > Streaming Integrations Dashboard**.

The Twitch and Kick standalone editions can coexist in one project. The combined Twitch and Kick edition replaces both standalone editions and must not be enabled alongside either one.

## 2. Register provider applications

Create an application in the Twitch or Kick developer portal. Add the exact redirect URI shown in **Project Settings > Plugins > Streaming Integrations**. Enter only the public Client ID in Unreal. Never enter a client secret in an Unreal project.

## 3. Configure secure authentication

For local development, run `Extras/TokenBroker/SetupLocalCertificate.ps1` once and then `StartLocalBroker.ps1`. Enter the provider Client ID and Client Secret only into the broker prompt, keep that window open, and set Unreal's **Token Broker Base URL** to `https://localhost:17564`. The secret stays in the local broker process and is never stored in Blueprint or plugin settings.

For a customer-facing build, deploy the broker contract from `TokenBrokerContract.md` to your own authenticated HTTPS service. Do not ship the local broker, generated certificate, or provider Client Secret inside a game. Then enter the deployed HTTPS base URL. For desktop games, call **Connect Twitch Account** or **Connect Kick Account** from your UI and choose a readable permission preset. The node opens the browser, captures the configured localhost callback, validates it, exchanges the code through the broker, and returns **On Connected** or **On Failure**. Players may skip connection and continue using the game normally. Tokens remain transient in the game instance.

The default callback addresses use localhost and must be registered exactly in the provider developer portal. Raw scope arrays, authorization URLs, callback parsing, codes, and returned state remain under **Authentication > Advanced** only for hosted callback or custom-platform flows.

For local development only, **Set Transient Access Token (Development)** can inject a short-lived token. Do not ship developer tokens.

Enable **Remember on This Device** on the Connect Account node to reuse the account after restarting the game. On Win64, access and refresh credentials are stored by Windows Credential Manager under the current Windows user; they are never written to Config, SaveGame, content, logs, or Blueprint. The same Connect node returns an unexpired remembered session immediately or refreshes it through the configured broker. **Forget Streaming Account** removes both the active session and the saved credential.

After connection, use **Get Logged In Twitch User** or **Get Logged In Kick User** to receive one typed `Streaming User` directly. **Get Connected Streaming User** reads the cached typed user later without another request.

## 4. Choose how providers run

In **Project Settings > Plugins > Streaming Integrations**, set **Enabled Providers** to **Twitch Only**, **Kick Only**, or **Twitch + Kick**. **Preferred Provider** is only used when an action explicitly targets Preferred Provider; it never disables the other service.

Combined action nodes expose **Preferred Provider**, **Twitch**, **Kick**, and **All Enabled Providers**. The last option fans an action out to both services and reports success or failure for each provider before its final completion output.

## 5. Use friendly action nodes

Search Blueprint for actions such as **Get Twitch Users**, **Get Kick Livestreams**, **Send Twitch Chat Message**, **Ban Kick User**, or **Send Streaming Chat Message**. These asynchronous nodes build paths, query fields, JSON, scopes, and authorization headers internally. Bind success and failure; failures include provider, category, HTTP status, request ID, retryability, and retry delay.

Provider-neutral parsers convert successful responses into typed users, channels, streams, categories, clips, videos, emotes, badges, rewards, redemptions, subscriptions, polls, predictions, schedules, and pagination. Raw JSON remains available for forward compatibility.

The endpoint catalogs and generic request nodes remain under **Advanced** for uncommon or newly released provider operations. They are not the recommended first workflow.

## 6. Events and chat

For the shortest Blueprint setup, place the bundled listener Blueprint or add **Twitch Events**, **Kick Events**, or **Streaming Events (Twitch + Kick)** to a persistent Actor. Bind **On Chat Message**, **On Chat Command**, **On Follow**, **On Subscription**, **On Raid**, **On Cheer**, **On Reward Redemption**, **On Moderation**, **On Poll**, **On Prediction**, **On Goal**, **On Ad Break**, **On Hype Train**, and **On Stream State Changed**. Every structure includes its provider so one gameplay graph can handle both services.

The component can connect enabled providers at Begin Play, register command names, filter muted users, and maintain a bounded chat history for UMG. Chat messages expose ordered fragments, emotes, cheermotes, mentions, badges, user colors, and image URLs where available.

For Twitch desktop/game clients, connect the EventSub subsystem and create subscriptions using the returned session ID. Handle raw events or normalize them in game code. The subsystem handles welcome, keepalive, reconnect handover, revocation, and duplicate message IDs. Backend webhook handlers can use **Verify Twitch Webhook** to validate HMAC signatures, reject stale/replayed messages, parse events, and extract callback-verification challenges.

For the normal Blueprint workflow, wait until Twitch reports **Connected**, then call **Subscribe to Twitch Event**. Choose the event from its enum and pass the `Id` from **Get Logged In Twitch User**; the plugin builds the EventSub type, version, condition, transport, and session ID. Raw subscription JSON is Advanced-only.

Kick events arrive at the publisher's public backend. Deploy `Extras/WebhookReceiver`, register its `/webhooks/kick` HTTPS route in the Kick developer application, and set **Kick Event Bridge URL** to its `/events/kick` WSS route. The backend verifies every signature and replay timestamp, authenticates the game connection with Kick, and routes only that broadcaster's events. The **Kick Events** component connects automatically after **Connect Kick Account** and fires typed chat/event/command delegates. Never expose a client secret or an unverified webhook directly from a packaged game.

## 7. Test without going live

Use the component's **Simulate...** Blueprint nodes to emit chat, commands, follows, subscriptions, raids, cheers, and reward redemptions. The simulator uses the same delegates and structures as live events. It is disabled in Shipping builds unless explicitly allowed.

During Play In Editor, the dashboard's **Event Playground** can trigger the same sample events against every active listener component. Twitch-only and Kick-only editions include their own ready-to-place listener Blueprint and theme. The combined edition also includes its profile and premium chat overlay.

## 8. Profiles

Create a **Streaming Integration Profile** DataAsset to store the action target, requested scopes, event types, separate Twitch/Kick broadcaster identities, and startup behavior. Profiles intentionally contain no credentials. Create a **Streaming Widget Theme** DataAsset for provider accents, panels, text, spacing, corner radius, and typography.

## Shipping checklist

- Use HTTPS for redirects and brokers outside local development.
- Request the minimum scopes required by enabled features.
- Test token expiry, logout, cancellation, rate limits, EventSub reconnect, and webhook replay rejection.
- Run **Run Streaming Setup Diagnostics** and resolve every error before packaging.
- Package with `Tools/PackageProducts.ps1`, then validate each output with Unreal AutomationTool `BuildPlugin`.

Official references: [Twitch authentication](https://dev.twitch.tv/docs/authentication), [Twitch EventSub](https://dev.twitch.tv/docs/eventsub/), [Kick documentation](https://docs.kick.com/).
