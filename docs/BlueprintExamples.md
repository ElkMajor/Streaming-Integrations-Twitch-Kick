# Blueprint Examples

## Ready-to-place manager Blueprints

- `BP_StreamingManager` (combined edition) contains the production **Streaming Events (Twitch + Kick)** component, separate `ConnectTwitchAccount` and `ConnectKickAccount` custom events wired to the production account nodes, Chat Bot permission presets, post-login provider connection, a registered `play` command, and a real **On Chat Command** handler.
- `BP_TwitchStreamManager` contains the production **Twitch Events** component, Twitch account connection, EventSub connection after login, and the same command handler.
- `BP_KickStreamManager` contains the production **Kick Events** component, Kick account connection, and the same verified-event command handler. Live Kick events still enter through the documented secure webhook bridge.

Place the appropriate manager in a persistent level. Call its public `ConnectTwitchAccount` or `ConnectKickAccount` custom event from a menu button. The examples intentionally use no demo-only request or event nodes; replace the included Print String after **On Chat Command** with game logic.

## Recommended: drop-in simultaneous events

1. Set **Enabled Providers** to Twitch Only, Kick Only, or Twitch + Kick.
2. Add **Streaming Events (Twitch + Kick)** to a persistent Actor.
3. Enable **Connect Enabled Providers on Begin Play**.
4. Bind the typed events you need. Read `Provider` from the event only when provider-specific behavior is desired.
5. Add command names to **Commands to Register**, or call **Register Streaming Chat Command** with optional moderator-only and cooldown rules.

The component also exposes bounded chat history and filtering suitable for a UMG chat feed.

## Standalone provider event components

- **Twitch Integration:** place `BP_TwitchEventListener` or add **Twitch Events** to an Actor. It connects to EventSub on Begin Play by default and exposes typed chat, follow, subscription, reward, stream-state, raid, cheer, moderation, poll, prediction, goal, ad-break, and hype-train events.
- **Kick Integration:** place `BP_KickEventListener` or add **Kick Events** to an Actor. Verify the original webhook request with **Verify And Parse Kick Webhook**, then pass the returned event to **Process Verified Kick Event**. The component exposes the same provider-neutral typed event set.

Both components include command registration with moderator/cooldown rules and the full offline simulator set. Simulation is disabled in Shipping unless explicitly enabled in project settings.

## Sign in and use named actions

1. Add optional **Connect Twitch** and/or **Connect Kick** buttons to the game's menu.
2. On click, call **Connect Twitch Account** or **Connect Kick Account** with a permission preset such as Chat Bot or Interactive Stream.
3. Bind **On Connected** and **On Failure**. The browser and localhost callback are handled automatically.
4. Use **Is Authenticated** to show Connected, Reconnect, Disconnect, and Play Without Streaming states.
5. Call a named action such as **Get Twitch Users**, **Get Kick Channel**, or **Send Streaming Chat Message**.
6. For an All Enabled Providers action, handle each provider result and then the final completion output.
7. Parse typed users, channels, streams, or pagination when the node returns an API response.

The manual **Begin Authorization (Advanced)**, callback parser, authorization code, and returned-state nodes are retained for hosted redirects and custom platform integrations. Normal game UI does not need to split or parse a URL.

Example endpoint IDs:

- Twitch: `get_users`, `get_streams`, `send_chat_message`, `create_clip`, `ban_user`.
- Kick: `users`, `livestreams`, `chat`, `moderation_bans`, `events_subscriptions`.

The generated endpoint catalogs remain available under Advanced for uncommon operations and forward compatibility.

## Friendly Twitch action nodes

The primary Twitch Blueprint surface includes users, streams, channel information and updates, followers, chat messages and deletion, announcements, shoutouts, bans, unbans, clips, raids, custom rewards and redemptions, polls, predictions, and EventSub subscription management. Arrays such as user IDs and redemption IDs are encoded as repeated Helix query keys automatically.

Typical workflows:

- **Channel setup:** `Update Twitch Channel` with title, category/game ID, and language.
- **Community:** `Send Twitch Chat Announcement`, `Send Twitch Shoutout`, or `Start Twitch Raid`.
- **Rewards:** `Create Twitch Custom Reward`, then `Get Twitch Reward Redemptions` and `Update Twitch Reward Redemptions`.
- **Interactive shows:** `Create Twitch Poll` / `End Twitch Poll`, or `Create Twitch Prediction` / `End Twitch Prediction`.

Enum pins expose only supported announcement colors and lifecycle statuses, keeping raw protocol strings out of gameplay Blueprints.

Discovery and content nodes include **Search Twitch Channels**, **Search Twitch Categories**, **Get Twitch Games**, **Get Twitch Top Games**, **Get Twitch Clips**, **Get Twitch Videos**, and **Get Twitch Stream Schedule**. Community and chat lookup nodes include broadcaster subscriptions, moderators, VIPs, chatters, emotes, and badges. Parse their responses with the matching provider-neutral category, clip, video, emote, or badge parser node.

## Friendly Kick action nodes

The primary Kick Blueprint surface includes users, channels and updates, categories, livestream discovery and statistics, chat send/reply/delete, moderation bans, channel rewards and redemptions, event subscriptions, and advertising status/actions. Use the generic advanced request node only for newly released or uncommon endpoints that do not yet have a friendly wrapper.

## Combined provider targeting

Choose **Twitch**, **Kick**, or **All Enabled Providers** on combined actions. **Preferred Provider** uses the centrally configured preference. Events always listen to every enabled provider and do not use the preferred-provider setting.

Unified fan-out actions include chat send/delete, live streams, users, channels, ban, and unban. Each selected provider produces its own success or failure callback followed by one final completion callback, so one unavailable service never hides the other provider's result.

## Premium chat overlay

Add `WBP_StreamingChatOverlay` to the viewport and point it at the same **Streaming Events (Twitch + Kick)** component. It provides provider labels, connection state, sender colors, badges, emotes, cheermotes, GIF fragments, bounded history, empty states, auto-scroll, and theme controls.

For Twitch badges, call **Get Twitch Channel Badges** and **Get Twitch Global Badges**, parse them with **Parse Streaming Badge Assets**, then call **Set Badge Assets** on the overlay. Use the equivalent emote actions and **Parse Streaming Emotes** with **Set Emote Assets**. Remote images are downloaded once per URL and cached by the widget.

## Setup diagnostics

Call **Run Streaming Setup Diagnostics** at development time to receive typed, actionable issues for missing modules/app IDs, invalid redirects, insecure token brokers, sensitive logging, and Shipping simulator exposure. The editor dashboard runs the same diagnostics automatically.

## Offline gameplay testing

Bind your normal component events, then call **Simulate Streaming Chat Message**, **Simulate Streaming Follow**, **Simulate Streaming Subscription**, **Simulate Streaming Raid**, **Simulate Streaming Cheer**, or **Simulate Streaming Reward Redemption**. Commands triggered by simulated chat pass through the same registration, permission, and cooldown rules as live messages.

## Twitch EventSub

1. Authorize a user with scopes required by the chosen subscription types.
2. Call **Connect Enabled Providers** on the event component.
3. Wait for **On Provider Connection Changed** to report Twitch as Connected.
4. Call **Subscribe to Twitch Event** and select a typed event such as **Chat Message**. Connect the `Id` returned by **Get Logged In Twitch User** to Broadcaster User ID. The node supplies the official event name, version, condition fields, WebSocket transport, and active session ID.
5. Bind the component's typed **On Chat Message**, **On Chat Command**, and other event delegates. Raw EventSub JSON is optional.

When a connection is lost, recreate subscriptions after the next welcome. A server-requested reconnect is handed over without creating a second subscription set.

## Webhooks

Webhook HTTP reception belongs on a public HTTPS backend. Use `Verify Twitch Webhook` or `Verify And Parse Kick Webhook` inside an Unreal-based trusted server only when it can receive the original unmodified request body and headers. Forward verified normalized events to packaged clients through your own authenticated channel.
