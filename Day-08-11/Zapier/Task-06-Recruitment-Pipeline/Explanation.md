# How the Automation Works

1. A candidate submits the Apply form.
2. Zapier stores the answers as a new row in the Candidates table.
3. The New Candidate Zap runs:
   - Experience >= 5 → Priority = High
   - Experience 2–4 → Priority = Medium
   - Experience < 2 → Priority = Low
   - Stage is set to Applied
   - Last Updated is set to the current time
4. The same record appears as a card on the Kanban board under Applied.
5. When a recruiter drags the card, Stage changes and the Stage Change Zap runs:
   - Screening → screening email to the candidate
   - Technical Interview → interview scheduling email to the candidate
   - HR Interview → notification email to HR
   - Offer → no email
   - Hired → congratulations email to the candidate
   - Rejected → rejection email to the candidate
   - Applied → no email
6. Last Updated is refreshed when the stage changes.
7. Every day the Follow-Up Zap runs:
   - Find Records looks for candidates not updated for 5 days
   - Hired and Rejected rows are skipped
   - Gmail emails the recruiter with the stale candidate details

## Key Features
- Form + Table + Kanban connected to one Candidates dataset
- Automatic priority scoring from years of experience
- New cards start in Applied
- Separate emails for Technical Interview and HR Interview
- Offer stage stays silent
- Rejection works from any stage
- 5-day recruiter reminder for stale candidates