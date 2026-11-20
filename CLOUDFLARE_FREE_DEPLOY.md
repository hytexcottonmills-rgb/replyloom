# ReplyLoom — Cloudflare Free-Plan Deployment

This branch prepares ReplyLoom for a reduced deployment using Cloudflare's Workers Free allowances.

## Deliberately disabled
- R2-backed WhatsApp media archive and template-attachment gallery.
- Sending image, video, audio, and document attachments from the CRM composer.
- Cloudflare built-in Rate Limiting bindings (these required a paid Workers plan in the original templates).

Incoming media messages remain recorded as message rows where possible, but their binary attachments are not downloaded or retained. Text messaging remains enabled.

## Isolated resources
- D1 database: `replyloom-crm` (separate from any other project database).
- Durable Objects: `WhatsAppSession` and `Scheduler` using SQLite storage.
- Queue: `wa-broadcast-dispatch`.
- Vectorize: `wa-messages` and `wa-docs`.
- KV: `REPLY_CACHE`.
- Workers AI: only within the account's free daily allowance; free-plan usage stops when the allowance is exceeded.

The bootstrap script does not create R2 buckets. It creates a new D1 database named `replyloom-crm` if it does not already exist. It does not select or modify `justclub-db`.

## Free-plan guardrails
Cloudflare free allowances are quotas, not unlimited service. Workers and Durable Objects have daily request/compute limits; D1 has daily row read/write and storage limits; KV, Queues, Vectorize, and Workers AI also have usage limits. Free-plan operations may fail after a quota is reached. Do not upgrade the account or enable paid usage without explicit approval.

## Required production configuration
Before deployment, configure the production app URL and engine URL in the generated Wrangler config and `apps/web/.env.production`. Set the same randomly generated `WS_TOKEN_SECRET` and `ENGINE_INTERNAL_SECRET` on both Workers, plus `BETTER_AUTH_SECRET` on the web Worker. Never commit secrets or generated `wrangler.jsonc` files.

## Important
This is a reduced text-first deployment. It is not yet deployed or production-verified. Run CI and deploy to a test hostname before connecting a real WhatsApp number.
