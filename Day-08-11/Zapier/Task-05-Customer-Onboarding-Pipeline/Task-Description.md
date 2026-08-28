# Task 05 – Customer Onboarding Pipeline

Built a CRM-style onboarding system in Zapier.

- Clients submit an Interface form
- A record is created in Zapier Tables with Stage = New Lead
- The record appears on a Kanban board
- A confirmation email is sent
- Moving cards between stages triggers emails/notifications
- If a lead stays in Qualified for more than 3 days, the account manager is notified

**Flow:** Form → Table → Kanban → Filters → Delay → Email

## Form
Interface page with these fields:

- Client Name
- Company
- Email
- Service
- Project Budget
- Start Date
- Account Manager
- Requirements

## Table
Table name: Customer Onboarding

Columns include the form fields plus Stage. Stage values:

New Lead → Qualified → Proposal → Won → Onboarding → In Progress → Completed

## Kanban
Kanban component in the Interface, connected to the same table, grouped by Stage.

- Card title: Client Name
- Card description: Company

## Zaps
All related Zaps are turned on:

| Zap name | What it does |
|---|---|
| Form → Create New Lead in Onboarding Pipeline | Creates the table record, sets Stage to New Lead, sends confirmation email |
| Stage Change: New Lead → Qualified | Sends qualified email |
| Stage Change: Qualified → Proposal | Sends create-proposal email |
| Stage Change: Proposal → Won | Sends deal-won email |
| Stage Change: Won → Onboarding | Sends onboarding email to the client |
| Stage Change: Onboarding → In Progress | Notifies the project manager |
| Stage Change: In Progress → Completed | Sends completion email to the client |
| Reminder: Qualified for more than 3 days | Waits 3 days, then notifies the account manager if Stage is still Qualified |

