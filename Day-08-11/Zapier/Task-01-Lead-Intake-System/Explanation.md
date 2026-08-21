# How the Automation Works

1. User submits the Sales Lead Intake form.
2. Code by Zapier calculates the Lead Score based on:
   - Urgency
   - Budget
   - Lead Source
3. Another Code step generates a unique Lead ID using timestamp.
4. All data is saved as a new record in the Leads table with Status = New.
5. Paths checks if Priority is "Hot".
6. If Priority is Hot, an email is sent to the sales team with the lead details.

## Scoring Rules
- High Urgency → +30
- Medium Urgency → +20
- Low Urgency → +10
- Budget > 5000 → +30
- Budget 1000–5000 → +20
- Budget < 1000 → +10
- Referral → +20
- LinkedIn → +15

## Priority Rules
- 70+ → Hot
- 40–69 → Warm
- Below 40 → Cold