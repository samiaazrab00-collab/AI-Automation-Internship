# Explanation — AI Operations Manager

## What I built
A Zapier Agent named AI Operations Manager.

Every run it:
1. Reads Sales Deals, Ops Tasks, and Support Tickets
2. Finds stuck deals, overdue follow-ups, high-value deals that need attention, overdue tasks, overloaded owners, and unresolved critical tickets
3. Creates only safe follow-up or escalation tasks
4. Writes one row to Daily Operations Reports
5. Emails an internal briefing to the preconfigured human inbox

## Tools
- Knowledge sources: Sales Deals, Ops Tasks, Support Tickets
- Zapier Tables: Find Records on Ops Tasks
- Zapier Tables: Create Record on Ops Tasks or Daily Operations Reports
- Email: send briefing to the locked human inbox only

## Safety
- No Delete Record
- No Update Record on Sales Amount or Stage
- Email recipient is locked to the operator
- Reassignment and customer outreach stay under Needs Human Approval

## Example results
- Case 1 flagged D-104, D-101, D-102, S-01, S-04, and Ahmad's overload
- Case 2 reused T-FUP-003 and T-ESC-003; only added missing T-ESC-004 for S-01
- Case 3 returned High Priority: None after the board was made healthy
- Case 5 flagged D-101 and D-102 but treated D-104 ($22,000, activity today) as current