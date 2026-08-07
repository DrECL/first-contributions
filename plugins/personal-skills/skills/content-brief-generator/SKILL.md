---
name: content-brief-generator
description: Generate structured content briefs that standardize the planning phase for macrominded.com blog posts — keyword strategy, outline, hope-based framing, and CTA strategy. Use when planning new blog content, before running blog-post-writer, or when asked to create a content brief for a topic.
---

# Skill: Content Brief Generator

## Purpose
Generate structured content briefs that standardize the planning phase for blog posts, ensuring consistent quality and SEO strategy before writing begins.

## When to Use
- When planning new blog content for macrominded.com
- When the Content Planner SEO tab has topics that need briefs
- When asked to create a content brief for a given topic
- Before running the Blog Post Writer skill (`blog-post-writer`)

## Inputs Required
- Topic and/or target keyword
- Brand-context files: `C:\Users\macro\.claude\brand\audience-profiles.md`, `C:\Users\macro\.claude\brand\products-services.md`
- Content Planner SEO tab (if available)

## Process

### Step 1: Load Context
1. Read `C:\Users\macro\.claude\brand\audience-profiles.md` for audience pain points and interests
2. Read `C:\Users\macro\.claude\brand\products-services.md` for relevant offerings to reference
3. Read Content Planner SEO tab for topic details, angles, and any notes

### Step 2: Research & Strategy
For each brief, determine:
- **Primary keyword** — The main search term to target
- **Secondary keywords** — 3-5 related terms to weave in naturally
- **Search intent** — Informational, navigational, or transactional
- **Content angle** — What unique perspective does Macro Minded bring?
- **Competitive gap** — What's missing from existing content on this topic?

### Step 3: Build the Brief

#### Brief Sections
1. **Topic & Title Options** — 3 working title options (SEO keyword in first 60 chars)
2. **Target Keyword Strategy**
   - Primary keyword
   - Secondary keywords (3-5)
   - Search intent
   - Estimated difficulty/competition (low/medium/high)
3. **Content Angle** — What makes this Macro Minded's take? How does it differ from what's already out there?
4. **Target Audience Segment** — Which audience profile is this primarily for?
5. **Outline / Structure**
   - Proposed H2 and H3 headings
   - Key points to cover in each section
   - Suggested examples, data points, or stories to include
6. **AI Search Optimization Notes**
   - Definitional paragraph to target (for featured snippets)
   - Questions this post should answer directly
   - Structured data opportunities (lists, tables, how-tos)
7. **Hope-Based Framing Notes**
   - What's the possibility-focused angle?
   - What success stories or positive examples could anchor this?
   - What action steps will we give the reader?
8. **Internal Linking Opportunities** — Existing Macro Minded content to link to
9. **CTA Strategy** — What action do we want the reader to take after reading?
10. **Target Specs**
    - Word count range
    - Target publish date (if scheduled)
    - Visual needs (header image, infographics, embedded media)

## Output Format
- Markdown file
- Filename: `brief-[topic-slug].md`
- Save to: `Working/SEO/Content-briefs/` if the project has that structure; otherwise ask where to save

## Output Template
```markdown
# Content Brief: [Topic Title]

**Date Created:** [YYYY-MM-DD]
**Status:** Ready for writing
**Assigned to:** [if applicable]

---

## Keyword Strategy
- **Primary:** [keyword]
- **Secondary:** [keyword 1], [keyword 2], [keyword 3]
- **Search Intent:** [informational / navigational / transactional]
- **Competition:** [low / medium / high]

## Content Angle
[2-3 sentences on what makes this Macro Minded's unique take]

## Target Audience
[Which audience segment + why this topic matters to them]

## Proposed Outline

### H2: [Section Title]
- Key point 1
- Key point 2
- Example/data to include

### H2: [Section Title]
- Key point 1
- Key point 2
- Example/data to include

[...repeat as needed...]

## AI Search Optimization
- **Featured snippet target:** [question this post should directly answer]
- **People Also Ask targets:** [2-3 related questions to address]
- **Structured content:** [lists, tables, step-by-step sections to include]

## Hope-Based Framing
- **Possibility angle:** [how to frame this topic through hope-based lens]
- **Success example:** [story or case study to anchor the positive framing]
- **Reader action:** [what empowered action can the reader take?]

## Internal Links
- [Existing post title → URL]
- [Existing post title → URL]

## CTA
[What action do we want? Newsletter signup, community join, share, comment, etc.]

## Specs
- **Word count:** [range]
- **Publish target:** [date if known]
- **Visual needs:** [header image, infographic, etc.]
```

## Quality Standards
- Brief should be detailed enough that someone unfamiliar with the topic could write the post
- Keyword strategy should be realistic for Macro Minded's current domain authority
- Outline should follow the hope-based content structure (open with possibility, close with agency)
- Every brief should have a clear "why now" — why is this topic timely for the audience?
