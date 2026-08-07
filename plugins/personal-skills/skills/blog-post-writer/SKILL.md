---
name: blog-post-writer
description: Generate full blog post drafts for macrominded.com that are SEO-conscious, hope-based, and optimized for AI search visibility. Use when asked to write a blog post, when a content brief exists, or when the Content Planner SEO tab has entries with empty status.
---

# Skill: Blog Post Writer

## Purpose
Generate full blog post drafts for macrominded.com that are SEO-conscious, hope-based, and optimized for AI search visibility.

## When to Use
- When a content brief exists in `Working/SEO/Content-briefs/`
- When the Content Planner SEO tab has entries with empty status
- When asked to write a blog post on a given topic

## Inputs Required
- Content brief (preferred) OR topic + target keyword
- Brand-context files: `C:\Users\macro\.claude\brand\brand-voice.md`, `C:\Users\macro\.claude\brand\hope-based-framework.md`

## Process

### Step 1: Load Context
1. Read `C:\Users\macro\.claude\brand\brand-voice.md` for tone and style
2. Read `C:\Users\macro\.claude\brand\hope-based-framework.md` for framing rules
3. Read the content brief if available (topic, keywords, content angle, structure)

### Step 2: Draft Structure
Build the post with this skeleton:
1. **Title** — SEO keyword in first 60 characters; compelling and possibility-focused
2. **Meta description** — 150-160 characters; includes primary keyword; invites the click
3. **Opening hook** (100-150 words) — Lead with a vision, success story, or forward-looking question. Never open with a problem statistic.
4. **Table of contents** — Linked section headings for posts over 800 words
5. **Body sections** (3-5 sections) — Each with:
   - H2 heading (keyword-conscious)
   - 2-4 paragraphs per section (3-4 sentences each)
   - At least one concrete example, data point, or practitioner story per section
   - Subheadings (H3) for longer sections
6. **Key Takeaways** — 3 bullet points summarizing main insights
7. **Take Action** — Three tiers:
   - Quick Win: something the reader can do today
   - Deep Dive: a longer-term learning or practice shift
   - Community Action: something to do with others
8. **Closing invitation** — Discussion question or reflection prompt

### Step 3: Apply Hope-Based Framework
Before finalizing, run through the quality checklist:
- Opens with possibility, not problem
- Uses strength-based population language throughout
- Frames systems as ready for transformation
- Includes concrete success examples
- Provides actionable steps
- Ends with engagement invitation
- Balances inspiration with evidence

### Step 4: SEO & AI Search Optimization
- Primary keyword appears in: title, first paragraph, one H2, meta description
- Use related keywords naturally throughout (don't stuff)
- Write in clear, direct sentences that AI search engines can extract as answers
- Include a "What is [topic]?" or definitional paragraph early for featured snippet potential
- Use structured data-friendly formatting (lists, tables, clear headings)
- Target 800-1500 words unless brief specifies otherwise

### Step 5: Personal Voice Integration
Where appropriate, include first-person perspective:
- "In my teaching, I've found..."
- "My students often discover..."
- "Through nonprofit work, I've developed..."

These should feel natural, not forced. One or two per post is usually right.

## Output Format
- Markdown file
- Filename: `[slug]-draft.md` (e.g., `building-sustainable-nonprofits-draft.md`)
- Save to: `Working/SEO/Output/` if the project has that structure; otherwise ask where to save
- Include YAML front matter:

```yaml
---
title: "Post Title Here"
slug: post-title-here
keyword: primary keyword
meta_description: "150-160 character description"
status: draft
date_created: YYYY-MM-DD
content_brief: filename-of-brief.md
---
```

## Quality Standards
- No deficit-based language (see hope-based-framework.md transformation tables)
- No jargon without explanation
- Every claim supported by evidence or example
- Readable at a professional-but-accessible level
- Would make a burnt-out macro practitioner feel energized, not heavier
