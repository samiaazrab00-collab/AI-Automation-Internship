# Task 07 - AI Customer Support Resolution Agent

## Task Description
Build an autonomous AI support agent for a fictional software company called CloudFlow. The agent must not only answer questions. It must decide what action to take.

The agent:
- Receives a customer issue such as “I was charged twice for my subscription”
- Identifies the customer by email from the Customers table
- Detects intent and priority
- Searches Support Tickets before creating a new record
- Chooses the correct action instead of following one fixed path
- Creates or updates a ticket in Zapier Tables
- Can send an email when the action requires it
- Escalates angry or urgent customers to a human
- Does not invent customer data
- Does not create a duplicate ticket for the same open issue

Possible actions:
- Password problem → send password reset instructions
- Billing question → search customer / order data
- Duplicate payment → create finance ticket
- Technical issue → create technical support ticket
- Angry / urgent customer → escalate to human

## Components
- **Agent**: CloudFlow Support Resolution Agent
- **Table**: Customers
- **Table**: Support Tickets
- **Tools**:
  - Zapier Tables: Find Records
  - Zapier Tables: Create Record
  - Zapier Tables: Update Record
  - Email by Zapier: Send Outbound Email

## Test Results

**Test 1 - Password Problem**
- Customer: Ali Raza
- Email: ali@example.com
- Message: I forgot my password and cannot log in. Please reset it.
- Result: Ticket TCK-20260826-001 created
- Category: Password
- Action: Sent password reset instructions
- Status: Resolved

**Test 2 - Billing Question**
- Customer: Sara Malik
- Email: sara@example.com
- Message: What plan am I on and what is my subscription status?
- Result: Agent searched the Customers table and found Starter / Past Due
- Ticket: TCK-20260826-002
- Category: Billing
- Action: Answered from customer record
- Status: Resolved

**Test 3 - Duplicate Payment**
- Customer: Ahmad Khan
- Email: ahmad@example.com
- Message: I was charged twice for my subscription.
- Result: Ticket TCK-20260826-003 created
- Category: Billing
- Priority: High
- Action: Created finance ticket
- Status: Open

**Test 4 - Technical Issue**
- Customer: Ali Raza
- Email: ali@example.com
- Message: The dashboard will not load and I keep getting an error.
- Result: New ticket TCK-20260826-004 created
- Category: Technical
- Action: Created technical support ticket
- Status: Open

**Test 5 - Angry / Urgent Customer**
- Customer: Sara Malik
- Email: sara@example.com
- Message: I want to talk to a human right now. I am very angry.
- Result: New ticket TCK-20260826-005 created
- Category: Escalation
- Action: Escalated to human
- Status: Escalated

**Test 6 - Duplicate Ticket Prevention**
- Customer: Ahmad Khan
- Message: I was charged twice for my subscription.
- Attempt: Sent the same issue again
- Result: Agent did not create a second finance ticket. The existing open ticket was reused.