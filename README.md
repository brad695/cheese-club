# Greys Cheese Club — Subscription Site

A working recreation of the Table22-style cheese-club experience, built for Greys Fine Cheeses. It has three sides, all in one app:

- **Customer storefront** — browse the three clubs, pick pickup or delivery, and subscribe.
- **Member portal** — members manage, skip, pause, cancel, switch fulfillment, and edit their profile.
- **Partner portal (admin)** — operator dashboard with metrics, subscriptions, members, cohorts, and orders.

Everything runs on your own **Supabase** project (`brad695's Project`) — real database, real accounts, real subscriptions.

---

## How to open it

Double-click **`index.html`** (it opens in your browser). That's the whole site — it talks directly to your Supabase backend in the cloud, so it works from your computer with no install step.

> Want a real shareable web address (e.g. `https://…/cc-app`) instead of a local file? Ask me to "deploy the cheese club site live" and I'll host it for you.

## Accounts you can use right now

| Role | Email | Password |
|------|-------|----------|
| **Operator / Admin** | `owner@greyscheese.com` | `CheeseClub2026!` |
| Sample member | `ava.sample@example.com` | `Sample2026!` |
| Sample member | `marcus.sample@example.com` | `Sample2026!` |
| Sample member (paused) | `lena.sample@example.com` | `Sample2026!` |

Sign in as the operator to see the **Partner Portal**. Sign in as a sample member (or create a brand-new account from the storefront) to see the **member** experience.

> ⚠️ Please change the operator password after your first sign-in. The three `*.sample@example.com` members and their subscriptions are demo data — delete them anytime (ask me, or remove the rows in Supabase).

## The three clubs (placeholder pricing — easy to change)

| Club | Price (placeholder) | Notes |
|------|--------------------|-------|
| **Only Rinds Club** | $45 / mo | The base club |
| **Cured & Cultured Club** | $65 / mo | Cheese + charcuterie |
| **The Pairing Club – Wine & Cheese** | $85 / mo | Premium, cheese + wine |

Fulfillment: pickup at **Memphis / Nashville / Franklin** (included) or **Local Delivery** (+$15).

To change names, prices, or descriptions, edit the `cc_clubs` / `cc_fulfillment_options` tables in Supabase — or just tell me what to change.

---

## What's under the hood

**Database tables** (all prefixed `cc_`, Row-Level Security enabled on every one):
`cc_clubs`, `cc_fulfillment_options`, `cc_members`, `cc_cohorts`, `cc_subscriptions`, `cc_orders`.

Members can only see their own data; the operator account (flagged `is_admin`) sees everything. This is enforced in the database itself, not just the UI.

**Edge functions** (serverless, already deployed):
- `cc-signup` — creates confirmed member accounts instantly.
- `cc-checkout` — creates a Stripe Checkout session (test mode).
- `cc-stripe-webhook` — activates the subscription after payment.

## Payments

Out of the box the site runs in **demo mode**: completing checkout activates the membership instantly so you can use and show the whole flow. No card is charged.

To turn on real **Stripe test-mode** payments:

1. In Supabase → Edge Functions → Secrets, add `STRIPE_SECRET_KEY` (your Stripe **test** secret key, `sk_test_…`).
2. In Stripe, add a webhook to `https://vdvtrevhqalmjwhjhjug.supabase.co/functions/v1/cc-stripe-webhook` for the `checkout.session.completed` event, then add its signing secret to Supabase as `STRIPE_WEBHOOK_SECRET`.
3. In `index.html`, set `const STRIPE_ENABLED = true;` (near the top of the script).

Checkout will then redirect to Stripe; test card `4242 4242 4242 4242` works. (Entering live keys / real payment details is something you'd do yourself in Stripe — I don't handle live credentials.)

## A security note about your existing data

Your Supabase project has a pre-existing table, **`public.holds`**, with Row-Level Security **disabled** and a column named `token` — meaning anyone with your public key can read it. I did **not** touch it, but you should lock it down. I'm happy to help with that separately.

---

*Built in Cowork. Pricing is placeholder; swap in your real numbers anytime.*
