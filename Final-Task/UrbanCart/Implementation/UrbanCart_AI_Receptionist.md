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
