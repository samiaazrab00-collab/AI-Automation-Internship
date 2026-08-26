# Task 05 - Customer Onboarding Pipeline

## Task Description
Build a CRM-style customer onboarding system in Zapier.

When a new customer submits an onboarding form, their record should automatically appear on a Kanban board and move through a sales/onboarding pipeline with automated emails and reminders.

The system:
- Collects client details through a Zapier Interface form
- Stores the record in a Zapier Table
- Creates a Kanban card in the New Lead stage
- Sends a confirmation email after form submission
- Triggers different actions when a card moves between stages
- Notifies the account manager if a card stays in Qualified for more than 3 days

## Components
- **Interface Form**: New Client Onboarding Form
- **Table**: Customer Onboarding
- **Kanban**: Onboarding Pipeline
- **Zaps**:
  - Form → Create New Lead in Onboarding Pipeline
  - Stage Change: New Lead → Qualified
  - Stage Change: Qualified → Proposal
  - Stage Change: Proposal → Won
  - Stage Change: Won → Onboarding
  - Stage Change: Onboarding → In Progress
  - Stage Change: In Progress → Completed
  - Reminder: Qualified for more than 3 days

## Form Fields
- Client Name
- Company
- Email
- Service
- Project Budget
- Start Date
- Account Manager
- Requirements

## Kanban Stages
New Lead → Qualified → Proposal → Won → Onboarding → In Progress → Completed

## Test Results

**Test 1 – Form Submission**
- Client Name: samia
- Company: sw services
- Service: AI automation
- Result: Record created in the table with Stage = New Lead
- Confirmation email sent successfully

**Test 2 – New Lead → Qualified**
- Card moved from New Lead to Qualified
- Result: “Lead Qualified – samia” email sent
- Only the Qualified Zap ran

**Test 3 – Qualified → Proposal**
- Card moved from Qualified to Proposal
- Result: “Action Required: Create Proposal for samia” email sent
- Project Budget and Requirements included in the email

**Test 4 – Later Stage Moves**
- Card moved through Won, Onboarding, In Progress, and Completed
- Result: Each stage sent the correct email and other stage Zaps did not run

**Test 5 – Duplicate Trigger Fix**
- Problem: Two emails were sent at the same time when Stage changed
- Fix: Added Filter steps so each Zap only continues when Stage exactly matches the target stage
- Result: Only one correct email is sent per move

**Test 6 – Qualified Reminder**
- Delay temporarily set to 1 minute for testing
- Card moved into Qualified
- Result: After the delay, the reminder Zap ran successfully
- Delay later changed back to 3 days