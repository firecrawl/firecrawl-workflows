# Firecrawl Workflows

**Source of truth for Firecrawl workflow skills** — outcome-focused skills for AI coding agents: research reports, SEO audits, QA reports, knowledge bases, lead lists, and agent-ready website design systems. CI mirrors `skills/` into the [`firecrawl/skills`](https://github.com/firecrawl/skills) catalog, which is the promoted install path.

## Install

Preferred — the full catalog (includes these workflows):

```bash
npx skills add firecrawl/skills
```

Installing directly from this repo also works and serves the same 16 workflow skills:

```bash
npx skills add firecrawl/firecrawl-workflows
```

## Contributing

Workflow skills are authored **here** — PRs welcome. For other skill families: CLI skills (including the research/developer index skills) → PR [`firecrawl/cli`](https://github.com/firecrawl/cli). Build/SDK skills → PR the [`firecrawl`](https://github.com/firecrawl/firecrawl) monorepo (`skills/`). The catalog ([`firecrawl/skills`](https://github.com/firecrawl/skills)) is a read-only distribution repo synced by CI — never PR it directly.

For authoring guidance, see [`skills/firecrawl-workflows/references/workflow-authoring.md`](./skills/firecrawl-workflows/references/workflow-authoring.md).

## License

ISC
