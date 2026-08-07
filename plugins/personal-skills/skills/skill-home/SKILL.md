---
name: skill-home
description: Knowledge of Dr. Erick's skill home — the durable GitHub repo where all his Claude skills live. Load whenever a session creates or improves a skill, when Dr. Erick mentions saving skills, losing skills, skill backup, connecting a project to his skills, or asks how his skill system works. Dr. Erick should never need to remember paths, commands, or settings — this skill carries them so any Claude can act on one plain-language request.
---

# Dr. Erick's Skill Home

The skill home is the GitHub repository **DrECL/first-contributions**, in
`plugins/personal-skills/skills/` — one folder per skill. It exists because
cloud sessions are temporary: anything not pushed there can be lost when a
session ends. Full details live in that repo's `plugins/README.md`.

Dr. Erick is not a programmer and should never be asked to run git
commands, edit JSON, or navigate settings. When he asks for something in
plain language, do the technical steps for him and report back in plain
language.

## The three requests this skill answers

**"Save this skill" / end of any session that made or improved a skill:**
Copy the skill folder into `plugins/personal-skills/skills/` in the repo,
commit, and push to main. Never leave a new skill only in a temporary
session. The repo is public — before pushing, check the skill for
passwords, keys, client names, or private details, and hold it back
(telling him why) if any are found.

**"Connect this project to my skills":**
Add to the project's `.claude/settings.json`:

```json
{
  "extraKnownMarketplaces": {
    "claude-skills": {
      "source": { "source": "github", "repo": "DrECL/first-contributions" }
    }
  },
  "enabledPlugins": { "personal-skills@claude-skills": true }
}
```

On his own computer the equivalent is:
`/plugin marketplace add DrECL/first-contributions` then
`/plugin install personal-skills`. To pick up newly pushed skills there:
`/plugin marketplace update claude-skills`.

**"Did it save?" / any claim that skills were pushed:**
Verify against the repo itself (fetch main, list the skills folder) and
report what is actually there. Plans are not results; confirm before
celebrating.

## Rules

- Never delete or overwrite an existing skill in the repo without showing
  him what would be lost and getting a yes.
- Nothing secret ever goes in (the repo is public).
- When in doubt, read `plugins/README.md` in the repo — it is the
  authoritative guide.
