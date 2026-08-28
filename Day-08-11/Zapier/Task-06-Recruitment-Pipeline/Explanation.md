# Task 06 — Explanation

## How the system works

The pipeline has three layers:

1. Form collects the application.
2. Table stores the candidate as one record.
3. Kanban shows that record as a card grouped by Stage.

Zaps run in the background. Recruiters only use the form and the board.

```text
Form submit
    → Candidates row created
    → Zap sets Stage = Applied and Priority
    → Card appears in Applied

Recruiter drags card
    → Stage field updates
    → Zap sends the matching email
    → Last Updated is refreshed

Every morning
    → Zap finds rows not updated for 5 days
    → Recruiter gets a reminder
    → Hired and Rejected are skipped