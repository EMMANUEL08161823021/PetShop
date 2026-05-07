# PawChain

A hybrid decentralised pet marketplace built on Solana, combining a React 18 front-end, Supabase Postgres back-end, and on-chain SOL settlement — with a custodial wallet layer that removes Web3 onboarding friction for everyday users.

🔗 **Live Demo:** [pet-shop-umber-three.vercel.app](https://pet-shop-umber-three.vercel.app)

---

## Overview

PawChain lets users browse pets, sign up with just an email, and pay in SOL — without ever installing a wallet extension or managing a seed phrase. Every purchase is settled on Solana Devnet and independently verifiable on Solana Explorer. The architecture bridges Web2 usability and Web3 transparency by silently provisioning a custodial wallet for each new user behind the scenes.

---

## Tech Stack

| Layer | Technology |
|---|---|
| Front-end | React 18, TypeScript, Tailwind CSS, React Router |
| Auth | Supabase Auth (Email + Google OAuth) |
| Database | Supabase (PostgreSQL) with Row Level Security |
| Blockchain | Solana Devnet + @solana/web3.js |
| Wallet | TweetNaCl, bs58 (Ed25519 keypair) |
| DevOps | Git, Vercel, ESLint, CRACO |

---

## Architecture

PawChain follows a three-tier hybrid architecture:

1. **Presentation layer** — React 18 SPA with TypeScript, responsive via Tailwind CSS, role-based UI through a `useAdmin` hook.
2. **Persistence layer** — Supabase (PostgreSQL) manages users, wallets, products, orders, and admins. Row Level Security enforces per-user data isolation directly at the database layer.
3. **Settlement layer** — Solana Devnet handles on-chain SOL transfers via the `useCheckout` custom hook, with transaction hashes stored back to Supabase for auditability.

When a user clicks **Buy Now**, an eight-step pipeline runs:
fetches encrypted key → decrypts in memory → reconstructs keypair → builds transfer instruction → submits to Devnet → awaits confirmation → writes tx signature to orders table → displays verifiable receipt.

---

## Features

- Email and Google OAuth sign-up with automatic custodial Solana wallet provisioning
- Pet product listings with admin-only create/edit controls
- On-chain SOL payment via Solana Devnet, no browser wallet extension required
- Transaction signatures linked to Solana Explorer for independent verification
- Responsive UI: horizontal carousel on mobile, three-column grid on desktop
- Row Level Security policies protecting all user data at the database layer
- Continuous deployment via Vercel on every push to `main`

---

## Getting Started

### Prerequisites

- Node.js ≥ 18
- npm ≥ 9
- A free [Supabase](https://supabase.com) project
- Git

### Installation

```bash
git clone https://github.com/Olatunjitaiwosam/PetShop.git
cd PetShop
npm install
```

### Environment Variables

Create a `.env` file in the project root:

```env
REACT_APP_SUPABASE_URL=<your supabase url>
REACT_APP_SUPABASE_ANON_KEY=<your anon key>
REACT_APP_SOLANA_NETWORK=devnet
REACT_APP_STORE_WALLET=<your devnet store wallet address>
```

### Database Setup

Run the schema SQL found in the `/supabase` directory using the Supabase SQL Editor. Then seed your first admin user:

```sql
INSERT INTO admins (user_id)
VALUES ((SELECT id FROM auth.users WHERE email = 'you@example.com'));
```

### Run Locally

```bash
npm start
# App runs at http://localhost:3000
```

### Production Build

```bash
npm run build
```

Vercel automatically builds and deploys on every push to `main`.

---

## Project Structure

```
PetShop/
├── src/
│   ├── components/       # UI components
│   ├── context/          # AuthContext, ModalContext
│   ├── hooks/            # useAdmin, useCheckout, etc.
│   ├── utils/            # walletUtils.ts (keypair generation/encryption)
│   └── pages/            # Route-level views
├── supabase/             # Schema SQL and RLS policies
├── public/
└── .env                  # Environment variables (not committed)
```

---

## Known Limitations

- **Custodial wallet risk** — encrypted secret keys are stored in Supabase. A production deployment would use HSM-backed key management or an embedded non-custodial provider (e.g. Privy).
- **Basic encryption** — `walletUtils.ts` uses base64 encoding with a UUID prefix, sufficient for a Devnet demo but not production-grade. AES-GCM or KMS-backed envelope encryption would be required before Mainnet use.
- **Devnet only** — SOL has no real economic value on Devnet. A Mainnet migration would require a formal security audit and compliance review.

---

## Code Quality

- **TypeScript strict mode** — all components, hooks, and interfaces are fully typed.
- **ESLint** — extended with `react-app` and `react-app/jest` presets; warnings treated as errors in CI.
- **Conventional Commits** — 40+ commits following `feat:`, `fix:`, `chore:` convention for a clean, readable history.
- **CRACO** — extends CRA with Node.js polyfills required by Solana Web3.js without ejecting.

---

