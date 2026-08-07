---
name: collaborative-prompting
description: Collaborative prompt refinement workflow that helps users develop "golden prompts" through iterative dialogue. Trigger when user says "let's collaborate!" or when they want to work together to perfect a prompt, explore complex ideas without predetermined endpoints, or determine the best AI tool/approach for a task. Use for brainstorming sessions, complex projects, blog content development, research questions, or any exploratory work where assumptions would derail the process.
---

# Collaborative Prompting

## Overview

This skill transforms initial ideas into refined, executable prompts through structured collaboration. Instead of making assumptions and immediately executing, work with the user to clarify intent, identify ambiguities, and co-develop the strongest possible approach. The output is a "golden prompt" template the user can reuse with any AI tool.

**Core Principle**: Ask, don't assume. If anything is vague, ambiguous, or missing, question it before proceeding.

## Workflow

### Step 1: Understand Intent

Begin by asking about the user's intent and desired outcome. This prevents wasted effort on assumptions.

**Essential questions to ask:**
- "What's the purpose of this? Who's the audience?"
- "What kind of output are you looking for? A report? An app? Ideas? A list? Something else?"
- "How will you use the result?"
- "Is this for your Macro Minded blog, course materials, research, or something else?"

**Example opening:**
> "Before I dive in, let me make sure I understand what you're after. What's the purpose of this, and what kind of output would be most useful to you?"

### Step 2: Identify and Clarify Ambiguities

Scan the user's request for anything vague, open to interpretation, or potentially missing. Ask about these items rather than filling in blanks.

**What counts as ambiguous:**
- Undefined scope ("analyze this" - what aspects? how deep?)
- Vague terms ("macro social workers" - which specific audience segments?)
- Missing context (references to "recent" events without timeframes)
- Unspecified constraints (word count, format, depth of analysis)
- Assumed inclusions (concepts that seem relevant but weren't mentioned)

**Guidelines for questioning:**
- **If a concept seems missing**: Ask whether it should be included, don't add it automatically
  - "I notice you didn't mention [X]. Should that be part of this, or is there a reason it's excluded?"
- **If scope is unclear**: Ask for boundaries
  - "Should this focus on [narrow scope] or include [broader scope]?"
- **If terms are vague**: Ask for definitions
  - "When you say 'macro social workers,' are you thinking of your specific audience (educators, community organizers, nonprofit leaders) or a broader definition?"

**Adaptive questioning style:**
- **Quick clarifications (2-3 at once)**: Yes/no questions, simple choices, or brief factual clarifications
  - Example: "Quick questions: (1) US-focused only, or international? (2) Is 25 a hard number or flexible? (3) Should I include policy orgs or strictly news sites?"
  
- **Complex questions (one at a time)**: Questions requiring thoughtful explanation or that might spawn follow-up questions
  - Example: "What's your main goal with this - is it to monitor these sites yourself, use them as sources for blog posts, or something else? This affects how I structure everything."

**Avoid**: The "5-step wall of text" where you provide lengthy instructions all at once that will generate questions before the user can complete them.

### Step 3: Determine the Best Tool/Approach

Not every task is best suited for Claude. Part of collaboration is honestly assessing whether another tool might serve the user better.

**Consider:**
- **Claude**: Complex analysis, writing, structured thinking, file creation, coding
- **Gemini Deep Research**: Comprehensive research projects requiring synthesis of many sources
- **Perplexity**: Current events, recent developments, real-time information
- **Web search**: Fact-checking, finding specific sources, current data
- **Other approaches**: Sometimes the answer isn't AI at all (manual research, expert consultation, etc.)

**Ask directly:**
> "For this task, I could handle it, but Gemini's Deep Research might actually be better suited for [reason]. Which would you prefer?"

Or:
> "This seems like it might benefit from [tool/approach] because [reason]. Does that sound right, or would you rather we proceed differently?"

### Step 4: Iterate on the Prompt

Work back-and-forth to refine the prompt until it's complete, specific, and assumption-free.

**Elements of a "golden prompt":**
- Clear purpose and intended use
- Specific scope and boundaries
- Defined output format and structure
- Any necessary constraints (length, depth, style)
- Context needed for accurate execution
- No ambiguous terms
- Explicit rather than implied requirements

**Check for readiness:**
> "I think we've clarified everything. Here's what I understand: [summarize]. Does this capture what you need, or should we refine anything else?"

### Step 5: Deliver the Golden Prompt

Present the refined prompt as a reusable template. Format it clearly so the user can copy and use it with any AI tool.

**Format:**
```
**Golden Prompt Template: [Task Name]**

[The refined, complete prompt with all clarifications incorporated]

**Context to provide:**
- [Any files, data, or background needed]

**Expected output:**
- [Clear description of deliverable]

**Best tool for this:** [Claude / Gemini / Perplexity / Other, with brief reasoning]
```

**Optional execution:**
If Claude is the identified best tool and the user wants execution now, offer:
> "Would you like me to execute this now, or would you prefer to save this template for later?"

## Anti-Patterns to Avoid

**DON'T do these things:**
- ❌ Make assumptions about scope, audience, or purpose
- ❌ Add concepts or requirements the user didn't mention without asking first
- ❌ Produce a full response before clarifying ambiguities
- ❌ Assume you know what the user means by vague terms
- ❌ Present 5+ paragraphs of steps all at once
- ❌ Batch complex questions that will require back-and-forth

**DO these things instead:**
- ✅ Ask about intent before proceeding
- ✅ Question anything that's unclear or open to interpretation
- ✅ Adapt your questioning style to the complexity
- ✅ Acknowledge when another tool might be better
- ✅ Work iteratively in natural back-and-forth flow
- ✅ Build the user's prompt engineering skills through the process

## Example: The News Sites Research

**Bad approach (what Gemini did):**
User: "Using the macro top 5 file and other resources at your disposal, investigate specific news sites used by macro social workers and return to me a list of the top 25 sites that provide news for macro social workers. These sites should have an active URL and the information should be updated on their site at least once a month if not more. In your list provide me justification for why you picked each of the sites, the active URL, the name of the news organization. Make sure these are news stories current events. Do you have any questions before you get started?"

Gemini: "Nope!" → [Immediately produces 20+ page comprehensive analysis with assumptions]

**Good approach (collaborative):**
Claude: "Before I dive into this research, let me make sure I understand what you're after:

1. **Purpose**: What will you use this list for? Your Macro Blog? Teaching materials? Personal monitoring? This affects how I structure everything.

2. **Depth**: Do you want a simple list with brief justifications, or the kind of deep strategic analysis with frameworks?

3. **Audience scope**: By 'macro social workers,' are you thinking of your specific audience (educators, community organizers, nonprofit leaders), or a broader definition?

Let me start with those, then we can refine from there."

The collaborative approach prevents wasted work and produces exactly what the user needs.
