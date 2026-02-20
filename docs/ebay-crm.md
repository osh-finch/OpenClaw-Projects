# eBay CRM Prototype Plan

## 1. Product Goals
- Handle low-to-mid volume eBay sellers today, but scale to SME inventory.
- Central hub for customers, orders, and conversations with retention focus.
- Detect intent (future item requests), sentiment, and unreplied messages.
- Run batch/triggered outreach via Email + WhatsApp.

## 2. Architecture Overview
- **Frontend**: Next.js 15 + Tailwind + shadcn UI. Emphasis on inbox-style views (Kanban + timeline).
- **Backend**: NestJS service exposing REST/tRPC endpoints. Background workers using BullMQ for sync + automations.
- **Database**: PostgreSQL via Prisma. Core tables: `customers`, `orders`, `items`, `messages`, `tickets`, `campaigns`, `tasks`, `sentiments`, `intents`.
- **Messaging Integrations**:
  - Email via Resend (prototype) with fallback to SMTP.
  - WhatsApp via Meta Cloud API (webhook receiver + outbound orchestrator).
- **LLM Services**: OpenAI GPT-4o-mini or local LLM (when available) for sentiment/intent extraction. Queue results to avoid latency.
- **Scheduler**: Temporal or BullMQ recurring jobs for eBay sync, follow-up reminders, SLA alerts.
- **Auth**: Clerk or Supabase Auth. Role-based access (owner, support agent).

## 3. Key Workflows
1. **Data Sync**
   - OAuth with eBay Sell API (Orders, Post-Order, Messaging).
   - Scheduled pulls store raw payloads + normalized records.
   - Webhooks (if available) to reduce polling; fallback to 15m cron.
2. **Inbox**
   - Column views: `Needs Reply`, `Awaiting Stock`, `Follow-up Scheduled`, `Resolved`.
   - Threads show latest sentiment, related orders, promises made.
3. **Intent/Sentiment Detection**
   - Worker takes new messages → LLM prompt extracts:
     - Sentiment (positive/neutral/negative).
     - Intent tags (e.g., `REQUEST_FUTURE_ITEM: lantern`).
   - Store tags in `customer_interests` table for future campaigns.
4. **Retention Automation**
   - Segment builder (filters: spent >£X, interest tag, sentiment threshold, last purchase date).
   - Template composer with merge fields (item name, newest stock).
   - Queue sends via email/WhatsApp; log outcomes + replies.
5. **Reminders & SLA**
   - Job checks for unreplied conversations > X hours.
   - Pushes to `tasks` table + notifies via email/WhatsApp digest.
6. **Analytics**
   - Dashboard metrics: CSAT proxy (avg sentiment), repeat buyer rate, response time, campaign performance.

## 4. Data Model Snapshot
- `customers (id, name, ebay_user, contact, opt_ins)`
- `items (id, sku, title, category, style_tags[], base_price)`
- `orders (id, customer_id, item_id, order_ref, status, value, shipped_at)`
- `messages (id, customer_id, order_id?, channel {ebay,email,whatsapp}, direction, body, sentiment_id, intent_id, replied_by, replied_at)`
- `intents (id, customer_id, category, keyword, urgency, expires_at)`
- `campaigns (id, title, channel, filters_json, template_id, status)`
- `campaign_recipients (campaign_id, customer_id, status, last_sent_at)`
- `tasks (id, customer_id, type {follow_up, pending_reply}, due_at, status)`
- `sentiments (id, label, score, message_id)`

## 5. Dataset / Mock Plan
- Craft realistic seed data in `/data/ebay_sample/`:
  - `customers.csv`, `orders.csv`, `messages.jsonl` with lantern/collectible themes.
  - Mark some customers with future-interest statements for testing.
- Provide script to load into Postgres for dev.

## 6. Milestones
1. **M0 – Scaffold (Day 1)**
   - Repo structure (shared or separate from marketplace?).
   - Prisma schema + seed data load.
   - Basic auth + layout.
2. **M1 – Data Views (Day 2-4)**
   - Customer list, order list, message inbox.
   - Linkages between entities + timeline drawer.
3. **M2 – Sentiment & Intent (Day 4-5)**
   - Worker pipeline + UI badges.
   - Interest tags visible on customer cards.
4. **M3 – Retention Engine (Day 6-7)**
   - Segment builder + campaign composer.
   - Email/WhatsApp send stubs (logging only initially).
5. **M4 – Tasks & Alerts (Day 8-9)**
   - SLA detection, task queue, notifications.
6. **M5 – Messaging Integrations (Day 10-12)**
   - Wire actual email + WhatsApp providers.
   - Add manual reply composer referencing templates.

## 7. Compliance Notes
- Need explicit buyer opt-in before contacting off eBay. Store consent + provide unsubscribe handling.
- Encrypt PII at rest (PG crypto extensions or app-level AES).
- Limit eBay data retention per policy (~18 months unless justified).

## 8. Next Steps
- Confirm shared codebase vs separate repo.
- Decide on hosting target for prototype (Render/Fly vs your server).
- Once design approved, move to wireframes + sprint kickoff.
