---
name: ad-creative-brief-generator
description: Generate structured creative briefs for ad campaigns across social platforms — audience targeting, messaging strategy, visual direction, and copy variations for testing. Use when asked to create ad campaign briefs, before generating ad creative, or when the Content Planner Ads tab has campaigns with empty status.
---

# Skill: Ad Creative Brief Generator

## Purpose
Generate structured creative briefs for ad campaigns across social platforms — defining audience targeting, messaging strategy, visual direction, and copy variations for testing.

## When to Use
- When the Content Planner Ads tab has campaigns with empty status
- When asked to create ad campaign briefs
- Before generating ad creative visuals or copy

## Inputs Required
- Content Planner Ads tab (campaign name, product/offer, goals, platform)
- Ad style library from `Working/Ads/Style-library/` (if available)
- Reference images from `Working/Ads/Input/` (if available)
- Brand-context files: `C:\Users\macro\.claude\brand\brand-voice.md`, `C:\Users\macro\.claude\brand\products-services.md`, `C:\Users\macro\.claude\brand\audience-profiles.md`

## Process

### Step 1: Load Context
1. Read `C:\Users\macro\.claude\brand\products-services.md` for the product/service being promoted
2. Read `C:\Users\macro\.claude\brand\audience-profiles.md` for targeting
3. Read `C:\Users\macro\.claude\brand\brand-voice.md` for messaging tone
4. Review ad style library in `Working/Ads/Style-library/` for creative direction (if available)
5. Check `Working/Ads/Input/` for reference images (if available)

### Step 2: Define Campaign Strategy
For each campaign, determine:
- **Campaign objective** — Awareness, engagement, traffic, leads, conversions
- **Target audience segment** — Which profile(s) from audience-profiles.md
- **Key message** — The single most important thing to communicate
- **Value proposition** — Why should the audience care right now?
- **Offer/CTA** — What specific action are we asking them to take?

### Step 3: Build the Creative Brief

#### Brief Sections
1. **Campaign Overview**
   - Campaign name
   - Objective
   - Platform(s): LinkedIn, Instagram, Facebook, etc.
   - Ad format(s): Static image, carousel, video, story
   - Budget tier: Low / Medium / High (affects variation count)
2. **Target Audience**
   - Primary segment (from audience-profiles.md)
   - Targeting parameters (interests, job titles, behaviors)
   - Pain point being addressed
   - Desired emotional response
3. **Key Message & Hooks**
   - Core message (1 sentence)
   - 3-5 hook variations to test:
     - Question hook
     - Statistic/data hook
     - Story/scenario hook
     - Bold statement hook
     - Value proposition hook
4. **Creative Direction**
   - Visual style (reference style library examples)
   - Color mood and palette notes
   - Image/graphic direction (product shots, lifestyle, abstract, illustrated)
   - Typography guidance (headline weight, body style)
   - Reference images to incorporate (from `Working/Ads/Input/`)
   - Logo placement and brand element notes
5. **Copy Variations** (5 per campaign by default)
   For each variation:
   - Headline (5-10 words)
   - Body copy (25-50 words)
   - CTA button text
   - Visual pairing notes
6. **Platform-Specific Requirements**
   - LinkedIn: Professional framing, thought leadership angle
   - Instagram: Visual-first, aspirational, swipeable
   - Facebook: Community-focused, conversational, shareable
7. **Testing Plan**
   - What variables to test (hook, visual, CTA, audience)
   - Minimum 2-3 variations per test
   - Success metrics for each objective

### Step 4: Apply Hope-Based Framing
Even ads should follow the framework:
- Lead with aspiration or possibility, not fear or pain
- Frame the offer as empowerment, not rescue
- Use strength-based language for the audience
- The reader should feel capable and inspired, not inadequate

### Step 5: Quality Check
- [ ] Campaign objective is clear and measurable
- [ ] Target audience is specific and actionable
- [ ] Key message is concise (one sentence)
- [ ] Hook variations are genuinely different approaches (not just rewording)
- [ ] Creative direction is specific enough for design execution
- [ ] Copy variations test meaningful differences
- [ ] Hope-based framing applied (no fear-based or deficit marketing)
- [ ] Platform requirements addressed

## Output Format
- Markdown file per campaign
- Filename: `ad-brief-[campaign-slug].md`
- Save to: `Working/Ads/Output/` if the project has that structure; otherwise ask where to save

## Output Template
```markdown
# Ad Creative Brief: [Campaign Name]
**Date Created:** [YYYY-MM-DD]
**Status:** Ready for creative production
**Product/Offer:** [what's being promoted]

---

## Campaign Overview
- **Objective:** [awareness / engagement / traffic / leads / conversions]
- **Platform(s):** [LinkedIn, Instagram, Facebook]
- **Ad Format(s):** [static, carousel, story, video]
- **Variations:** [number of creative variations to produce]

## Target Audience
- **Segment:** [from audience profiles]
- **Targeting:** [job titles, interests, behaviors]
- **Pain point:** [what problem does this solve]
- **Desired response:** [what should they feel/think/do]

## Key Message
[One sentence — the core thing to communicate]

## Hook Variations
1. **Question:** [hook text]
2. **Data/Stat:** [hook text]
3. **Story:** [hook text]
4. **Bold statement:** [hook text]
5. **Value prop:** [hook text]

## Creative Direction
- **Visual style:** [reference to style library]
- **Color mood:** [warm/cool/bold/muted + specific notes]
- **Image direction:** [type of imagery]
- **Typography:** [headline treatment, body style]
- **Reference images:** [files from Working/Ads/Input/]
- **Brand elements:** [logo placement, tagline inclusion]

## Copy Variations

### Variation 1
- **Headline:** [text]
- **Body:** [text]
- **CTA:** [button text]
- **Visual pairing:** [notes]

### Variation 2
[...repeat for each variation...]

## Platform Notes
**LinkedIn:** [specific adjustments]
**Instagram:** [specific adjustments]
**Facebook:** [specific adjustments]

## Testing Plan
- **Variables to test:** [what we're learning]
- **Success metrics:** [what we're measuring]
- **Minimum run:** [duration or impressions before evaluating]
```

## Quality Standards
- Briefs should be actionable — a designer or copywriter could execute without further clarification
- Hook variations must test genuinely different psychological angles, not just word swaps
- Visual direction should be specific but leave room for creative interpretation
- All copy must pass the hope-based communication quality checklist
- No fear-based marketing, manufactured urgency, or deficit framing
