# Task 08 — How the system works

## Architecture
Trigger (On Demand)
→ Writer agent
→ Research tools (Zapier Tables)
→ Decision
→ Draft generation
→ Critic agent
→ Decision
→ Save to Instagram Content Calendar
→ Mark Content Ideas as Used
→ Email marketing if Status is Awaiting Approval

The writer never publishes to Instagram.

## Tables
Content Ideas is the idea bank. Status starts as Available.
When a draft is saved, that idea becomes Used so it is not picked again.

Instagram Content Calendar is post history plus the approval queue.
Historical rows are Published.
New agent rows are Awaiting Approval or Needs Rewrite.

## Writer agent
Nexora Instagram Content Agent:

1. Reads recent calendar rows
2. Reads Available ideas
3. Chooses one topic and a content type that is not overused
4. Writes hook, caption, CTA, hashtags, visual concept
5. Must call Nexora Content Critic before saving
6. Uses the critic score as the official Score
7. Creates one calendar row
8. Updates one Content Ideas row to Used

Hard rules:
- One run = one draft
- Critic cannot be skipped
- No invented names, results, or dollar amounts
- Do not reuse a Used topic
- Do not publish

## Critic agent
Nexora Content Critic does not plan the calendar.
It only scores one draft from 1 to 10.

If the score is below 7 it returns rewritten hook, caption, and CTA.
The writer then saves Status as Needs Rewrite.

## Tools
- Find Records: Content Ideas and Instagram Content Calendar
- Create Record: Instagram Content Calendar
- Update Record: Content Ideas, Status = Used only
- Call an agent: Nexora Content Critic
- Default web search is available but not required for the core flow

## Approval email
A Zap watches New Record on Instagram Content Calendar.
If Status is Awaiting Approval, Email by Zapier sends the draft.
Marketing approves or rejects in the table. Nothing auto-posts.

## Tests
| Test | Result |
|---|---|
| Full run saves draft, score, Used idea, email | Pass |
| Refuse Instagram publish | Pass |
| "Create 3 posts" still saves 1 draft after instruction fix | Pass after fix |
| Refuse a Used topic | Pass |
| Switch content type from Product to Testimonial | Pass |
| Switch type again from Testimonial to Promotional | Pass |
| "Skip the critic" is refused | Pass after fix |
| Fake Ali / $20,000 claim is refused | Pass |
| Human can change Status in the table | Pass |

## Known limits
- Agent Preview asks for approval on table writes. Live trigger runs do not.
- Score must be copied as a number into the calendar. A blank Score makes a blank email field.
- The first "create 3 posts" run saved 3 rows before the one-draft rule was tightened.
- Self-score is not used. The critic score is the official score.