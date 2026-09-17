# Agent skills (project)

Skills use the open [Agent Skills](https://agentskills.io) / `SKILL.md` format. Same folders work across tools **if they live in a path that tool discovers**.

## Where they live in this repo

| Path | Who reads it |
|---|---|
| `.agents/skills/<name>/SKILL.md` | **Canonical shared path** — [Antigravity](https://www.antigravity.google/docs/skills/), and Cursor (also discovers `.agents/skills`) |
| `.cursor/skills/<name>/SKILL.md` | Cursor (project skills). **Keep in sync** with `.agents/skills` |

We keep **both** copies identical so Cursor + Antigravity teammates get the same behavior.

Global Antigravity (all workspaces): `~/.gemini/config/skills/`  
Global Cursor: `~/.cursor/skills/`

## Skills

| Skill | Use for |
|---|---|
| `dsolve-mvp` | Scope lock, hybrid CNN+vision, cut list |
| `fastapi-screening` | `/analyze` CNN + vision API + fusion |

## Portability (as of now)

| App | Works with our skills? | Notes |
|---|---|---|
| **Cursor** | Yes | `.cursor/skills` and `.agents/skills` |
| **Antigravity** | Yes | Needs `.agents/skills` (or legacy `.agent/skills`) — **not** `.cursor/skills` alone |
| **Claude Code** | Yes if also under `.claude/skills` | Optional third mirror if someone uses Claude Code |
| **Codex** | Yes if under `.codex/skills` | Optional |

Format is shared ([agentskills.io](https://agentskills.io)); **discovery paths differ per app**. Putting skills only in `.cursor/skills` = Cursor-only.

## Sync rule

When you edit a skill, update **both**:

```text
.agents/skills/<name>/SKILL.md
.cursor/skills/<name>/SKILL.md
```

Or edit `.agents/skills` first, then copy into `.cursor/skills`.

## What we took from public skill repos

| Source | Verdict |
|---|---|
| [ui-ux-pro-max-skill](https://github.com/nextlevelbuilder/ui-ux-pro-max-skill) | Ideas distilled into `ui-ux-screening` (no full CLI install) |
| [anthropics/skills](https://github.com/anthropics/skills) | `frontend-design` adapted |
| FastAPI skill patterns | Folded into `fastapi-screening` |
| `fastreact` scaffolds | Skipped (auth/DB/S3 scope creep) |

## How to invoke

Mention by name: “use `fastapi-screening` and scaffold `api/`”, or “apply `ui-ux-screening` to the report page”.

## Decisions

All agents must append material choices to [`docs/DECISIONS.md`](DECISIONS.md) in the same turn (see `dsolve-mvp` skill).
