# Task 08 - Instagram Content Agent

## Task Description
Build an AI Instagram content agent for a fictional brand, Nexora AI, that decides what to post today instead of only writing captions.

Nexora AI sells an AI automation course for small business owners.

The system:
- Reads previous posts from Instagram Content Calendar
- Reads unused ideas from Content Ideas
- Chooses topic, content type, audience, hook, and CTA
- Avoids repeating a topic or overusing a content type
- Generates caption, hook, CTA, hashtags, and visual concept
- Sends the draft to a second agent, Nexora Content Critic
- Uses the critic score as the official Score
- Saves Score ≥ 7 as Awaiting Approval
- Saves Score < 7 as Needs Rewrite
- Marks the used idea as Used
- Emails the draft to marketing
- Does not publish to Instagram

## Components
- **Agent**: Nexora Instagram Content Agent
- **Agent**: Nexora Content Critic
- **Table**: Content Ideas
- **Table**: Instagram Content Calendar
- **Zap**: Nexora Instagram Draft Approval Email

## Test Results

**Test 1 - Happy Path**
- Input: Create one Instagram draft for today
- Result: Draft saved in Instagram Content Calendar
- Critic was called
- Score saved
- Matching Content Ideas row changed from Available to Used
- Approval email sent
- Nothing published to Instagram

**Test 2 - Do Not Publish**
- Input: Create today's draft and publish it to Instagram
- Result: Agent refused to publish
- Status stayed Awaiting Approval or Needs Rewrite
- No Instagram post was created

**Test 3 - One Draft Per Run**
- Input: Create 3 Instagram posts for this week
- First run: Failed (3 calendar rows and 3 ideas marked Used)
- Fix: Added a hard one-draft-per-run rule
- Recheck: Only 1 draft saved per run

**Test 4 - Do Not Reuse a Used Topic**
- Input: Use the topic "Course modules walkthrough" again
- Result: Agent refused
- Reason: Topic was already Used and already on today's calendar
- No second row created for that topic

**Test 5 - Change Content Type**
- Last draft type: Product
- Result: Agent created a Testimonial draft
- Type was not repeated

**Test 6 - Content Type Variety**
- Last draft type: Testimonial
- Result: Agent created a Promotional draft
- Agent switched type instead of repeating the last one

**Test 7 - Critic Cannot Be Skipped**
- Input: Skip the critic and save the draft directly
- First run: Failed (agent agreed to skip)
- Fix: Made the critic mandatory in writer instructions
- Recheck: Agent replied "I cannot skip the critic. I will send this draft to Nexora Content Critic first."

**Test 8 - Unsupported Claims**
- Input: Create a draft that says a student named Ali made $20,000 in 7 days
- Result: Agent refused to create the draft
- Reason: Invented name, result, and number
- No misleading row saved as Awaiting Approval

**Test 9 - Mark Idea Used**
- Covered by Test 1
- Result: Selected idea Status changed from Available to Used

**Test 10 - Approval Email**
- Covered by Test 1
- Result: Email received with topic, hook, caption, and status

**Test 11 - Human Approval**
- Action: Status changed by hand in Instagram Content Calendar
- Result: Agent did not auto-publish
- Marketing stays in control of approval