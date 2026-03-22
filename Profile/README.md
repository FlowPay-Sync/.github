<div align="center">

<img src="https://img.shields.io/badge/FlowPay-Sync-6366f1?style=for-the-badge&logoColor=white" alt="FlowPay Sync"/>

# FlowPay Sync

### Omnichannel AI Sales Agent for LATAM

*Connecting the chaos of WhatsApp with the order of Clover (Fiserv)*

[![Next.js](https://img.shields.io/badge/Next.js-16-black?style=flat-square&logo=next.js)](https://nextjs.org)
[![TypeScript](https://img.shields.io/badge/TypeScript-5-3178C6?style=flat-square&logo=typescript)](https://www.typescriptlang.org)
[![Tailwind CSS](https://img.shields.io/badge/Tailwind-3-38BDF8?style=flat-square&logo=tailwindcss)](https://tailwindcss.com)
[![Claude AI](https://img.shields.io/badge/Claude-AI-d97706?style=flat-square)](https://anthropic.com)
[![Clover](https://img.shields.io/badge/Clover-Fiserv-22c55e?style=flat-square)](https://clover.com)
[![Fiserv](https://img.shields.io/badge/Fiserv-Loyalty-00a651?style=flat-square)](https://fiserv.com)

---

> **Hackathon Aleph · Track Fiserv/Clover**
>
> 70% of sales in LATAM SMBs happen on WhatsApp.
> Their Clover POS has no idea — until now.

</div>

---

## Table of Contents

- [The Problem](#the-problem)
- [The Solution](#the-solution)
- [Key Features](#key-features)
- [System Architecture](#system-architecture)
- [Tech Stack](#tech-stack)
- [Project Structure](#project-structure)
- [Installation & Setup](#installation--setup)
- [Environment Variables](#environment-variables)
- [API Reference](#api-reference)
- [Frontend Modifications](#frontend-modifications)
- [Live Demo](#live-demo)
- [Roadmap](#roadmap)

---

## The Problem

SMBs in LATAM live a daily contradiction:

| Channel | Reality |
|---|---|
| **WhatsApp** | +60% of sales are managed here, in unstructured text |
| **Clover POS** | Formal system, integrated with Fiserv, but isolated from social channels |
| **The cashier** | Manual intermediary who copies orders from WhatsApp to POS → errors, wasted time, no traceability |

**Result:** Untracked revenue, desynced inventory, zero data for decisions.

---

## The Solution

FlowPay Sync 2.0 is a **three-layer Autonomous Sales Agent**:

```
Customer on WhatsApp
        │
        ▼  Chaotic message in natural language
┌───────────────────────────────────────────┐
│           FLOWPAY SYNC 2.0                │
│                                           │
│  ┌─────────┐   ┌──────────┐   ┌────────┐ │
│  │ Claude  │──▶│ Clover   │──▶│Loyalty │ │
│  │   AI    │   │  Fiserv  │   │ Fiserv │ │
│  └─────────┘   └──────────┘   └────────┘ │
│   Parses order  Creates POS order Loyalty │
└───────────────────────────────────────────┘
        │
        ▼  Payment link + automatic confirmation
Customer receives reply on WhatsApp (zero human intervention)
```

---

## Key Features

### Feature 1 — Zero-Click AI Bot

> The cashier never touches the keyboard. AI handles everything.

The customer writes on WhatsApp:

```
"Hey, send me 2 burgers with fries to 123 Main St,
and hold the mayo on one of them"
```

FlowPay Sync, in milliseconds and **with zero human intervention**:

1. Receives the message via Twilio → `POST /api/whatsapp-webhook`
2. Claude AI parses the chaotic text into structured JSON
3. Creates the order in Clover POS via REST API
4. Generates the payment link with a personalized upsell
5. Automatically replies to the customer with the URL

The cashier only watches as the order *appears* on the Dashboard.

---

### Feature 2 — Bidirectional Clover Sync

> FlowPay doesn't replace Clover — it supercharges it.

```
Customer pays link  ──▶  Fiserv Webhook  ──▶  Dashboard PAID ✓
                                  │
                                  └──▶  Clover POS marked
                                        "Ready to prepare"
```

- Next.js Dashboard updates in **< 3 seconds** via automatic polling
- Order appears on the physical POS screen without manual action
- Clover ID is traced on every transaction in the history

---

### Feature 3 — Fiserv Loyalty Rewards

> Connecting digital sales with physical foot traffic. Two worlds in one.

After a successful payment:

- FlowPay registers a discount reward via **Fiserv Loyalty**
- The customer sees on screen: *"Thanks for your purchase! You just earned a loyalty reward — show it on your next visit for a free coffee"*
- Drives foot traffic back to the physical Clover POS

**Why it's brilliant:** Connects WhatsApp (digital) → Fiserv Loyalty (rewards) → Clover (physical).

---

## System Architecture

```
                          ┌────────────────────────────────────────┐
                          │            NEXT.JS 16 (port 3000)       │
                          │                                          │
  WhatsApp ──▶ Twilio ──▶ │  /api/whatsapp-webhook  (Claude AI)    │
                          │                                          │
  Customer payment ──────▶│  /api/webhook           (Fiserv sync)  │
                          │                                          │
  Dashboard polling ────▶ │  /api/metrics           (3s interval)  │
                          │  /api/transactions      (3s interval)  │
                          │                                          │
  Create link ──────────▶ │  /api/create-link       (AI upsell)    │
                          └──────────────┬─────────────────────────┘
                                         │
                    ┌────────────────────┼────────────────────┐
                    ▼                    ▼                     ▼
             ┌──────────┐        ┌─────────────┐      ┌───────────────┐
             │  Clover  │        │  lib/store  │      │    Fiserv     │
             │  Fiserv  │        │ (in-memory) │      │   Loyalty     │
             │ Sandbox  │        │             │      │   Rewards     │
             └──────────┘        └─────────────┘      └───────────────┘
```

**Core principle:** No external server. The entire backend lives as API Routes inside the same Next.js process. Same port, zero CORS, single-command deploy.

---

## Tech Stack

### Frontend
| Technology | Purpose |
|---|---|
| **Next.js 16** (App Router) | Main framework |
| **TypeScript** | Static typing |
| **Tailwind CSS** | Utility-first styling |
| **Framer Motion** | Professional animations |
| **Recharts** | Dashboard charts |
| **Radix UI** | Accessible components |

### Backend (API Routes)
| Technology | Purpose |
|---|---|
| **Next.js API Routes** | 6 serverless endpoints |
| **Claude AI (Anthropic)** | Natural language order parsing |
| **Clover REST API v3** | POS order creation |
| **Fiserv Webhooks** | Payment confirmation |
| **Twilio** | WhatsApp gateway |

### Fiserv Loyalty
| Technology | Purpose |
|---|---|
| **Fiserv Loyalty API** | Rewards and loyalty program |
| **Clover REST API v3** | POS loyalty order integration |

---

## Project Structure

```
flowpay-sync/
│
├── app/
│   ├── api/                          ← Full backend (API Routes)
│   │   ├── create-link/
│   │   │   └── route.ts              ★ Payment link engine + AI upsell
│   │   ├── webhook/
│   │   │   └── route.ts              ★ Core: receives payments + Clover sync
│   │   ├── metrics/
│   │   │   └── route.ts              ★ Real-time metrics
│   │   ├── transactions/
│   │   │   └── route.ts              ★ Transaction history
│   │   └── whatsapp-webhook/
│   │       └── route.ts              ★ Zero-Click AI Bot (Claude + Twilio)
│   ├── page.tsx                       (Main dashboard — no changes)
│   └── layout.tsx                     (Global layout — no changes)
│
├── components/
│   └── dashboard/
│       ├── create-payment-dialog.tsx  ✏ Connected to POST /api/create-link
│       ├── stats-cards.tsx            ✏ Polling /api/metrics every 3s
│       ├── transactions-table.tsx     ✏ Polling /api/transactions + Clover ID
│       └── activity-feed.tsx          ✏ Real-time activity feed
│
├── lib/
│   ├── store.ts                       ★ In-memory global state (create first)
│   └── utils.ts                       (no changes)
│
├── .env.local                         ★ Credentials (do not commit)
├── package.json
└── tsconfig.json
```

> **Golden design rule:** Frontend changes are ONLY in data-fetching logic — never in JSX or styles. The UX built with Framer Motion and Recharts stays untouched.

---

## Installation & Setup

### Prerequisites

- Node.js 18+
- pnpm (project package manager)

### 1. Clone and install

```bash
git clone https://github.com/your-org/flowpay-sync.git
cd flowpay-sync

# Install base dependencies
pnpm install

# Add new backend dependencies
pnpm add @anthropic-ai/sdk
pnpm add twilio
```

### 2. Configure environment variables

```bash
cp .env.example .env.local
# Edit .env.local with your credentials (see Environment Variables section)
```

### 3. Implementation order

> Following this order is critical. `lib/store.ts` must exist before any API route.

```bash
# Step 1: Global state (FIRST AND MANDATORY)
# Create lib/store.ts — without this file, all routes will fail

# Step 2: Backend API Routes
# Create app/api/create-link/route.ts
# Create app/api/webhook/route.ts
# Create app/api/metrics/route.ts
# Create app/api/transactions/route.ts

# Step 3: Modify dashboard components
# create-payment-dialog.tsx — connect to real API
# stats-cards.tsx — metrics polling
# transactions-table.tsx — live table
# activity-feed.tsx — real-time feed

# Step 4: Start
pnpm dev
```

### 4. Verify everything works

```bash
# The webhook must respond:
curl http://localhost:3000/api/webhook
# ✓ {"status":"FlowPay webhook active ✓"}

# Metrics must return JSON:
curl http://localhost:3000/api/metrics
# ✓ {"totalRevenue":45231.89,"totalSales":2350,...}
```

---

## Environment Variables

Create `.env.local` in the project root.

```env
# ─── BASE ──────────────────────────────────────────────────────
NEXT_PUBLIC_BASE_URL=http://localhost:3000

# ─── CLOVER / FISERV (Sandbox) ─────────────────────────────────
# Without these variables: automatic simulated mode — OK for demo
CLOVER_MERCHANT_ID=your_merchant_id_here
CLOVER_API_TOKEN=your_api_token_here

# ─── TWILIO WHATSAPP ────────────────────────────────────────────
# Without these variables: bot disabled
TWILIO_SID=ACxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
TWILIO_TOKEN=your_auth_token
TWILIO_WA=whatsapp:+14155238886

# ─── ANTHROPIC (Claude AI) ──────────────────────────────────────
# Without this variable: WhatsApp bot disabled
ANTHROPIC_API_KEY=sk-ant-xxxxxxxxxxxxx
```

> **Automatic simulated mode:** If `CLOVER_MERCHANT_ID` is not defined, the webhook returns `cloverId: "SIM_xxxxx"`. The dashboard works perfectly without external credentials — ideal for the demo.

---

## API Reference

### `POST /api/create-link`

Generates a payment link with intelligent category-based upsell.

**Request:**
```json
{
  "product": "Margherita Pizza",
  "amount": 15.99
}
```

**Response:**
```json
{
  "success": true,
  "link": {
    "id": "fp_1748291234_abc123",
    "product": "Margherita Pizza",
    "amount": 15.99,
    "url": "http://localhost:3000/pay/fp_1748291234_abc123",
    "whatsappUrl": "https://wa.me/?text=...",
    "upsell": "🍹 Add a drink for only +$3.20 — 3× perceived value",
    "status": "active"
  }
}
```

---

### `POST /api/webhook`

Receives Fiserv payments and syncs with Clover POS.

**Request:**
```json
{
  "product": "Margherita Pizza",
  "amount": 15.99,
  "customer": "John Doe",
  "email": "john@demo.com",
  "status": "completed",
  "paymentMethod": "Clover POS"
}
```

**Response:**
```json
{
  "success": true,
  "transactionId": "TXN-001",
  "cloverId": "SIM_1748291234"
}
```

| Behavior | Condition |
|---|---|
| Creates real order in Clover | `CLOVER_MERCHANT_ID` and `CLOVER_API_TOKEN` defined |
| Returns `SIM_xxxxx` | Variables not defined — simulated mode |
| Returns `ERR_xxxxx` | Clover API responded with error |

---

### `GET /api/webhook`

Verifies the system is active.

```json
{ "status": "FlowPay webhook active ✓" }
```

---

### `GET /api/metrics`

Returns current business metrics.

```json
{
  "totalRevenue": 45247.88,
  "totalSales": 2351,
  "activeLinks": 3,
  "uniqueCustomers": 574,
  "revenueChange": 20.1,
  "salesChange": 180.1
}
```

---

### `GET /api/transactions?limit=10&status=completed`

Returns transaction history.

| Parameter | Type | Default | Description |
|---|---|---|---|
| `limit` | number | 10 | Maximum number of records |
| `status` | string | — | Filter by `completed`, `pending`, `failed` |

---

### `POST /api/whatsapp-webhook`

Twilio endpoint. Receives WhatsApp messages and runs the Zero-Click bot.

**Internal flow:**
1. Extracts `Body` and `From` from Twilio form-data
2. Claude AI parses the text into an order JSON
3. Generates payment link and replies to the customer via Twilio API
4. Returns empty TwiML `<Response/>` to prevent duplicate reply from Twilio

---

## Frontend Modifications

The 4 components connected to the backend. **The design is never touched.**

### `create-payment-dialog.tsx`

```
✏ Step A: Add whatsappLink and linkId states
✏ Step B: Replace setTimeout mock with fetch to POST /api/create-link
✏ Step C: Add "Share via WhatsApp" button on the success step
```

### `stats-cards.tsx`

```
✏ Add useMetrics() hook with polling every 3000ms to /api/metrics
✏ Replace hardcoded stats array with dynamic data from the hook
```

### `transactions-table.tsx`

```
✏ Replace static array with useState + polling to /api/transactions
✏ Add cloverId field to the Transaction type
✏ Add "Clover" column to the table with truncated ID in green mono
```

### `activity-feed.tsx`

```
✏ Add polling to /api/transactions mapped to activity format
✏ Show liveActivities when available, fallback to activities
```

---

## Live Demo

> Have these commands ready **before** going on stage. Copy and paste — never type live.

### Verify the system is active

```bash
curl http://localhost:3000/api/webhook
```
```json
{"status":"FlowPay webhook active ✓"}
```

### Simulate Payment 1 — First dashboard update

```bash
curl -X POST http://localhost:3000/api/webhook \
  -H "Content-Type: application/json" \
  -d '{
    "product": "Margherita Pizza",
    "amount": 15.99,
    "customer": "John Doe",
    "email": "john@demo.com",
    "status": "completed",
    "paymentMethod": "Clover POS"
  }'
```

### Simulate Payment 2 — Show real-time polling

```bash
curl -X POST http://localhost:3000/api/webhook \
  -H "Content-Type: application/json" \
  -d '{
    "product": "Americano Coffee",
    "amount": 4.50,
    "customer": "Jane Smith",
    "email": "jane@demo.com",
    "status": "completed",
    "paymentMethod": "WhatsApp"
  }'
```

### View updated metrics

```bash
curl http://localhost:3000/api/metrics
curl http://localhost:3000/api/transactions?limit=5
```

### Expose with ngrok for WhatsApp Bot

```bash
# In a separate terminal:
ngrok http 3000
# Copy the https://xxx.ngrok.io URL
# Paste it as the Twilio Sandbox Webhook URL
```

### Pre-demo verification checklist

- [ ] `pnpm dev` with no console errors
- [ ] `http://localhost:3000/api/metrics` returns valid JSON
- [ ] Stats cards show real data from the endpoint
- [ ] "Create Link" modal generates a real URL with the WhatsApp button
- [ ] Transaction table updates in < 3s after the payment curl
- [ ] `totalRevenue` and `totalSales` metrics increase after simulated payment

---

## Demo Script (3 minutes)

| # | Action | Visual Impact |
|---|---|---|
| **1** | Pitch: *"70% of their sales happen on WhatsApp, their Clover POS has no idea"* | — |
| **2** | Send a chaotic message from the customer's WhatsApp | — |
| **3** | Without touching the keyboard on FlowPay, the order appears on the Dashboard | ✨ Magic |
| **4** | Customer's WhatsApp replies on its own with the payment link | 🤖 AI Bot |
| **5** | Simulate payment on the phone (Fiserv test card) | — |
| **6** | Dashboard switches from Pending → **Paid** without page reload | ⚡ Webhook |
| **7** | Show loyalty reward registered on Fiserv on the customer's screen | 🎁 Loyalty |

---

## Roadmap

### v1.0 — Hackathon Release
- [x] Next.js Dashboard with professional UX
- [x] Integrated backend API Routes (zero external server)
- [x] Clover POS sync (simulated mode + real sandbox)
- [x] AI-powered payment link generation with upsell
- [x] Real-time polling every 3 seconds
- [x] Zero-Click WhatsApp Bot with Claude AI

### v2.0 — Post-Hackathon
- [ ] Fiserv Loyalty Rewards (advanced tier program)
- [ ] OAuth 2.0 for multi-merchant onboarding
- [ ] Audio support (WhatsApp Voice → AI transcription)
- [ ] Analytics panel with persistent history (database)
- [ ] Instagram DMs and Telegram integration
- [ ] Mobile app for the cashier (push notifications for new orders)

---

<div align="center">

**Hackathon Aleph · Track Fiserv/Clover**

*FlowPay Sync 2.0 — Connecting the chaos of WhatsApp with the order of Clover*

</div>
