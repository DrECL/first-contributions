# Claude Skills — Dr. Erick's Shared Skill Home

This repository is the **one durable place** where my Claude skills live.
Cloud sessions run in temporary computers that get wiped when the session
ends — any skill saved only there is lost. Skills saved **here** survive
forever and are reachable from every Claude environment.

## The golden rule

> If a Claude writes or improves a skill during a session, the last step of
> that session is always: **"Commit and push that skill to the
> claude-skills repo."**
>
> If it isn't pushed here, it doesn't exist.

## How each environment uses this repo

**Claude Code terminal CLI or desktop app (your computer):**
Run these two commands once, and the skills follow you into every project:

```
/plugin marketplace add DrECL/claude-skills
/plugin install personal-skills
```

**Claude Code on the web / cloud sessions:**
Add this to the `.claude/settings.json` of any repo you work in (or just ask
the session's Claude to add it — point it at this README):

```json
{
  "extraKnownMarketplaces": {
    "claude-skills": {
      "source": {
        "source": "github",
        "repo": "DrECL/claude-skills"
      }
    }
  },
  "enabledPlugins": {
    "personal-skills@claude-skills": true
  }
}
```

## How to add a new skill

1. Copy the `plugins/personal-skills/skills/skill-template/` folder.
2. Rename the copy to your skill's name (lowercase-with-dashes).
3. Edit its `SKILL.md` — the template explains each part.
4. Commit and push. Done — every environment picks it up.

Or simply tell any Claude session: *"Add a skill to my claude-skills repo
that does X"* — it can read this README and follow the same steps.

## Repository layout

```
claude-skills/
├── README.md                     ← you are here
├── .claude-plugin/
│   └── marketplace.json          ← makes this repo installable as a plugin source
└── plugins/
    └── personal-skills/
        ├── .claude-plugin/
        │   └── plugin.json       ← the plugin's identity card
        └── skills/
            └── skill-template/   ← copy me to create new skills
                └── SKILL.md
```
