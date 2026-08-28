# How the Pipeline Works

The form, table, and Kanban use the same client record.

When the form is submitted, the first Zap creates a row in Customer Onboarding and writes Stage as New Lead. The confirmation email is sent in that same Zap. The Kanban is grouped by Stage, so the new row shows up in the New Lead column.

Moving a card is a Stage update in the table. Each stage Zap listens to that update. A Filter after the trigger keeps the Zap from running unless Stage exactly matches its own stage. That Filter was required because one card move was starting two Zaps and sending two emails.

The reminder Zap also starts from a Stage update to Qualified. It does not watch cards that are already sitting in the column. After the trigger, it waits 3 days, finds the same record, and checks Stage again. If the card has moved on, no email is sent. If it is still Qualified, the account manager is notified.

The reminder was tested with a 1-minute delay, then the delay was set back to 3 days.

## Test Results

| Test | Input | Action | Expected result | Actual result |
|---|---|---|---|---|
| 1 | Client Name: samia, Company: sw services, Service: AI automation | Submit form | Record created with Stage = New Lead, confirmation email sent | Passed |
| 2 | Card for samia | Drag New Lead → Qualified | Only Qualified email sent | Passed. Subject: Lead Qualified – samia |
| 3 | Same card | Drag Qualified → Proposal | Only Proposal email sent | Passed. Subject: Action Required: Create Proposal for samia. Budget 10000, Requirements None |
| 4 | Same card | Drag through Won, Onboarding, In Progress, Completed | Matching email for each stage | Passed |
| 5 | Same Stage update | Move card without Filters | Two emails arrived together | Failed at first |
| 6 | Same Stage update after adding Filters | Move card again | Only the matching Zap runs | Passed |
| 7 | Card already sitting in Qualified | Wait for reminder Zap | Zap does not start | Confirmed. Trigger needs a Stage update |
| 8 | Card moved Qualified → New Lead → Qualified. Delay set to 1 minute | Wait 1 minute | Reminder Zap runs | Passed |
| 9 | Reminder Zap | Change delay back to 3 days | Delay is 3 days | Done |