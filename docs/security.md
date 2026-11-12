# Security Notes

Implemented:

- write endpoints require authentication
- email OTP challenges are hashed before storage
- OTPs expire after 10 minutes
- human session tokens are separate from agent tokens
- API token secrets are hashed before storage
- OTP codes are never returned in an API response unless a development deployment sets `ALLOW_DEV_OTP_RESPONSE=true`
- metric writes are idempotent
- mutations are recorded in `audit_log`
- metric processing happens asynchronously through Cloudflare Queues

Recommended before broad public launch:

- enable Cloudflare Email Sending and set `REQUIRE_EMAIL_OTP=true` before public OTP login
- add Cloudflare rate limits for OTP, applications, and metric writes
- add token creation/list/revocation commands
- scope automation tokens to specific metric keys
- add Turnstile or invite-gating for public application creation
- put reviewer dashboard behind Cloudflare Access
- add CI tests for auth, idempotency, and alert generation
