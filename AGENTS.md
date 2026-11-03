# Firecrawl Workflows Repo

**Source of truth** for Firecrawl workflow skills. All 19 workflow skills under `skills/` are authored here and safe to edit. CI mirrors `skills/` into the `firecrawl/skills` catalog under `skills/workflows/` on every push to main.

## Vertical skills (opt-in)

Vertical skills for a specific domain (legal, financial, health, travel and similar) live in `verticals/<skill-name>/`, not `skills/`. CI mirrors `verticals/` into the catalog under `skills/verticals/`. `firecrawl init` and `firecrawl setup workflows` install only the `skills/workflows/` family, so they don't install vertical skills. Users opt in with `firecrawl setup verticals`, `firecrawl setup <name>` or `npx skills add firecrawl/skills --skill <name> -g -y`. New vertical skills go in `verticals/` and need no CLI release. Don't list them in the plugin manifests, which install as one bundle.

## Routing rule

Workflow skills → PR this repo. CLI skills (including the research/developer index skills) → PR `firecrawl/cli`. Build/SDK skills → PR the `firecrawl` monorepo (`skills/`). The catalog `firecrawl/skills` is read-only — never PR it.

## Authoring Rules

- Keep each `SKILL.md` concise and trigger-oriented; lead with "use this when..." guidance.
- Workflow skills infer from the user's request first and only ask short clarifying questions when required inputs are missing.
- Favor endpoint names in slash notation: `/scrape`, `/search`, `/interact`.
- Keep CLI references short and defer to `firecrawl/cli` instead of duplicating command manuals.
- Skill layout: `skills/<skill-name>/SKILL.md` (or `verticals/<skill-name>/SKILL.md`), deeper docs in `references/` beside it, one level deep.
- Authoring guide: `skills/firecrawl-workflows/references/workflow-authoring.md`.
