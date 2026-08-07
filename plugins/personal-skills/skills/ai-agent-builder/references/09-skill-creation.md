## 9. Skill & Capability Creation

### 9.1 SKILL.md Format Specification

Every Perplexity Computer skill must follow this exact format:

---

name: skill-name-with-hyphens

description: One or two sentences describing when to use this skill. Start with "Use when..." or describe the trigger conditions clearly.

license: MIT

metadata:

  author: your-username

  version: '1.0'

---

# Skill Title

Brief one-paragraph overview of the skill's purpose.

## When to Use

...

## Core Concepts

...

## Step-by-Step Process

...

## Examples

...

## Common Mistakes

...

### 9.2 Validation Pipeline

# Validate skill structure and frontmatter

cd /home/user/workspace && uvx --from skills-ref agentskills validate \<skill-name>

# What the validator checks:

# OK First line is exactly ---

# OK YAML frontmatter present and parseable

# OK Required fields: name, description, license, metadata.author, metadata.version

# OK name matches directory name

# OK version is quoted string ('1.0' not 1.0)

# OK Skill directory exists at workspace/\<skill-name>/SKILL.md

# OK No syntax errors in YAML block

### 9.3 Skill Quality Checklist

Before publishing any skill:

* [ ] First line of SKILL.md is exactly --- (three dashes, no spaces)  
* [ ] All required YAML fields present (name, description, license, metadata.author, metadata.version)  
* [ ] version is quoted: '1.0' not 1.0  
* [ ] Description tells the agent WHEN to load the skill (trigger conditions)  
* [ ] ## When to Use section with clear positive AND negative cases  
* [ ] At least one working code example  
* [ ] Common mistakes / red flags section  
* [ ] Validation passes: uvx --from skills-ref agentskills validate \<name>

### 9.4 Skill Packaging for Distribution

skill-directory/

+-- SKILL.md          \<- Main skill file (required)

+-- README.md         \<- Human-readable docs (optional)

+-- examples/

|   +-- basic.md      \<- Annotated simple example

|   +-- advanced.md   \<- Complex workflow example

+-- templates/

|   +-- prompt_template.md

|   +-- config.yaml

+-- scripts/

    +-- validate.py   \<- Skill-specific validation helpers

### 9.5 Skill Trigger Design

The description field determines when the skill is loaded. Write it to trigger on the right signals:

# Bad — too vague, triggers on everything

description: Helps build things with AI.

# Bad — too narrow, misses many triggers

description: Use when the user types "build an MCP server".

# Good — triggers on intent, not exact phrasing

description: >

  Use when building AI agents, creating MCP servers, designing RAG systems,

  coordinating subagents, optimizing prompts, or architecting any AI-powered

  automation workflow. Covers agent design patterns, multi-agent orchestration,

  and production deployment.

---