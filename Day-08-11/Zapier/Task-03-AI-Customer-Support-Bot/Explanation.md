# How the Automation Works

1. User interacts with the CloudFlow Support Bot.
2. If the user needs support, the bot asks for:
   - Full Name
   - Email
   - Issue description (in one message)
3. User clicks the “Create Support Ticket” button.
4. Code by Zapier generates a unique Ticket ID.
5. Another Code step extracts Name, Email, Issue and detects Priority based on keywords.
6. A new record is created in the Support Tickets table with Status = New.

## Priority Rules
- “completely down” / “system down” → Critical
- Login / password / access issues → High
- General questions / features → Low
- Everything else → Medium