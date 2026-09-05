# Troubleshooting

## Authentication succeeds in the browser but Unreal reports state failure

Use the exact callback paired with the most recent authorization attempt. State values are case-sensitive and attempts expire after ten minutes. Do not start a second flow while handling the first callback.

## API responds with 401

Confirm the token belongs to the configured Client ID and provider, has not expired or been revoked, and is the token type required by that operation. Twitch third-party applications must validate maintained OAuth sessions; refresh or authorize again when validation fails.

## API responds with 403

The user token is valid but lacks a required scope, the authenticated user is not the broadcaster/moderator required by the operation, or the application has not received a required platform approval.

## EventSub connects but receives nothing

Create subscriptions after the welcome message using the current session ID. On a normal disconnect, create them again for the new session. Check each event type's version, condition fields, and scopes in the current official documentation.

## Kick webhook verification fails

Pass the exact raw body and original headers, without whitespace normalization. Ensure the timestamp format is ISO-8601 and that clocks are synchronized. The included public key can be overridden if Kick rotates it.

## Requests are rate limited

Read `RateLimitRemaining`, `RateLimitResetsAtUtc`, and the structured error's `RetryAfterSeconds`. Queue or back off retryable work; do not immediately retry every Blueprint tick.
