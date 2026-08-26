# Task 06 — Recruitment Pipeline

## Task Description
Build a recruitment management system for a company hiring developers. Candidates apply through a Zapier form and automatically enter a Kanban pipeline.

The system:
- Collects candidate details through an application form
- Stores each application in a Zapier Table
- Shows every candidate as a card on a Kanban board
- Sets Stage to Applied when an application arrives
- Assigns Priority from years of experience (5+ High, 2–4 Medium, under 2 Low)
- Sends a screening email when a card moves to Screening
- Sends an interview scheduling email when a card moves to Technical Interview
- Notifies HR when a card moves to HR Interview
- Sends a congratulations email when a card moves to Hired
- Sends a rejection email when a card moves to Rejected from any stage
- Does not send an email when a card moves to Offer
- Notifies the recruiter if a candidate has not been updated for 5 days

## Components
- **Form**: Sales / Career Apply page (Candidate Application)
- **Table**: Candidates
- **Kanban**: Recruitment Pipeline
- **Zap**: New Candidate — Set Stage and Priority
- **Zap**: Candidate Stage Change Emails
- **Zap**: Candidate 5-Day Follow-Up

## Test Results

**Test 1 — New Application + High Priority**
- Candidate: Samia
- Position: Frontend Developer
- Experience: 6 years
- Result: Record created in Candidates. Stage = Applied. Priority = High. Card appeared under Applied.

**Test 2 — New Application + Medium Priority**
- Candidate: hajra
- Position: Backend Developer
- Experience: 3 years
- Result: Record created. Stage = Applied. Priority = Medium. Card appeared under Applied.

**Test 3 — New Application + Low Priority**
- Candidate: Ali
- Position: DevOps
- Experience: 1 year
- Result: Record created. Stage = Applied. Priority = Low. Card appeared under Applied.

**Test 4 — Screening Email**
- Input: Moved Samia from Applied → Screening
- Result: Screening email sent to the candidate

**Test 5 — Technical Interview Email**
- Input: Moved Samia from Screening → Technical Interview
- Result: Interview scheduling email sent to the candidate

**Test 6 — HR Interview Notification**
- Input: Moved Samia from Technical Interview → HR Interview
- Result: HR notification sent with candidate name, email, phone, experience, and expected salary

**Test 7 — Offer Stage**
- Input: Moved Samia from HR Interview → Offer
- Result: No automatic email sent (correct)

**Test 8 — Hired Email**
- Input: Moved Samia from Offer → Hired
- Result: Congratulations email sent to the candidate

**Test 9 — Rejection Email**
- Candidate: hajra
- Input: Moved card to Rejected
- Result: Rejection email sent to the candidate

**Test 10 — 5-Day Recruiter Alert**
- Candidate: Ali
- Stage: Applied
- Last Updated: 20/08/2026
- Result: Recruiter received “No update for 5 days: Ali” with position, stage, priority, and last updated date

**Test 11 — Closed Candidates Excluded**
- Hired and Rejected rows with today’s date were not included in the 5-day reminder
- Result: No extra recruiter emails for closed candidates