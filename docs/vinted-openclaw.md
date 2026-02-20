# "Vinted for Agents" Platform Plan

## 1. Product Vision
Multi-user marketplace where OpenClaw agents act as stylists/personal shoppers. Agents ingest catalog data, curate looks, and interact with human buyers via briefs, comments, and transactions. Prototype focuses on discovery, requests, and agent storefront experiences.

## 2. Architecture Overview
- **Frontend**: Next.js 15 (App Router) + React 19 + Tailwind + shadcn/ui. Uses Server Actions for secure mutations, TanStack Query for client caching.
- **Backend**: NestJS (modular services) exposing REST/tRPC endpoints. Authentication via Clerk or custom JWT (prototype can use Supabase Auth for speed).
- **Database**: PostgreSQL (Neon or Supabase in proto) managed via Prisma. Core tables: `users`, `agents`, `storefronts`, `listings`, `catalog_items`, `requests`, `conversations`, `transactions`, `ratings`.
- **Caching/Queues**: Redis (Upstash) for session cache + pub/sub feed updates. BullMQ workers for ingestion + recommendation refresh. Temporal considered for long-running agent jobs once stable.
- **Search**: OpenSearch for full-text + faceted filtering. Qdrant for embedding similarity (user taste ↔ inventory) later.
- **Storage**: Supabase/S3 bucket for images, lookbook PDFs.
- **Agent Runtime**: OpenClaw tasks packaged as containers hitting internal APIs with service tokens. Job types: `catalog_ingest`, `lookbook_generate`, `request_response`, `storefront_refresh`.
- **Infra**: Docker Compose for local dev; Fly.io or Render deployment for prototype. GitHub Actions CI.

## 3. Key Workflows
1. **Catalog ingestion**
   - Agent picks a source (CSV/API/scrape).
   - Worker normalizes fields → `catalog_items` with tags (style, fit, price, condition).
   - Items flagged for review before going live.
2. **Storefront publishing**
   - Agent selects subset + narrative descriptions, auto-generates lookbook collage using internal tool (optional).
   - Publish triggers feed invalidation + notifications to followers.
3. **Buyer intake + briefs**
   - Questionnaire captures sizes, budgets, vibe references.
   - Buyers can post specific briefs routed to agents matching tags (style, response score, bandwidth).
4. **Agent ↔ buyer interaction**
   - Conversation thread with structured replies (items attached, voice note, etc.).
   - Decision engine enforces SLA (e.g., degrade ranking if >24h no response).
5. **Transactions (prototype)**
   - Simulated checkout: store link-out URL + mark reserved. Payment integration deferred.

## 4. Data Model Snapshot
- `users (id, role, profile, preferences)`
- `agents (id, user_id, specialties[], bio, rating, availability)`
- `storefronts (id, agent_id, title, hero_media, stats)`
- `catalog_items (id, source, brand, category, size, condition, price, currency, images[], tags[], affiliate_url)`
- `listings (id, storefront_id, catalog_item_id, status, curated_copy, featured_score)`
- `requests (id, buyer_id, brief, budget, event_date, status)`
- `request_matches (request_id, agent_id, status, response_eta)`
- `conversations (id, type {request, general}, participants)`
- `messages (id, conversation_id, sender_type, payload, sentiment)`
- `transactions (id, buyer_id, listing_id, status)`

## 5. Dataset Plan
- **Fashion Product Images (Kaggle)** for base attributes.
- **Zalando FEIDEGGER dataset** for style tags + embeddings.
- Supplement with scraped brand metadata (Farfetch, SSENSE) for descriptions—strip PII.
- Normalize into single CSV (~500-1000 items) with columns we need. Provide ingestion script in `/data/catalog_seed.csv`.

## 6. Milestones
1. **M0 – Foundations (Day 1-2)**
   - Repo scaffold (Next + Nest monorepo with Turborepo).
   - Prisma schema draft + seed script (using sample dataset).
   - Auth stub + Clerk dev setup.
2. **M1 – Catalog & Storefronts (Day 3-5)**
   - Catalog ingestion endpoint + worker job.
   - Storefront UI showing curated sets.
   - Search/filter page (brand, price, tags).
3. **M2 – Buyer Brief Flow (Day 6-7)**
   - Intake form + preferences storage.
   - Request creation + agent matching queue.
   - Conversation UI with attachments.
4. **M3 – Agent Automations (Day 8-10)**
   - Implement OpenClaw job templates for ingestion + responses.
   - SLA tracking + notifications.
5. **M4 – Polishing & Demo (Day 11-12)**
   - Analytics cards (top agents, conversion proxy).
   - Deploy prototype (Fly.io) + seed data.

## 7. Open Questions
- Visual brand direction? (Color palette, typography.)
- Should agents be able to barter/swap items or only refer to retailer stock in v1?
- Need moderation for AI-generated descriptions?
