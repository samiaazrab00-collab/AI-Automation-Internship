# Task 02 – Employee Expense Approval

## Task Description
Build an internal expense management system using Zapier Form + Zapier Table + Automation.

Employees submit expenses through a form. The system automatically:
- Generates a unique Request ID
- Calculates Risk Level (Low / Medium / High)
- Sets Approval Status
- Stores the request in a table
- Sends approval emails based on risk level
- Requests receipt if it is missing

## Components
- **Form**: Employee Expense Portal
- **Table**: Expense Requests
- **Zap**: Employee Expense Approval System

## Test Cases & Results

**Test 1 – Low Risk**
- Amount: 75 | Receipt: Present
- Result: Risk = Low | Status = Approved | No email

**Test 2 – Medium Risk**
- Amount: 250 | Receipt: Present
- Result: Risk = Medium | Status = Pending Approval | Email sent to Manager

**Test 3 – High Risk + Missing Receipt**
- Amount: 750 | Receipt: Missing
- Result: Risk = High | Status = Receipt Required | Email sent requesting receipt