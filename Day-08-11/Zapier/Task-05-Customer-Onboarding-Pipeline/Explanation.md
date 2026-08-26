# How the Automation Works

1. A client submits the New Client Onboarding Form in Zapier Interfaces.
2. The main Zap is triggered.
3. A new record is created in the Customer Onboarding table.
4. The Stage field is set to New Lead.
5. The record automatically appears as a card on the Onboarding Pipeline Kanban board.
6. A confirmation email is sent to the client.
7. When a card is dragged to another stage, the Stage field in the table is updated.
8. Separate Zaps listen for Updated Record on the Stage field and use Filters so only the correct stage change continues.
9. Each stage change sends the matching email or notification:
   - New Lead → Qualified: Lead qualified email
   - Qualified → Proposal: Create proposal task email
   - Proposal → Won: Deal won email
   - Won → Onboarding: Onboarding email to the client
   - Onboarding → In Progress: Notify project manager
   - In Progress → Completed: Completion email to the client
10. If a record is moved to Qualified, a reminder Zap starts a 3-day delay.
11. After the delay, the Zap finds the same record again.
12. If the Stage is still Qualified, the account manager is notified.

## Key Features
- Form to Table to Kanban pipeline
- Automatic New Lead creation
- Confirmation email on form submission
- Stage-based automation using Updated Record + Filter
- Separate Zap for each pipeline transition
- 3-day follow-up reminder using Delay + Find Record + Filter

