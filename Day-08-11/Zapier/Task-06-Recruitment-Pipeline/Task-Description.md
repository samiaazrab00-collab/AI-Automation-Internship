# Task 06 — Recruitment Pipeline

## Objective

Build a recruitment management system in Zapier for a company hiring developers.

Candidates apply through a form. Each application is stored in a Zapier Table and appears on a Kanban board. Automation assigns priority from years of experience, sends emails when a card changes stage, and alerts the recruiter if a candidate is not updated for 5 days.

## Tools used

- Zapier Forms (Interface)
- Zapier Tables
- Zapier Kanban
- Zaps (Paths, Schedule, Gmail / Email by Zapier)

## What was built

### 1. Application form

Page: Apply

Fields collected from the candidate:

- Candidate Name
- Email
- Phone
- Position
- Experience (years)
- Expected Salary
- Resume link
- Portfolio link
- Availability

Internal fields are not shown on the form:

- Stage
- Priority
- Last Updated
- Recruiter Email

### 2. Candidates table

Table name: Candidates

Columns:

- Candidate Name
- Email
- Phone
- Position
- Experience
- Expected Salary
- Resume link
- Portfolio link
- Availability
- Stage
- Priority
- Last Updated
- Recruiter Email

Stage options:

Applied → Screening → Technical Interview → HR Interview → Offer → Hired → Rejected

Default stage for a new application: Applied

### 3. Kanban board

Page: Pipeline

- Data source: Candidates table
- Group by: Stage
- Card title: Candidate Name
- Card detail: Position

Recruiters move cards between columns. Moving a card updates Stage and triggers email automation.

### 4. Automation 1 — New application

Trigger: New record in Candidates

The Zap updates the same record (it does not create a second row):

| Experience | Priority |
|---|---|
| 5+ years | High |
| 2–4 years | Medium |
| Less than 2 years | Low |

It also sets:

- Stage = Applied
- Last Updated = current time

### 5. Automation 2 — Stage emails

Trigger: Candidates record updated (Stage changed)

| Stage | Action |
|---|---|
| Screening | Email candidate — screening next steps |
| Technical Interview | Email candidate — interview scheduling |
| HR Interview | Email HR — candidate details |
| Offer | No email (recruiter handles the offer) |
| Hired | Email candidate — congratulations |
| Rejected | Email candidate — rejection |
| Applied | No email |

### 6. Automation 3 — 5-day follow-up

Trigger: Schedule by Zapier — every day

The Zap searches Candidates for records where:

- Last Updated is more than 5 days ago
- Stage is not Hired
- Stage is not Rejected

Then it emails the recruiter with the candidate name, position, stage, priority, and last updated date.

## Test evidence

| Candidate | Position | Result |
|---|---|---|
| Samia | Frontend Developer | Moved Applied → Screening → Technical Interview → HR Interview → Offer → Hired. Matching emails sent. |
| hajra | Backend Developer | Rejection email sent. |
| Ali | DevOps | Left in Applied with Last Updated 20/08/26. Recruiter received “No update for 5 days” email. |

## Extra challenge

Completed: if a candidate has no update for 5 days, notify the recruiter.