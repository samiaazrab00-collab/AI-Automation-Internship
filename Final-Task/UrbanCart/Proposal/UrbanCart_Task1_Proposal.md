# UrbanCart — Task 1 Solution Proposal

**Client:** UrbanCart (e-commerce, Pakistan)  
**Prepared for:** Sarah Malik, Operations Manager  
**Discovery date:** 1 September 2026  
**Role:** Solutions & Automation Engineer  

---

## 1. What UrbanCart asked for

From the discovery meeting:

- Customers wait too long for simple answers: price, stock, order status, returns, shipping, warranty.
- Staff invent answers when they cannot find the file. The AI must not do the same.
- Sales leads are lost in chat. Support complaints are not tracked as tickets.
- Slack is already noisy. Only important events should ping the team.
- Management wants one place to see leads, tickets and tasks. The team already uses Notion.
- Policy files change. Uploading a new document should update answers without rewriting the agent.
- Customers arrive from the website, WhatsApp, Instagram and phone.
- Success = 24/7 coverage, answers from UrbanCart data, useful leads, basic order lookup, and a human alert when the case is too serious for the agent.

---

## 2. Problems and how they are solved

| Problem | Solution |
|---|---|
| No 24/7 receptionist | One n8n brain behind web, WhatsApp, Instagram and Vapi voice |
| Guessed prices / tracking | PostgreSQL catalog and orders on Supabase |
| Policies nobody searches | Drive folder → ingest → chunk → embedding → vector search |
| Lost leads | Airtable Leads + Slack `#sales-leads` only when Ready and high value |
| Damage has no paper trail | P1 ticket + Task + Slack + Drive evidence + Notion incident |
| Slack on every chat | Four alert types only (below) |
| No customer memory | `customers`, `conversations`, `messages` on Supabase |
| Ops cannot see work | Airtable Tasks created with every P1 ticket |
| Policy updates need a developer | Ingest webhook replaces chunks; chat code stays unchanged |

---

## 3. Automate vs human

**Automated:** price and stock; order status for a real code; policy answers from retrieved chunks; lead capture; P1 ticket + task; Slack on the four events below; Drive evidence folder + Notion incident; conversation logging; knowledge ingest and embeddings.

**Human:** refunds; returns outside the published window; courier promise changes; policy authorship; closing tickets.

If a fact is missing, the agent says support must take over.

---

## 4. Users and channels

- **Customer** — website chat, WhatsApp webhook, Instagram webhook, phone (Vapi). Same JSON contract: `{ "text", "channel" }`.
- **Sales** — Airtable Leads. Slack `#sales-leads` for Ready + high value.
- **Support** — Airtable Tickets (P1 Open). Slack `#support-priority`.
- **Operations** — Airtable Tasks; Slack `#ops-alerts` for late orders; Slack `#workflow-errors` when automation fails.
- **Management** — Airtable boards + Notion Incidents + Drive evidence.

---

## 5. Architecture

```text
Web / WhatsApp / Instagram / Vapi
        → n8n  /webhook/urbancart-chat  (and channel-specific doors)
              → Supabase PostgreSQL     products, orders, customers,
                                        conversations, messages
              → RAG                     embed question → match_chunks
              → Airtable                Leads, Tickets, Tasks
              → Slack                   four rooms only
              → Zapier on P1 Open
                    → Google Drive      UrbanCart Evidence
                    → Notion            Incidents

Drive  UrbanCart Knowledge / Active
        → n8n  /webhook/urbancart-ingest
              → documents + chunks + embeddings
```

| Tool | Role | Why |
|---|---|---|
| **Vapi** | Voice mouth. Tool `urbancart_ask` posts to the chat webhook. | Phone coverage without a second catalog. |
| **n8n** | Conversation brain + knowledge ingest. | One place for intent, lookups, writes and alerts. |
| **Supabase / PostgreSQL** | Products, orders, customers, conversations, messages, documents, chunks, `vector` embeddings. | Structured truth plus pgvector for RAG. |
| **RAG** | Ingest file → chunk → `text-embedding-3-small` → `match_chunks` → reply with citation. | Grounded policy answers; knowledge updates without editing the agent. |
| **Google Drive** | `UrbanCart Knowledge / Active` is the policy source. `UrbanCart Evidence` holds P1 folders. | Files the team already knows how to edit. |
| **Airtable** | Leads, Tickets, Tasks. | Non-technical daily board. |
| **Slack** | Interrupt wire only. | Matches “do not make Slack noisy.” |
| **Zapier** | P1 ticket → Drive folder + Notion incident. | Back office after the ticket exists. |
| **Notion** | Incidents register. | Internal write-up of serious cases. |

---

## 6. RAG and knowledge updates

1. Ops drop or edit a file in Drive `UrbanCart Knowledge / Active` (`return_policy.md`, `shipping_policy.md`, `warranty_policy.md`, `support_guidelines.md`, `product_catalog.md`).
2. Ingest webhook receives `{ filename, content }`.
3. Row in `documents` is upserted. Old `chunks` for that `document_id` are deleted. A new chunk is inserted.
4. The chunk text is embedded (`text-embedding-3-small`, 1536 dimensions) and stored on `chunks.embedding`.
5. A customer policy question is embedded the same way. `match_chunks` returns the nearest rows with a similarity score.
6. The reply is the retrieved text plus a source line. The model does not invent a return window.

Changing a file and calling ingest changes the next chat answer. The chat workflow is not edited.

---

## 7. Slack rules (quiet by design)

| Event | Channel |
|---|---|
| Ready lead and high value (budget ≥ Rs. 100,000 or iPhone) | `#sales-leads` |
| Damage / anger / P1 complaint | `#support-priority` |
| Order still `in_transit` after `promised_by` | `#ops-alerts` |
| HTTP / automation failure | `#workflow-errors` |

Normal price and policy questions do not notify anyone.

---

## 8. Memory and work queue

- Phone number upserts `customers`.
- Each turn writes `conversations` and two `messages` (user + assistant).
- Each P1 ticket also creates an Airtable **Task** (To do, P1, order code, notes) so operations see work, not only tickets.

---

## 9. Demonstration scenarios

1. Product price / stock from `products` — no Slack.  
2. UC-10452 status from `orders`.  
3. Unknown order — cannot find, no invented status.  
4. Return after 10 days — vector hit on `return_policy`, cannot approve.  
5. Lahore delivery — vector hit on `shipping_policy`.  
6. Headphones warranty — `warranty_policy`.  
7. “Approve my refund?” — `support_guidelines`.  
8. Ready lead — Leads + `#sales-leads`.  
9. Damaged parcel — ticket + task + `#support-priority` + Drive + Notion.  
10. Late in-transit order — `#ops-alerts`.  
11. Broken lookup — `#workflow-errors`, customer still gets a response.  
12. Ingest 14-day return file — next chat follows 14 days; restore 7 days.  
13. Same questions on Vapi, WhatsApp webhook and Instagram webhook.

---

## 10. Recommendation

Build this platform.

- The mouth is Vapi.  
- The doors are web, WhatsApp, Instagram and voice.  
- The brain is n8n.  
- The truth for catalog, orders, people and policy vectors is Supabase / PostgreSQL.  
- The files ops edit are on Google Drive.  
- The place people work is Airtable.  
- The interrupt wire is Slack (four rooms).  
- The evidence pack is Zapier → Drive + Notion.
