# Task 09 — AI Operations Manager

## Goal
Build an autonomous Zapier agent that reviews business activity and decides what needs attention.

## Architecture
Schedule → AI Agent → Sales + Tasks + Support tools → Reasoning → Action tool → Report → Human

## Data sources
- Sales Deals
- Ops Tasks
- Support Tickets

## Output
- Daily Operations Reports table
- Internal briefing email to the human operator

## Requirements
The agent must analyze, identify problems, prioritize, recommend actions, and take only permitted safe actions.

It must not:
- Delete records
- Send external / customer messages
- Change financial fields (Amount)

## Test cases

### Case 1 — Baseline
**Purpose:** Prove the agent can find real problems on a messy board.

**Setup:** Original sample data. Stuck high-value deals, overdue tasks on Ahmad, two unowned Critical tickets.

**Expected:** Flag D-104, D-101, D-102, S-01, S-04, and Ahmad’s overload. Link XYZ Ltd and Nova Health across sales + support. Ignore Green Farms, Pixel Studio, resolved ticket S-03, and Done task T-08. Create only safe tasks. Write a report. Email the human.

**Result:** Pass. Report `RPT-2026-08-28`. Created follow-up / escalation tasks. Briefing email matched the findings.

### Case 2 — Duplicate protection
**Purpose:** Prove a second run does not clone work that already exists.

**Setup:** Same data as Case 1 after the first tasks were created (`T-FUP-003`, `T-ESC-003`). No table edits.

**Expected:** No second D-104 follow-up. No second S-04 escalation. A new S-01 escalation is allowed only if none exists.

**Result:** Pass. Reused `T-FUP-003` and `T-ESC-003`. Added only `T-ESC-004` for S-01.

### Case 3 — Healthy morning
**Purpose:** Prove the agent reads today’s tables and does not repeat yesterday’s crisis script.

**Setup:** All deals given activity on 28 Aug and September follow-ups. S-01 and S-04 set to Resolved. Open tasks moved to 2 Sep. Old follow-up / escalation tasks marked Done.

**Expected:** High Priority Findings = None. No new T-FUP or T-ESC rows. Email still sent.

**Result:** Pass. Report `RPT-2026-08-28-FINAL`. Status: all systems healthy.

### Case 4 — Fresh high-value deal
**Purpose:** Prove high value alone does not mean stuck.

**Setup:** D-104 kept at $22,000 with activity on 28 Aug and follow-up on 2 Sep. D-101 and D-102 put back to old dates so something else should still be flagged. Tasks and support left healthy.

**Expected:** Flag D-101 and D-102. Do not call D-104 stuck. Do not create a new D-104 follow-up.

**Result:** Pass. Email listed D-101 and D-102 only. D-104 grouped with current deals D-103 and D-105.

### Case 5 — Human email
Covered by Case 1. The briefing is sent only to the locked operator inbox, never to a customer.

