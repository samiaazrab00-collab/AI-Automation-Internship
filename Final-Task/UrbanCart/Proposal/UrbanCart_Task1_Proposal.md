# UrbanCart — Task 1 Solution Proposal

**Client:** UrbanCart (e-commerce, Pakistan)  
**Prepared for:** Sarah Malik, Operations Manager  
**Discovery date:** 1 September 2026  
**Role:** Solutions & Automation Engineer  

---

## 1. What UrbanCart asked for

From the discovery meeting:

- Customers wait too long for simple answers: price, stock, “where is my order”, returns, shipping, warranty.
- Staff invent answers when they cannot find the file. Sarah does not want the AI to do the same.
- Sales leads are lost in chat. Support complaints are not tracked as tickets.
- Slack is already noisy. Only high-value leads and serious complaints should ping the team.
- Management wants one place to see leads and tickets. The team already uses Notion for process notes.
- Success = 24/7 chat or phone, answers from UrbanCart’s own data, useful leads for sales, basic order lookup, and a human alert when the case is too serious for the agent.

---

## 2. Problems to solve

| Problem | Why it hurts | What “solved” looks like |
|---|---|---|
| No 24/7 receptionist | After-hours buyers drop off | Voice and web hit the same brain |
| Answers are guessed | Wrong price or a fake order status | Catalog and orders come from the database |
| Policies live in files nobody searches | “Can I return after 10 days?” gets a yes | Agent reads the return / shipping / warranty text |
| Leads stay in the transcript | Sales never calls the buyer | Lead row in Airtable + Slack only if Ready and high value |
| Damage cases have no paper trail | Refund fights, no folder | P1 ticket + Slack + Drive folder + Notion incident |
| Slack on every chat | Team ignores real alerts | Quiet Slack by design |

---

## 3. What the agent may do vs what a human must do

**Automated**

- Product price and stock from the catalog
- Order status for a real code such as UC-10452
- Return, shipping, and warranty answers from stored policy text
- Create a lead when the caller shares name, phone, product, budget, and city
- Create a P1 ticket when the parcel is damaged or the customer is angry
- Notify Slack only for those two cases
- Create an evidence folder and a Notion incident for each new P1 ticket

**Stays with a human**

- Approve a refund
- Approve a return after the policy window
- Change a courier promise
- Rewrite policy
- Close or reassign a ticket

If the data is missing, the agent says so. It does not invent a price or a tracking status.

---

## 4. Users and journeys

**Customer (web chat or phone)**  
Asks price, order status, delivery, returns, or warranty. Can leave a buying signal or report damage.

**Sales**  
Works the **Leads** table. Gets Slack `#sales-leads` only when the buyer is Ready and the deal is high value (budget ≥ Rs. 100,000 or iPhone).

**Support**  
Works **Tickets**, view **P1 Open**. Gets Slack `#support-priority` on damage or a serious complaint. Opens the Drive folder and the Notion incident created after the ticket.

**Operations / management**  
Airtable is the live board. Notion holds the incident write-up. Drive holds the evidence pack.

---

## 5. How the customer reaches UrbanCart

One contract for every channel:

```json
{ "text": "customer message", "channel": "web | voice | whatsapp" }
```

- Web chat posts that JSON to the orchestration webhook.
- Vapi is the phone / voice mouth. A tool on the assistant posts the same JSON with `"channel": "voice"`.
- n8n is the only brain. Vapi does not keep a second catalog.

---

## 6. Architecture (and why each tool)

```text
Customer (web / Vapi voice)
        → n8n webhook
              → Supabase / PostgreSQL   products, orders, policy chunks
              → Airtable                Leads, Tickets
              → Slack                   #sales-leads, #support-priority
              → Zapier (on P1 Open)
                    → Google Drive      UrbanCart Evidence / ticket folder
                    → Notion            Incidents
```

| Tool | Role | Why it fits |
|---|---|---|
| **Vapi** | Voice receptionist. Speaks the orchestrated reply. | Sarah asked for phone as well as chat. Vapi is only the mouth. |
| **n8n** | Intent router and orchestrator. Classifies product, order, policy, lead, complaint, or other. Calls the other systems. | One workflow owns the live conversation. |
| **Supabase (PostgreSQL)** | Source of truth for catalog, orders, and policy chunks. | Structured rows for SKU, price, stock, order code, and status. REST access from n8n. |
| **RAG** | Policy questions retrieve a chunk (`return_policy`, `shipping_policy`, `warranty_policy`). The reply is that text plus a short citation. | Stops the model inventing a 10-day return window. |
| **Airtable** | Day-to-day board: Leads for sales, Tickets for support, **P1 Open** view. | The team can filter and assign without opening the database. |
| **Slack** | Interrupt wire only. | Matches “don’t make Slack noisy.” |
| **Zapier** | Office side door: new or updated P1 ticket → Drive folder + Notion page. | Back-office tools, not the live conversation. Slack stays in n8n so the alert is not sent twice. |
| **Google Drive** | Evidence packs for P1 cases under **UrbanCart Evidence**. | A folder the team can drop photos into later. |
| **Notion** | Internal incident record (summary, order code, Airtable URL, Drive folder, status). | Already how the team writes things down. |

n8n owns the conversation. Zapier owns the paper trail after the ticket exists. That is why both tools are in the design.

---

## 7. How each UrbanCart problem is handled

**“How much is the iPhone 15?”**  
Intent `product_inquiry` → `products` where name matches → reply with Rs. price and stock. No Slack.

**“My order UC-10452 hasn’t arrived.”**  
Intent `order_status` → `orders` by `order_code` → real status, courier, last checkpoint. Unknown codes get “I cannot find that order.” No invented tracking.

**“Can I return headphones after 10 days?”**  
Intent `policy_returns` → chunk `return_policy` → policy window only; the agent cannot approve a late return. Same pattern for Lahore delivery (`shipping_policy`) and headphones warranty (`warranty_policy`).

**Ready-to-buy lead**  
Name, phone, product, budget, city, Ready → Airtable Leads + Slack `#sales-leads` when the deal is high value. A later price question does not create another lead or another Slack message.

**“The parcel arrived damaged.”**  
Intent `complaint` → Airtable Tickets P1 / Damage / Open → Slack `#support-priority` → Zapier creates the Drive folder and the Notion Incidents row. The spoken reply does not approve a refund.

**“Who is the CEO?”**  
Intent `other` → short menu of what the agent can do. No guessed company facts.

---

## 8. Guardrails

- Prices are Pakistani Rupees from the catalog, never guessed and never converted to dollars.
- Order status exists only if the code exists.
- Policy text is retrieved, not authored by the model.
- Refunds and late returns are human work.
- Slack has two rooms and two triggers. That is the whole list for v1.

---

## 9. Demonstration scenarios

These are the paths the live system must show:

1. Product inquiry — iPhone / headphones / kettle price and stock, no Slack.
2. Unknown order code — “cannot find,” no invented status.
3. Known order UC-10452 — in-transit sentence from the database.
4. Return after 10 days — grounded refusal from the return policy.
5. Delivery to Lahore — shipping policy, no same-day promise.
6. Headphones warranty — warranty policy.
7. High-value Ready lead — Airtable Leads + `#sales-leads`.
8. Damaged parcel — P1 ticket, `#support-priority`, Drive evidence folder, Notion incident, no refund promise.
9. Voice — the same questions through Vapi, answers from the same webhook.

---

## 10. Recommendation

Build this platform.

It matches what Sarah asked for: coverage after hours, answers from UrbanCart data, a lead list sales can work, a P1 queue support can work, quiet Slack, and a paper trail when a parcel is smashed.

- The mouth is Vapi.
- The brain is n8n.
- The truth for products, orders, and policy is Supabase / PostgreSQL.
- The place people work is Airtable.
- The interrupt wire is Slack.
- The evidence pack is Zapier → Drive + Notion.
