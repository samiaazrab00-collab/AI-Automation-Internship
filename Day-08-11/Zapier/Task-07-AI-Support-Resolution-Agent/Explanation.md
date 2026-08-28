# Explanation — CloudFlow Support Resolution Agent

## What I built
A published Zapier Agent named **CloudFlow Support Resolution Agent**.

The agent receives a customer message, searches the Customers table, searches existing tickets, chooses an action, writes to Support Tickets, and can draft an email.

This is not a linear Zap. The agent decides which tool to use at runtime.

## Architecture
On Demand trigger
→ Zapier Agent
→ Find Records in Customers or Support Tickets
→ Create Record or Update Record in Support Tickets
→ Send Outbound Email when needed

## Tables

### Customers
Sample records used for testing:
- CUST-001 · Ahmad Khan · ahmad@example.com · Professional · Active
- CUST-002 · Sara Malik · sara@example.com · Starter · Past Due
- CUST-003 · Ali Raza · ali@example.com · Business · Active

### Support Tickets
The agent creates rows with:
- Ticket ID format: TCK-YYYYMMDD-00X
- Category: Billing, Technical, Account, Password, Escalation
- Priority: Critical, High, Medium, Low
- Status: Open, In Progress, Escalated, Resolved

## Agent tools
- Zapier Tables · Find Records  
  Table ID limited to Customers and Support Tickets
- Zapier Tables · Create Record → Support Tickets
- Zapier Tables · Update Record → Support Tickets
- Email by Zapier · Send Outbound Email

Find Records is added once. The agent is allowed to choose between the two tables.

## Decision rules
1. Identify the customer by email
2. Classify intent and priority
3. Search Support Tickets for an existing open ticket on the same issue
4. If a matching open ticket exists, update it
5. If not, create a new ticket
6. Send email only when the action requires it

Category rules:
- Asks for a human / angry / urgent → Escalation
- Forgot password / reset login → Password
- Charged twice / duplicate payment → Billing + finance ticket
- Plan / status / billing question → Billing + search customer record
- App error / dashboard broken and no request for a human → Technical

## Test results

| Ticket ID | Customer | Input | Category | Action | Status |
|---|---|---|---|---|---|
| TCK-20260826-001 | Ali Raza | Forgot password | Password | Sent password reset instructions | Resolved |
| TCK-20260826-002 | Sara Malik | What plan am I on? | Billing | Answered from customer record | Resolved |
| TCK-20260826-003 | Ahmad Khan | Charged twice | Billing | Created finance ticket | Open |
| TCK-20260826-004 | Ali Raza | Dashboard error | Technical | Created technical support ticket | Open |
| TCK-20260826-005 | Sara Malik | Want a human, angry | Escalation | Escalated to human | Escalated |

## Extra challenge
The same duplicate-payment message was run twice for Ahmad. The agent did not create a second finance ticket. It reused the existing open ticket.

Sara and Ali each have two tickets because the second message was a different issue.

## What this proves
The agent can:
- detect intent
- look up customer data
- choose the correct action
- write structured tickets
- escalate angry customers
- avoid duplicate tickets for the same open issue