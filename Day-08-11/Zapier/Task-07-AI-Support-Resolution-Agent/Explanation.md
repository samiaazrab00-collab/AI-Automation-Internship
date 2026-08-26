# How the Automation Works

1. A support message is sent to the CloudFlow Support Resolution Agent in Agent Preview.
2. The agent reads the customer email and message.
3. It uses Find Records to search the Customers table by email.
4. It classifies the request:
   - Intent / Category
   - Priority
   - Whether the customer should be answered or escalated
5. It uses Find Records again to search the Support Tickets table for an existing open ticket with the same customer and same issue.
6. The agent decides the action:
   - Password problem → send password reset instructions
   - Billing question → use the customer record and answer from that data
   - Duplicate payment → create a finance ticket
   - Technical issue → create a technical support ticket
   - Angry / urgent customer → escalate to a human
7. If no matching open ticket exists, Create Record adds a new row in Support Tickets.
8. If a matching open ticket exists, Update Record is used instead. No duplicate ticket is created.
9. Send Outbound Email is used when the action requires a customer or team message.

## Key Features
- Autonomous decision-making instead of a fixed Zap path
- Customer lookup from the Customers table
- Intent and priority detection
- Ticket creation in Support Tickets
- Duplicate ticket prevention using Find Records
- Escalation for angry or urgent customers
- Email drafts for password reset and escalation