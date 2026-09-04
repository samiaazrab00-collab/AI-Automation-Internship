# UrbanCart AI Receptionist — Task 2

## Goal

Build a live receptionist for UrbanCart that answers product, order, and policy questions from company data, captures sales leads, and escalates damaged-parcel complaints. The agent must not invent prices, stock, order status, delivery times, or warranty terms, and must not approve refunds or late returns.

## Architecture

The customer talks on web or voice. **Vapi** is the voice front door. Every request is sent to one **n8n** webhook (`/webhook/urbancart-chat`) as `{ "text", "channel" }`.

n8n classifies intent and routes:

- **Product / order** → **Supabase** (`products`, `orders`)
- **Returns / shipping / warranty** → **Supabase** knowledge tables (`documents`, `chunks`)
- **High-value lead** → **Airtable Leads** + Slack `#sales-leads`
- **Damage / complaint** → **Airtable Tickets (P1 Open)** + Slack `#support-priority`
- **Unknown questions** → short fallback, no invented facts

**Zapier** watches the Airtable **P1 Open** view. A new or updated P1 ticket creates a folder under Google Drive **UrbanCart Evidence** and a row in Notion **Incidents**. Slack is not sent a second time from Zapier.

## What was configured

- Airtable base **UrbanCart Operations** (Leads, Tickets with **P1 Open** view)
- Supabase catalog, orders, and three policy documents (return, shipping, warranty)
- n8n workflow: intent router, lookups, replies, Airtable writes, Slack only on lead/P1
- Zapier: Airtable → Drive folder → Notion Incidents
- Vapi assistant **UrbanCart Receptionist** with tool `urbancart_ask` pointing at the same webhook

## Results (live tests)

| Scenario | Result |
|---|---|
| iPhone 15 price / stock | Rs. 214,999, in stock (SKU UC-EL-IP15-128) |
| Studio Headphones | Rs. 8,499, in stock |
| Stainless kettle | Rs. 4,250, in stock |
| Order UC-10452 | In transit via TCS, last checkpoint Lahore hub |
| Unknown order UC-00000 | “Cannot find” — no invented status |
| Return headphones after 10 days | 7-day policy, cannot approve |
| Delivery to Lahore | 1–2 business days |
| Headphones warranty | 6 months (policy chunk) |
| “Who is the CEO?” | Fallback only |
| Ready-to-buy lead (voice) | Lead row + `#sales-leads` |
| iPhone price again | No extra Slack / no extra lead |
| Damaged parcel (WhatsApp) | P1 ticket, `#support-priority`, no refund promise, Drive folder + Notion incident |
| Same questions on Vapi | Tool `urbancart_ask` succeeded; spoken answers matched backend data |

## Guardrails

- Answers for catalog, tracking, and policy are read from Supabase, not guessed.
- A return after 7 days and a damage complaint are escalated, not settled by the agent.
- Slack is quiet except high-value Ready leads (budget ≥ 100,000 or iPhone) and P1 tickets.

## Demo path

Voice or webhook → n8n → Supabase → reply. For a smashed parcel: Airtable P1 + Slack, then Zapier creates the evidence folder and the Notion incident.
