# Task 07 — AI Customer Support Resolution Agent

## Goal
Build an autonomous CloudFlow support agent that does not only answer questions. It must decide the customer intent, severity, required data, which tool to use, and whether to answer, create a ticket, update a ticket, or escalate.

## Required tables

### Customers
- Customer ID
- Name
- Email
- Plan
- Subscription Status

### Support Tickets
- Ticket ID
- Customer
- Issue
- Category
- Priority
- Action
- Status

## Required tools
- Search Customer
- Search Tickets
- Create Ticket
- Update Ticket
- Send Email

## Required actions
- Password problem → send password reset instructions
- Billing question → search customer / order data
- Duplicate payment → create finance ticket
- Technical issue → create technical support ticket
- Angry / urgent customer → escalate to human

## Extra challenge
Before creating a ticket, check whether an open ticket already exists for the same customer and the same issue. Do not create a duplicate.

## Safety rules
- Do not invent customer data
- Do not delete records
- Do not change financial information
- Do not send a refund or pretend a refund was issued