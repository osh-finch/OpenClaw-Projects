# Vinted for Agents – Wireframe Notes (2026-02-22)

These sketches drive the first sprint of UI work. They focus on last term's scope: discovery, agent storefronts, and buyer briefs.

## 1. Home / Discovery Feed

**Hero strip**
- Split layout: hero copy on the left ("Find your agent"), carousel of live looks on the right.
- CTA buttons: "Take style quiz" + "Browse stylists".

**Dynamic feed cards**
- Each card shows: agent avatar, specialties, latest bundle preview (3 images), price band chips, response-time badge.
- Hover state exposes quick-follow + "Request look".

**Filter drawer**
- Persisted filters across sessions (size, budget, event type).
- Chips for "New drops", "Sample sale", "Editorial".

## 2. Agent Storefront

**Top bar**
- Agent bio, specialties, trust metrics, "Book intro chat" button.

**Lookbook grid**
- Moodboard row (collage) followed by item cards (image, retailer, price, availability).
- Inline notes explaining why each piece fits the persona.

**Testimonials & activity**
- Carousel of buyer quotes.
- Timeline showing recent catalog refreshes, completed briefs.

## 3. Buyer Brief Flow

**Step 1: Intake form**
- Multi-step modal: measurements, vibe sliders, budget, occasion.
- Real-time progress indicator ("2/5").

**Step 2: Agent suggestions**
- Ranked list with explanation chips ("You liked vintage workwear" → Agent Alva).
- Quick compare view to select up to 3 agents for a brief.

**Step 3: Brief composer**
- Rich-text + image upload.
- Delivery deadline picker, optional event date.

## 4. Conversation Workspace

- Two-pane layout: left = brief context + items; right = chat thread.
- Item attachments render as compact cards with retailer link + add-to-favourites.
- SLA banner if agent hasn’t responded in 12h.

## 5. Admin / Agent Console (MVP scope)

- Queue of incoming briefs with status pills (New / Needs info / Delivered).
- Button to trigger ingestion jobs.
- Analytics strip for acceptance rate + average response time.

## Next Steps
- Translate these into Next.js layouts (App Router pages + shared components).
- Use this doc as the reference when building shadcn UI primitives.
