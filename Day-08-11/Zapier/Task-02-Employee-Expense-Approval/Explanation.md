# How the Automation Works

1. Employee submits the Expense form.
2. Code by Zapier generates a unique Request ID (EXP-YYYYMMDD-HHMMSS).
3. Another Code step calculates:
   - Risk Level based on Amount
   - Approval Status based on Risk + Receipt availability
4. All data is stored in the Expense Requests table.
5. Paths decide what email to send:
   - Medium Risk → Approval request to Manager
   - High Risk → Approval request to Manager
   - Missing Receipt → Email to employee requesting receipt
   - Low Risk → Automatically approved (no email)

## Risk Rules
- Amount < 100 → Low
- Amount 100–500 → Medium
- Amount > 500 → High

## Approval Status Rules
- No Receipt → Receipt Required
- Low Risk + Receipt → Approved
- Medium or High Risk + Receipt → Pending Approval