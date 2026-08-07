---
name: skill-template
description: A template showing how to write a skill. Copy this folder, rename it to your skill's name, and replace everything below with your skill's actual instructions. The description field (this text) is how Claude decides when to use the skill — describe the situations that should trigger it.
---

# Skill Template

This is a starter skill. It exists to show the format — copy this folder to
create a real skill.

## The two parts of every skill

1. **The frontmatter** (the block between the `---` lines above):
   - `name`: lowercase-with-dashes, matches the folder name.
   - `description`: the most important part. Claude reads this to decide
     *when* to activate the skill, so describe the triggering situations,
     not just what the skill does.

2. **The body** (everything below the frontmatter): the instructions Claude
   follows once the skill activates. Write them like directions to a
   capable colleague — clear steps, examples of good output, and any rules
   that must never be broken.

## Tips that make skills work well

- One skill = one job. Two loosely related jobs make two skills.
- Concrete beats abstract: show an example of the desired output.
- If a step is mandatory, say "always"; if forbidden, say "never".
- Keep it short enough to read in one sitting — link to reference files in
  the same folder if more detail is needed.
