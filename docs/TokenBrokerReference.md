# Local HTTPS Token Broker

This developer tool completes Twitch/Kick OAuth without putting a client secret in Unreal content or Blueprint. It binds only to `127.0.0.1` and keeps credentials in the broker process memory.

## One-time setup

1. In PowerShell, run `SetupLocalCertificate.ps1`. This creates and trusts a localhost-only certificate for the current Windows user.
2. Copy `.local-certs/localhost.pem` to `<YourProject>/Content/Certificates/cacert.pem`, then restart Unreal Editor. Unreal uses its own certificate bundle and loads this file during startup.
3. Start `StartLocalBroker.ps1`, choose the provider, and enter the developer application's Client ID and Client Secret when prompted.
4. In Unreal Project Settings > Streaming Integrations, set **Token Broker Base URL** to `https://localhost:17564`.
5. Keep Kick Redirect URI set to `http://localhost:17563/callback/kick` in both Unreal and the Kick developer dashboard.
6. Keep the broker window open while testing `Connect Kick Account`.

Test the broker at `https://localhost:17564/health`.

## Security boundary

Do not package `.local-certs`, this broker, or a Client Secret with a released game. This is a localhost development tool. For customers/players, deploy the same endpoint contract to an authenticated HTTPS service and put that service URL in Unreal settings.
