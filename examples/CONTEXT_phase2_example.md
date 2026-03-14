# Nova App — Context Anchor
> Last updated: 2026-03-14 11:28 | Session: PRE-COMPACT UPDATE — Stripe checkout done, webhook 80% complete

---

## 🚀 Resume Prompt

You are working on **Nova**, a Next.js 14 SaaS app with Stripe billing. The `createCheckoutSession` function is complete in `src/api/stripe/checkout.ts`. We are mid-way through the webhook handler in `src/api/stripe/webhook.ts` — the signature verification works but the `customer.subscription.updated` event handler is not yet written. The `Subscription` model has been added to Prisma schema but the migration has NOT been run yet (`npx prisma migrate dev` is the next step after finishing the webhook handler). **Critical:** never parse the raw request body before Stripe signature verification or it will always fail.

---

## 📍 Current Status
**Working on:** Stripe webhook handler — `customer.subscription.updated` event  
**Blocked by:** Nothing  
**Next up:** Write the `customer.subscription.updated` case in `src/api/stripe/webhook.ts`, then run `npx prisma migrate dev`

---

## 🗺️ Project Overview

**Purpose:** SaaS productivity tool for freelancers — time tracking, invoicing, client management  
**Stack:** Next.js 14 (App Router), Prisma + PostgreSQL, Stripe (billing), Zod (validation)  
**Structure:**
- `src/auth/` — 🔧 Planned. Not yet started.
- `src/api/stripe/checkout.ts` — ✅ Complete. `createCheckoutSession` implemented.
- `src/api/stripe/webhook.ts` — 🔧 In progress. Signature verification done, event handlers incomplete.
- `prisma/schema.prisma` — Has `User` + `Subscription` models. **Migration not yet run.**
**Repo/Location:** `/home/user/projects/nova-app`

---

## 📋 Active Work

### Stripe Webhook Handler
- **Goal:** Handle `checkout.session.completed`, `customer.subscription.updated`, `customer.subscription.deleted` events
- **Approach:** Switch on `event.type`, update `Subscription` table via Prisma for each relevant event
- **Status:** In progress — signature verification complete, `checkout.session.completed` handler done, `customer.subscription.updated` handler not yet written
- **Key decisions made:**
  - Idempotency via `stripeEventId` stored on processed events — *because Stripe can send duplicate webhooks*
  - Upsert pattern for subscription records — *because subscription may or may not exist at webhook time*
- **Notes:** The raw body middleware must be applied BEFORE the JSON body parser in Next.js middleware chain

---

## ✅ Resolved

### Stripe Checkout Session ✓
- **What was done:** `createCheckoutSession` function in `src/api/stripe/checkout.ts`. Creates a Stripe Checkout session and returns the URL.
- **Key decision:** Hosted Checkout over custom payment form — PCI compliance
- **Outcome:** Working. Redirects user to Stripe-hosted checkout page. `stripeCustomerId` saved to User on success callback.

---

## 📝 Decision Log

| Decision | Chosen approach | Reason | Date |
|----------|----------------|--------|------|
| Billing UI | Stripe Hosted Checkout | Avoids PCI compliance complexity | 2026-03-14 |
| Customer linking | `stripeCustomerId` on User | Direct lookup without extra table | 2026-03-14 |
| Framework | Next.js 14 App Router | Modern patterns, good ecosystem | 2026-03-14 |
| Webhook idempotency | Store `stripeEventId` | Stripe can send duplicate events | 2026-03-14 |
| Subscription writes | Upsert pattern | Subscription may not exist at webhook time | 2026-03-14 |

---

## ⚠️ Gotchas & Known Issues

- **Raw body is required for Stripe webhook verification** — do NOT parse as JSON before calling `stripe.webhooks.constructEvent()` or signature check always fails
- **Raw body middleware must come BEFORE JSON parser** in Next.js middleware chain — discovered this the hard way
- Stripe CLI must be running locally for dev webhooks: `stripe listen --forward-to localhost:3000/api/stripe/webhook`
- Prisma migration not yet run — `Subscription` table does not exist in DB yet

---

## 🗄️ Archive

<details>
<summary>Archived items (click to expand)</summary>

*(Nothing archived yet)*

</details>
