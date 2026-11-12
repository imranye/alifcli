# Self-Hosting On Cloudflare

Alif is built for Cloudflare:

- Workers for the API
- D1 for relational storage
- Queues for async metric evaluation
- Cron Triggers for scheduled backend work
- Email Sending for production OTP delivery

## Create Resources

```bash
npx wrangler d1 create alif-db
npx wrangler queues create alif-metric-events
```

Copy the D1 `database_id` into `wrangler.jsonc`.

## Email OTP Delivery

Enable Cloudflare Email Sending for a domain:

```bash
npx wrangler email sending enable yourdomain.com
npx wrangler email sending list
```

Add the binding and production vars to `wrangler.jsonc`:

```jsonc
{
  "send_email": [{ "name": "EMAIL" }],
  "vars": {
    "ALIF_ENV": "production",
    "REQUIRE_EMAIL_OTP": "true",
    "OTP_FROM_EMAIL": "login@yourdomain.com",
    "OTP_FROM_NAME": "Alif"
  }
}
```

Without an Email Sending binding, the Worker refuses OTP requests with `email_delivery_unavailable` and sends no code. A public deployment must use Email Sending.

## Local Development OTP

Local development can read the OTP from the API response. Put both values in `.dev.vars`:

```bash
ALIF_ENV="development"
ALLOW_DEV_OTP_RESPONSE="true"
```

The fallback needs both values. The Worker never returns the OTP when `ALIF_ENV` is `production`. Do not set `ALLOW_DEV_OTP_RESPONSE` on a public deployment.

## Migrate And Deploy

```bash
npm run db:migrate:remote
npm run deploy
```

Optional private beta gate:

```bash
npx wrangler secret put SIGNUP_SECRET
```

Then pass it during application creation:

```bash
ALIF_SIGNUP_SECRET=... npx alif apply --api-url https://your-worker.example.workers.dev
```
