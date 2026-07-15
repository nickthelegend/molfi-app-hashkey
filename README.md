# Molfi — Prediction Markets on HashKey Chain

> Bet YES/NO on short-horizon crypto price markets — with real on-chain escrow, an LP vault, and optional zero-knowledge private betting.

## Overview

Molfi is the web app for a prediction-markets protocol deployed on [HashKey Chain](https://hsk.xyz) (an EVM L2). Users connect a wallet, claim test **mUSDC**, and stake it on the YES/NO outcome of auto-rolling markets (e.g. "Will BTC be above the strike at close?"). Stakes are escrowed on-chain in a pari-mutuel pool and pay out to the winning side after settlement.

Markets are sourced from a backend market engine that runs a price feed and rolls fresh short-cadence markets, with a direct on-chain read path as a fallback. Beyond plain betting, the app supports a shared **liquidity vault** and a **confidential betting** flow where the chosen side is hidden behind Groth16 proofs verified on-chain.

This repository is the frontend only. It targets HashKey Chain testnet (chainId 133) by default and mainnet (chainId 177) via env override.

## Features

- **Pari-mutuel prediction markets** — stake mUSDC on YES/NO; funds are escrowed in the `PredictEscrow` contract and paid out pro-rata to winners on resolution.
- **On-chain mUSDC faucet** — one-click claim of the 6-decimal test token to fund a wallet.
- **Auto-rolling markets** — short-cadence (e.g. 30-minute) markets over assets like BTC, driven by a backend price/settlement engine, with raw on-chain market enumeration as a fallback.
- **Confidential / private bets** — hidden-side commitment notes (`ConfidentialBet`) and proof-gated escrow bets (`betZk`), with Groth16 proofs verified **on-chain** and nullifier-based claims. Notes are kept locally in the browser (their secrecy is what keeps the side hidden).
- **LP vault** — deposit mUSDC to mint vault shares at NAV and earn the protocol fee as yield; withdraw shares or the full position.
- **Live price charts** — candles/lines and premium sparklines via `lightweight-charts`, plus an oracle spot-price series.
- **Portfolio, points & leaderboard** — position tracking, PnL charting, and a volume-based points leaderboard.
- **Market comments** — Firestore-backed comment threads with emoji and GIF pickers.
- **Jarvis** — an optional automated keeper/assistant layer (settings, guardrails, live event stream) wired to a keeper API.
- **Wallet UX** — RainbowKit multi-wallet connect (MetaMask, WalletConnect, Coinbase, injected), EIP-3085 add-chain, and a mobile bottom nav.
- **Animated landing & pitch deck** — GSAP-driven marketing landing and a `/pitch` slide deck.

## Tech Stack

- **Framework:** React 19, TypeScript, Vite 7
- **Routing & data:** TanStack Router (file-based) + TanStack Query
- **Styling:** Tailwind CSS v4, Radix UI primitives (shadcn-style), `tw-animate-css`, GSAP
- **Chain / wallet:** wagmi + viem + RainbowKit, ethers v6, HashKey Chain (EVM)
- **Realtime:** socket.io-client, WebSocket indexer streams
- **Charts:** lightweight-charts
- **Other:** Firebase (Firestore comments), Zod, React Hook Form, Sonner, Valtio
- **Tooling:** Vitest, ESLint, Prettier; deployable on Vercel (Bun supported via `bunfig.toml`)

## Getting Started

```bash
# 1. Clone
git clone https://github.com/nickthelegend/molfi-app-hashkey.git
cd molfi-app-hashkey

# 2. Install dependencies (npm, or bun install)
npm install

# 3. Configure env — copy the example and fill in as needed
cp .env.example .env
# Defaults target HashKey Chain testnet (chainId 133, https://testnet.hsk.xyz).
# Contract addresses are baked in; a backend API URL, WalletConnect projectId,
# and Firebase keys are optional (see .env.example).

# 4. Run the dev server (http://localhost:8080)
npm run dev

# 5. Production build + preview
npm run build
npm run preview

# Lint / format / test
npm run lint
npm run format
npm run test
```

## Project Structure

```
molfi-app-hashkey/
├── index.html              # Vite entry (dark theme, global shim)
├── vite.config.ts          # Vite + TanStack Router + Tailwind + node polyfills
├── vercel.json             # SPA rewrites for Vercel
├── .env.example            # Network, contract, backend & Firebase config
├── public/                 # Static assets (boot.js, icons, comments)
├── scripts/                # Maintenance scripts (e.g. balance check)
└── src/
    ├── main.tsx            # App bootstrap
    ├── routes/             # File-based routes (markets, portfolio, vault, points, pitch, predictions/:id)
    ├── components/         # UI (charts, landing, leverx, pitch, ui primitives)
    ├── context/            # Wallet, stream, oracle & favorites providers
    ├── hooks/              # Markets, indexer, jarvis, chart & wallet hooks
    └── lib/
        ├── hsk/            # HashKey Chain: chain def, contracts, ABIs, ethers calls
        ├── predict/        # Predict oracle/market domain logic
        ├── leverx/         # Indexer, keeper (Jarvis) & trade helpers
        ├── charts/         # Chart data & lifecycle utilities
        ├── comments/       # Comment rendering & GIF catalog
        └── ...             # animation, api, firebase, config, utils
```

---

Built by [nickthelegend](https://github.com/nickthelegend) · [nickthelegend.tech](https://nickthelegend.tech)
