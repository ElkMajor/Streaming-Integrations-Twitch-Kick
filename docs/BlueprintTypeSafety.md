# Blueprint Type-Safety Rules

The normal Blueprint API uses enums and structs whenever Twitch or Kick defines a finite documented value set. Raw strings, JSON, maps, endpoint IDs, protocol headers, and custom future event names belong under **Advanced**.

Strings remain intentional for open-ended or provider-issued data: user/channel/message/reward/subscription IDs, usernames, titles, chat text, search text, cursor tokens, URLs, locale/language codes, custom tags, and webhook payload bodies.

## Typed workflow coverage

- Authentication uses provider, permission-preset, token-kind, operation, and enabled-provider enums plus typed status/error structs.
- Kick event subscription uses `Kick Event Type`; documented version 1 is mapped internally. Custom event name/version remains Advanced.
- Twitch EventSub status filtering uses `Twitch EventSub Subscription Status`; custom type/status filters remain Advanced.
- Poll, prediction, redemption, announcement, commercial, moderation, provider-target, HTTP verb, authentication-mode, and connection-state choices are enums.
- Poll choices, prediction outcomes, chat settings, users, channels, streams, messages, events, pagination, responses, and errors are structs.
- `Get Logged In Kick User` returns one typed `Streaming User` directly.

When adding an endpoint, a string pin is rejected from the normal Blueprint API if its valid values are a documented finite list. Add an enum and a private conversion helper instead.

