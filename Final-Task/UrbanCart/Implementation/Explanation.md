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

## Test cases

Chat URL: `/webhook/urbancart-chat`  
Ingest URL: `/webhook/urbancart-ingest`  
WhatsApp / Instagram URLs: `/webhook/urbancart-whatsapp`, `/webhook/urbancart-instagram`

| ID | Input | Channel / door | Expect | Slack |
|---|---|---|---|---|
| A1 | How much is the iPhone 15 and is it in stock? | web | Rs. 214,999, SKU UC-EL-IP15-128, in stock | none |
| A2 | Do you have Studio Headphones? | web | Rs. 8,499, SKU UC-AU-HD-01, in stock | none |
| A3 | How much is the kettle? | web | Rs. 4,250, SKU UC-HM-KT-17, in stock | none |
| B1 | My order UC-10452 hasn't arrived yet. | web | in_transit, TCS, Lahore hub, promised 2026-08-30 | `#ops-alerts` (overdue) |
| B2 | What's the status of order UC-00000? | web | Cannot find that order. No invented status | none |
| C1 | Can I return headphones after 10 days? | web | 7-day window, cannot approve. Vector hit `return_policy` (similarity ~0.67) | none |
| C2 | Do you deliver to Lahore and how long does delivery take? | web | 1–2 business days. Vector hit `shipping_policy` (similarity ~0.64) | none |
| C3 | Does the Studio Headphones product have a warranty? | web | Audio: 6 months. `warranty_policy` | none |
| C4 | Can you approve my refund or do I need a human? | web | Must not approve refunds. `support_guidelines` | none |
| D1 | Hello, who is the CEO? | web | Fallback only. No invented company facts | none |
| E1 | My name is Hassan Malik, phone 03001122334. I am interested in the iPhone 15, budget Rs. 200,000, I am in Lahore and ready to buy. | voice | Airtable Lead Ready + customer upsert `+923001122334` | `#sales-leads` |
| E2 | How much is the iPhone 15? | web | Price only. No extra lead | none |
| F1 | The parcel for order UC-10452 arrived damaged. The box was smashed. I want a refund. | whatsapp | No refund promise. Ticket P1 Open + Task “P1 follow-up UC-10452” + Drive folder + Notion incident | `#support-priority` |
| G1 | Same product / order / return / damage questions spoken on Vapi | voice | Tool `urbancart_ask` succeeds. Spoken reply matches n8n `reply`. Prices stay in Rs. | same rules as web |
| H1 | Ingest `return_policy.md` with a 14-day window, then ask C1 on **chat** | ingest then chat | Chat follows 14 days | none |
| H2 | Ingest `return_policy.md` with the 7-day text, then ask C1 again | ingest then chat | Chat returns to 7 days | none |
| I1 | Temporarily break Lookup product URL (`productsx`) | web | Webhook still returns a body | `#workflow-errors` |
| J1 | Damaged parcel JSON posted to `/webhook/urbancart-whatsapp` (no `channel` field) | WhatsApp door | `channel=whatsapp`, same P1 path as F1 | `#support-priority` |
| J2 | iPhone price posted to `/webhook/urbancart-instagram` | Instagram door | `channel=instagram`, price from catalog | none |
| K1 | Hassan + iPhone on chat | web | New `conversations` row + two `messages` (user, assistant) | none unless E1 rules apply |

H1 is a demo only. After H1, always run H2 so production policy stays 7 days.
