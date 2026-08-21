# Task 01 – Lead Intake System

## Task Description
Build a lead capture system using Zapier Form + Zapier Table + Automation.

When a lead submits the form:
- A unique Lead ID is generated
- Lead Score and Priority (Hot / Warm / Cold) are calculated
- The complete lead is stored in the Leads table
- If the lead is Hot, an email notification is sent to the sales team

## Components
- **Form**: Sales Lead Intake
- **Table**: Leads
- **Zap**: Sales Lead Intake - Form to Table + Hot Lead Alert

## Test Input
- Full Name: Ahmad
- Email: ahmad@test.com
- Phone: 123456
- Company: ABC Company
- Budget: 8000
- Lead Source: LinkedIn
- Urgency: High

## Test Output
- Lead ID: LEAD-20260821-XXXXXX
- Lead Score: 75
- Priority: Hot
- Status: New
- Email notification sent