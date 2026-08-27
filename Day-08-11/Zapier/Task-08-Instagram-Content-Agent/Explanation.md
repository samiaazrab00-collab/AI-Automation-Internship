# How the Automation Works

1. The writer agent, Nexora Instagram Content Agent, starts on demand.
2. It searches Instagram Content Calendar to see recent topics and content types.
3. It searches Content Ideas for rows with Status = Available.
4. It chooses one topic, content type, audience, hook, and CTA.
   - It does not reuse a Used topic.
   - It avoids repeating the last content type.
5. It writes the Instagram caption, hashtags, and visual concept.
6. It must call Nexora Content Critic before saving.
   - The critic scores hook, repetition, CTA, audience fit, and unsupported claims.
   - Score 7 or higher → Status = Awaiting Approval
   - Score below 7 → Status = Needs Rewrite
7. The writer creates one record in Instagram Content Calendar.
   - Official Score comes from the critic only.
   - One run creates one draft only.
8. The writer finds the matching Content Ideas row by Topic and sets Status = Used.
9. The Zap is triggered when a new calendar record is created:
   - Filter continues only if Status = Awaiting Approval
   - Email by Zapier sends the draft to marketing
10. Marketing approves or rejects the row in the table.
    - The agent does not publish to Instagram.

## Key Features
- Agent decides the post instead of only writing a caption
- History check from Instagram Content Calendar
- Idea bank in Content Ideas with Available / Used status
- Second-agent quality check before the draft is saved
- One draft per run
- No auto-publish
- Human approval email when Status is Awaiting Approval