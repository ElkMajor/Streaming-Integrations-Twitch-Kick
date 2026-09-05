# Token Broker Contract

The broker keeps provider client secrets and refresh tokens outside packaged Unreal content. It must require your own application/session authentication, restrict origins where applicable, rate-limit requests, redact logs, and use HTTPS.

The plugin includes a working localhost development implementation under `Extras/TokenBroker`. See its README for the one-time certificate setup and launcher. It is for developer testing only; deploy an authenticated implementation for released games.

## `POST /oauth/exchange`

Request JSON:

```json
{
  "provider": "twitch",
  "code": "authorization-code",
  "code_verifier": "pkce-verifier",
  "redirect_uri": "https://your-app.example/oauth/callback"
}
```

Successful response JSON:

```json
{
  "access_token": "opaque-access-token",
  "refresh_token": "opaque-refresh-token-or-empty",
  "token_type": "Bearer",
  "expires_in": 3600
}
```

Return non-2xx status codes with a safe JSON error message. Do not pass provider client secrets to Unreal. A production broker should bind the authorization attempt to the signed-in application user, allowlist redirect URIs, validate the provider, and rotate refresh tokens atomically.

## `POST /oauth/refresh`

Accept `provider` and `refresh_token`. Return the same token JSON shape as `/oauth/exchange`; `scopes` may be an array. Refresh-token rotation is supported: return the replacement token when the provider issues one.

## `POST /oauth/app-token`

Accept `provider`. Authenticate the calling product/user through your own session layer, then perform the provider's client-credentials flow with the secret stored at the broker. Return a short-lived app access token using the standard token JSON shape. In API requests, set `Authentication Mode` to `App Token` for operations/transports that require it.

## `POST /oauth/revoke`

Accept `provider`, plus the available `access_token` and/or `refresh_token`. Revoke the credential with the provider and return any 2xx response only after revocation succeeds. The plugin clears its local session after that response.

Keep long-lived refresh tokens at the broker whenever your product architecture permits it. The included contract also supports transient client-held refresh tokens for native-app deployments, but they are never exposed as Blueprint pins, settings, logs, or assets.
