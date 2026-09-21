# AUTH

Send crypto to any @username. AUTH maps social identities to Solana payment destinations, lets
creators route their token fees however they want, and keeps every payment on a
public, verifiable ledger.

Not affiliated with X Corp.

## What it does

- **Pay by @username** — pick a handle, an asset and an amount, authorize, send.
  Recipients who haven't set up an account yet get a claimable payment.
- **Claims** — a recipient proves ownership of their X handle, then points the
  handle at the wallet they want paid.
- **Launches** — create a token through pump.fun, with optional dev buy, and
  configure how creator fees are routed.
- **Fee routing** — split creator fees across wallets, buyback, burn or charity.
- **Ledger & analytics** — real mainnet signatures, balances and activity.
- **Asset registry** — 55+ verified Solana mainnet assets, keyed by exact mint,
  decimals and token program.

AUTH never holds private keys or seed phrases.

## Tech stack

- React 19 + TanStack Start (SSR, server functions) on Vite 7
- Tailwind CSS v4 + shadcn/ui
- Postgres (Supabase) with row-level security, Drizzle migrations
- Solana mainnet via Helius RPC

## Getting started

```bash
bun install
bun run dev
```

The app runs at http://localhost:8080.

## Environment

Create a `.env` (never commit it) with:

```
VITE_SUPABASE_URL=
VITE_SUPABASE_PUBLISHABLE_KEY=
SUPABASE_URL=
SUPABASE_PUBLISHABLE_KEY=
SUPABASE_SERVICE_ROLE_KEY=
HELIUS_API_KEY=
PUMPPORTAL_API_KEY=
PINATA_JWT=
```

Server-only values are read inside server functions via `process.env`; only
`VITE_*` values reach the browser.

## Architecture

**Social identity payments and claims.** Every recipient is a canonical
`(provider, immutable provider_user_id)` identity — never a text handle. Funds
sent to an unclaimed identity settle immediately into a protected,
identity-specific deposit wallet and stay attributable to that identity until
its real owner proves ownership through the platform's own OAuth flow and
claims them.

**Platforms.** X, YouTube and Twitch are live. TikTok and Instagram are shown
in the product as coming soon, pending TikTok Login Kit approval and Meta app
review.

**Chain.** Solana mainnet. SOL, USDC and a verified mint registry, with
on-chain confirmation required before any balance or claim is marked complete.

**Token launches.** Tokens are created through pump.fun with a freshly
generated mint. The AUTH launch wallet covers only launch/network/provider
infrastructure cost — it is never the beneficial owner of a token supply.

**Dev buys.** An optional dev buy is funded by the launcher, not by AUTH. The
launcher supplies the destination wallet, and the purchased allocation is
forwarded there; delivery is only reported after on-chain confirmation.

**Creator fees.** Creator fees belong to the social recipient identity attached
to the launch, entirely separate from dev-buy ownership.

**Custody.** Turnkey backs both the per-identity deposit wallets and each
claimed account's primary payout wallet. The app never handles, requests or
stores a user's private key.

**Creator-fee reconciliation.** A scheduled job runs every 5 minutes as the
fallback source of truth, reading on-chain creator-vault activity, attributing
it to the canonical identity, and writing immutable, idempotent ledger credits.

## External services required

Lovable Cloud (Postgres, auth, storage), Helius RPC, PumpPortal / pump.fun,
Pinata (token metadata pinning), Turnkey, and the OAuth apps for X, Google/
YouTube, Twitch, TikTok and Meta/Instagram. Copy `.env.example` for the
variable names; real values are stored as server-side secrets only.

## Database

Migrations live in `drizzle/migrations` and are applied in order.

```bash
bunx drizzle-kit check
```

## Scripts

```bash
bun run dev      # local dev server
bun run build    # production build
bunx tsgo --noEmit   # typecheck
```

## Status

Solana mainnet SOL, USDC and the verified asset registry are live. Token
launches require pump.fun provider credentials. Other chains are listed for
discovery only and are not payable.

## License

Proprietary. All rights reserved.
