# UrbanCart AI Receptionist — Implementation note

## What runs

Customers reach one n8n brain through:

- `POST /webhook/urbancart-chat` (web)
- `POST /webhook/urbancart-whatsapp`
- `POST /webhook/urbancart-instagram`
- Vapi assistant **UrbanCart Receptionist** → tool `urbancart_ask` → chat webhook, `channel: voice`

Body: `{ "text": "...", "channel": "web|voice|whatsapp|instagram" }`.

A second workflow, **UrbanCart Knowledge Ingest**, listens on `/webhook/urbancart-ingest` and updates policy chunks when a Drive file changes.

## Data

**Supabase / PostgreSQL**

- `products`, `orders` — catalog and tracking
- `documents`, `chunks` — policy text plus `embedding vector(1536)`
- `match_chunks(query_embedding, match_count)` — vector retrieval
- `customers`, `conversations`, `messages` — memory

**Airtable UrbanCart Operations**

- Leads, Tickets (P1 Open), Tasks

**Google Drive**

- `UrbanCart Knowledge / Active` — return, shipping, warranty, support, catalog files
- `UrbanCart Evidence` — P1 folders (Zapier)

**Notion** — Incidents  
**Slack** — `#sales-leads`, `#support-priority`, `#ops-alerts`, `#workflow-errors`

## Flows

1. Intent router classifies product, order, policy, lead, complaint, other.
2. Product / order rows come from SQL. Policy questions are embedded and sent to `match_chunks`. The reply is the retrieved text plus a source line.
3. A phone number upserts `customers`. Every turn writes a conversation and two messages.
4. Ready + high-value lead → Airtable Leads + `#sales-leads`.
5. Damaged parcel → Tickets P1 + Tasks To do + `#support-priority`. Zapier adds a Drive folder and a Notion incident. The agent does not approve a refund.
6. Order still in transit after `promised_by` → `#ops-alerts`.
7. Failed HTTP node → `#workflow-errors`; the webhook still responds.
8. Ingest `{ filename, content }` replaces that document’s chunk and stores a new embedding. The next chat answer follows the new text.

## Guardrails

- Prices are Rs. from `products`.
- Unknown order codes are not invented.
- Late returns and refunds stay with a human.
- Slack has four triggers only.

## Demo path

Voice or a channel webhook → n8n → Supabase (SQL or vectors) → reply.  
P1 complaint → Airtable + Slack → Zapier → Drive + Notion.  
Edit a policy file → ingest → next question uses the new chunk.

## Test cases (passed)

These are the cases the live system already passed.

Chat URL: `/webhook/urbancart-chat`  
Ingest URL: `/webhook/urbancart-ingest`  
WhatsApp / Instagram URLs: `/webhook/urbancart-whatsapp`, `/webhook/urbancart-instagram`

| ID | Input | Door | Result |
|---|---|---|---|
| A1 | How much is the iPhone 15 and is it in stock? | web | Rs. 214,999, SKU UC-EL-IP15-128, in stock. No Slack. |
| A2 | Do you have Studio Headphones? | web | Rs. 8,499, SKU UC-AU-HD-01, in stock. No Slack. |
| A3 | How much is the kettle? | web | Rs. 4,250, SKU UC-HM-KT-17, in stock. No Slack. |
| B1 | My order UC-10452 hasn't arrived yet. | web | in_transit, TCS, Lahore hub, promised 2026-08-30. Slack `#ops-alerts`. |
| B2 | What's the status of order UC-00000? | web | “Cannot find that order.” No invented status. No Slack. |
| C1 | Can I return headphones after 10 days? | web | 7-day window, cannot approve. Vector hit `return_policy` (similarity ~0.67). |
| C2 | Do you deliver to Lahore and how long does delivery take? | web | 1–2 business days. Vector hit `shipping_policy` (similarity ~0.64). |
| C3 | Does the Studio Headphones product have a warranty? | web | Audio 6 months from `warranty_policy`. |
| C4 | Can you approve my refund or do I need a human? | web | Must not approve refunds. `support_guidelines`. |
| D1 | Hello, who is the CEO? | web | Fallback only. No invented company facts. No Slack. |
| E1 | Hassan Malik, 03001122334, iPhone 15, budget Rs. 200,000, Lahore, ready to buy | voice | Airtable Lead Ready. Customer `+923001122334`. Slack `#sales-leads`. |
| E2 | How much is the iPhone 15? | web | Price only. No extra lead. No Slack. |
| F1 | Parcel UC-10452 arrived damaged. Box smashed. Want a refund. | whatsapp | No refund promise. Ticket P1 Open + Task “P1 follow-up UC-10452” + Drive folder + Notion incident. Slack `#support-priority`. |
| G1 | Product / order / return / damage on Vapi | voice | Tool `urbancart_ask` succeeded. Spoken reply matched n8n. Prices stayed in Rs. |
| H1 | Ingest `return_policy.md` with 14 days, then ask C1 on chat | ingest → chat | Chat followed the 14-day text. |
| H2 | Ingest 7-day return text, then ask C1 again | ingest → chat | Chat returned to 7 days. |
| I1 | Lookup product URL broken (`productsx`) | web | Webhook still responded. Slack `#workflow-errors`. |
| J1 | Damaged-parcel JSON to `/webhook/urbancart-whatsapp` | WhatsApp door | `channel=whatsapp`. Same P1 path as F1. |
| J2 | iPhone price to `/webhook/urbancart-instagram` | Instagram door | `channel=instagram`. Catalog price. No Slack. |
| K1 | Hassan + iPhone on chat | web | New `conversations` row and two `messages` (user, assistant). |

H1 was a knowledge-update demo. H2 restored the official 7-day policy afterward.
