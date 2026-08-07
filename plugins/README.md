# Dr. Erick's Shared Skill Home

This folder is the **one durable place** where my Claude skills live.
Cloud sessions run in temporary computers that get wiped when the session
ends — any skill saved only there is lost. Skills saved **here** survive
forever and are reachable from every Claude environment.

Note: this repository is public, so nothing secret (passwords, keys,
private client information) ever goes into a skill here.

## The golden rule

> If a Claude writes or improves a skill during a session, the last step of
> that session is always: **"Commit and push that skill to the skill home
> in my first-contributions repo."**
>
> If it isn't pushed here, it doesn't exist.

## How each environment uses this repo

**Claude Code terminal CLI or desktop app (your computer):**
Run these two commands once, and the skills follow you into every project:

```
/plugin marketplace add DrECL/first-contributions
/plugin install personal-skills
```

**Claude Code on the web / cloud sessions:**
Sessions on this repo load the skills automatically via `.claude/settings.json`.
For sessions on any *other* repo, add this to that repo's
`.claude/settings.json` (or just ask the session's Claude to add it —
point it at this README):

```json
{
  "extraKnownMarketplaces": {
    "claude-skills": {
      "source": {
        "source": "github",
        "repo": "DrECL/first-contributions"
      }
    }
  },
  "enabledPlugins": {
    "personal-skills@claude-skills": true
  }
}
```

## How to add a new skill

1. Copy the `personal-skills/skills/skill-template/` folder.
2. Rename the copy to your skill's name (lowercase-with-dashes).
3. Edit its `SKILL.md` — the template explains each part.
4. Commit and push. Done — every environment picks it up.

Or simply tell any Claude session: *"Add a skill to my skill home in the
first-contributions repo"* — it can read this README and follow the same
steps.

## Layout

```
.claude-plugin/
└── marketplace.json          ← makes this repo installable as a plugin source
plugins/
├── README.md                 ← you are here
└── personal-skills/
    ├── .claude-plugin/
    │   └── plugin.json       ← the plugin's identity card
    └── skills/               ← one folder per skill
        └── skill-template/   ← copy me to create new skills
```
