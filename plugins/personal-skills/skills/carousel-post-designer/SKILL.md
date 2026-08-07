---
name: carousel-post-designer
description: Generate full carousel post sets — slide copy, visual direction, and platform-ready captions — for LinkedIn, Instagram, and Facebook. Use when asked to create carousel posts, when a content brief specifies carousel format, or when the Content Planner Carousel tab has entries with empty status.
---

# Skill: Carousel Post Designer

## Purpose
Generate full carousel post sets — slide copy, visual direction, and platform-ready captions — for LinkedIn, Instagram, and Facebook.

## When to Use
- When the Content Planner Carousel tab has entries with empty status
- When asked to create carousel posts on given topics
- When a content brief specifies carousel format

## Inputs Required
- Content Planner carousel tab OR topic brief
- Style library examples from `Working/Carousel/Style-library/` (if available)
- Brand-context files: `C:\Users\macro\.claude\brand\brand-voice.md`, `C:\Users\macro\.claude\brand\products-services.md`

## Process

### Step 1: Load Context
1. Read `C:\Users\macro\.claude\brand\brand-voice.md` for voice and carousel-specific guidance
2. Read style library in `Working/Carousel/Style-library/` for visual inspiration and style guide (if available)
3. Read the Content Planner or topic brief for requirements

### Step 2: Plan the Carousel
For each carousel set, determine:
- **Topic/angle** — What single idea does this carousel teach or communicate?
- **Platform** — LinkedIn (landscape/square), Instagram (square/portrait), or both
- **Slide count** — 5-10 slides (sweet spot: 7-8)
- **Content type** — Educational, listicle, story-driven, myth-busting, how-to, framework

### Step 3: Write Slide Copy

#### Slide Structure
1. **Slide 1 (Cover/Hook):** Bold statement, question, or promise that stops the scroll. Keep to 5-10 words max. This is the most important slide.
2. **Slides 2-[n-1] (Body):** One idea per slide. Short headline + 1-3 supporting sentences or bullet points. Use the hope-based framework language.
3. **Final Slide (CTA):** Clear call to action — save, share, follow, comment, visit link. Include brand name "Macro Minded."

#### Copy Rules
- One core idea per slide — if it needs a second thought, it needs a second slide
- Headlines: 3-8 words, bold and direct
- Body text: 15-40 words per slide maximum
- Use active voice and possibility-focused language
- No slide should require the previous slide to make sense (people swipe at different speeds)

### Step 4: Visual Direction
For each slide, provide:
- **Layout suggestion:** Text-heavy, visual-heavy, split, quote-style, data/chart
- **Color mood:** Reference brand palette or campaign theme
- **Image direction:** What type of image/graphic would complement the text (abstract, photo, icon-based, illustrated)
- **Typography notes:** Which text should be largest/boldest

Important: Use the style library as *inspiration and guidance*, not a rigid template. Vary the execution so each carousel feels fresh while staying on-brand.

### Step 5: Write the Caption
For each carousel, write a platform-appropriate caption:
- **LinkedIn:** 150-300 words. Hook → context → why this matters → CTA + hashtags
- **Instagram:** 100-200 words. Hook → value summary → CTA (save this!) + hashtags in first comment
- **Facebook:** 80-150 words. Conversational, community-focused framing

### Step 6: Apply Quality Check
- [ ] Cover slide hook would stop a scroll
- [ ] One idea per slide maintained throughout
- [ ] Hope-based language throughout (no deficit framing)
- [ ] CTA slide is clear and specific
- [ ] Caption enhances but doesn't repeat the slides
- [ ] Visual directions are specific enough for design execution

## Output Format
- Markdown file per carousel set
- Filename: `carousel-[topic-slug]-[platform].md`
- Save to: `Working/Carousel/Output/` if the project has that structure; otherwise ask where to save

## Output Template
```markdown
# Carousel: [Topic Title]
**Platform:** [LinkedIn / Instagram / Both]
**Slides:** [count]
**Campaign/Theme:** [if applicable]

## Slide Copy

### Slide 1 — Cover
**Headline:** [hook text]
**Visual direction:** [layout, mood, imagery notes]

### Slide 2
**Headline:** [text]
**Body:** [supporting text]
**Visual direction:** [notes]

[...repeat for each slide...]

### Slide [n] — CTA
**Headline:** [CTA text]
**Body:** [supporting text]
**Visual direction:** [notes]

## Caption
[Platform-ready caption with hashtags]

## Visual Notes
**Style reference:** [which style library example(s) to draw from]
**Brand elements:** [logo placement, color palette notes]
**Image assets needed:** [any specific photos or graphics to source]
```

## Quality Standards
- Cover slide must be compelling enough to earn the swipe
- Educational value should be deliverable even without reading the caption
- Visual directions should be actionable for a designer or image generator
- Each carousel should be shareable — would someone save this or send it to a colleague?
