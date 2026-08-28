# Task 08 — Instagram Content Agent

## Goal
Build an AI Instagram content agent for a fictional brand, Nexora AI.
The agent must decide what to post today. It must not only write captions.

Nexora AI sells an AI automation course for small business owners.

## Required workflow
Every run:

1. Analyze existing content
   - Topics already posted
   - Content types being overused
   - Topics not covered recently
2. Independently choose topic, hook, content type, CTA, and audience
3. Generate caption, hook, CTA, hashtags, and visual concept
4. Send the draft to a second AI critic
   - Hook strength
   - Repetition
   - CTA clarity
   - Audience relevance
   - Unsupported claims
   - Score < 7 → rewrite / Needs Rewrite
   - Score ≥ 7 → Awaiting Approval
5. Store the draft in Instagram Content Calendar
6. Human approval
   - Do not publish to Instagram
   - Status = Awaiting Approval
   - Email the draft to marketing

## Extra behavior
If recent posts already used a content type, switch to another type.
Do not reuse a Used idea.
One run creates one draft only.

## Tables
### Content Ideas
Topic, Product, Target Audience, Content Type, Goal, Status

### Instagram Content Calendar
Date, Topic, Caption, Hook, CTA, Hashtags, Visual Concept, Score, Status, Notes

## Agents
- Nexora Instagram Content Agent (writer)
- Nexora Content Critic (second evaluation)

## Zap
New record in Instagram Content Calendar
→ Filter Status = Awaiting Approval
→ Email marketing the draft