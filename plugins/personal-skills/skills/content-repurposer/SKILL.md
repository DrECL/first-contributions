---
name: content-repurposer
description: Transform existing long-form content (blog posts, presentations, newsletters, research notes) into platform-ready social media posts across LinkedIn, Instagram, Facebook, and X/Twitter. Use when asked to repurpose content for social platforms or when source content exists in Working/Social/Input/.
---

# Skill: Content Repurposer (Social)

## Purpose
Transform existing long-form content (blog posts, presentations, newsletters, research notes) into platform-ready social media posts across LinkedIn, Instagram, Facebook, and X/Twitter.

## When to Use
- When blog posts or other source content exist in `Working/Social/Input/`
- When asked to repurpose existing content for social platforms
- When the Content Planner Social tab has entries with empty status

## Inputs Required
- Source content file(s) from `Working/Social/Input/`
- Brand-context files: `C:\Users\macro\.claude\brand\brand-voice.md`, `C:\Users\macro\.claude\brand\audience-profiles.md`

## Process

### Step 1: Load Context
1. Read `C:\Users\macro\.claude\brand\brand-voice.md` for platform-specific voice adjustments
2. Read `C:\Users\macro\.claude\brand\audience-profiles.md` to understand who we're reaching on each platform
3. Read the source content file(s)

### Step 2: Extract Repurposable Elements
From each source, identify:
- **Key insights** — 3-5 standalone ideas that can each become a post
- **Quotable moments** — Strong statements or frameworks
- **Data points** — Statistics or findings that grab attention
- **Actionable tips** — Practical advice readers can use immediately
- **Story hooks** — Personal anecdotes or case examples
- **Contrarian or fresh angles** — Takes that challenge conventional thinking

### Step 3: Generate Posts by Platform

#### LinkedIn (Primary Platform)
- **Format:** 1,200-1,500 characters (visible before "see more" fold: ~210 characters)
- **Structure:**
  1. Hook (1-2 sentences — must earn the click to "see more")
  2. Context/insight (2-3 short paragraphs)
  3. Key takeaway or lesson
  4. Engagement question or CTA
- **Tone:** Professional but conversational; share insights from teaching/research/practice
- **Hashtags:** 3-5 relevant tags (#MacroSW #MacroSocialWork #SystemsChange #SocialJustice #PolicyPractice)
- **Goal:** Thought leadership + community building
- **Posts per source:** 2-3

#### Instagram (Caption for carousel or static)
- **Format:** 150-300 words for feed posts
- **Structure:**
  1. Bold opening hook
  2. Value-packed body (short paragraphs or line breaks)
  3. CTA: save, share, or comment
- **Tone:** Warm, direct, inspiring
- **Hashtags:** 15-20 in first comment (mix of macro social work + broader social impact tags)
- **Posts per source:** 1-2

#### X/Twitter
- **Format:** Single tweet (280 chars) or thread (3-5 tweets)
- **Structure:**
  - Single: One sharp insight + link or question
  - Thread: Hook tweet → supporting points → CTA tweet
- **Tone:** Concise, punchy, direct
- **Hashtags:** 1-2 max (#MacroSW #SocialWork)
- **Posts per source:** 1-2

#### Facebook
- **Format:** 100-250 words
- **Structure:** Personal tone, community-focused, story-driven
- **Tone:** Conversational, inclusive, community-building
- **Posts per source:** 1

### Step 4: Apply Hope-Based Framing
Every post must:
- Lead with possibility or insight, not doom
- Use strength-based language (see transformation tables in `C:\Users\macro\.claude\brand\hope-based-framework.md`)
- End with reader agency — they should feel empowered, not lectured

### Step 5: Document Each Post
For every generated post, include:
- **Source:** Which content it was repurposed from
- **Platform:** Target platform
- **Format:** Text post / carousel caption / thread / etc.
- **Hook:** The opening line
- **Visual direction:** Brief description of what visual would pair well (for handoff to design or image generation)
- **Suggested posting day/time:** Based on platform best practices

## Output Format
- Markdown file per batch
- Filename: `social-repurposed-[source-slug]-[date].md`
- Save to: `Working/Social/Output/` if the project has that structure; otherwise ask where to save
- Group posts by source, then by platform

## Output Template Per Post
```markdown
### Post [number] — [Platform]
**Source:** [filename]
**Format:** [text post / thread / carousel caption]
**Hook:** [opening line]

[Full post copy here]

**Visual direction:** [brief description]
**Hashtags:** [if applicable]
**Suggested timing:** [day/time recommendation]
```

## Quality Standards
- Each post must stand alone — reader shouldn't need the source to get value
- No copy-paste from source; always rewrite for the platform's native style
- Hook must be strong enough to stop the scroll
- Every post includes an implicit or explicit invitation to engage
- Respect platform character limits and formatting norms
