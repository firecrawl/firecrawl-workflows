# Firecrawl Workflows

**Source of truth for Firecrawl workflow skills** — outcome-focused skills for AI coding agents: research reports, SEO audits, QA reports, knowledge bases, lead lists, and agent-ready website design systems. CI mirrors `skills/` into the [`firecrawl/skills`](https://github.com/firecrawl/skills) catalog, which is the promoted install path.

## Install

Preferred — the full catalog (includes these workflows):

```bash
npx skills add firecrawl/skills
```

Installing directly from this repo also works and serves the same 23 workflow skills:

```bash
npx skills add firecrawl/firecrawl-workflows
```

Focused workflows cover professional discovery and enrichment, published business contacts, current job openings, fantasy decisions, existing brand assets and official documentation. Website design extraction and SEO auditing retain their existing skill names.

With the Firecrawl CLI installed, use `firecrawl setup workflows` to install the workflow set. The workflows use supported CLI commands, inspect discovered provider contracts before execution and preserve source evidence in their results. Alexandria provider access requires an authenticated Firecrawl account; workflows describe public-source fallbacks where applicable.

## Contributing

Workflow skills are authored **here** — PRs welcome. For other skill families: CLI skills (including the research/developer index skills) → PR [`firecrawl/cli`](https://github.com/firecrawl/cli). Build/SDK skills → PR the [`firecrawl`](https://github.com/firecrawl/firecrawl) monorepo (`skills/`). The catalog ([`firecrawl/skills`](https://github.com/firecrawl/skills)) is a read-only distribution repo synced by CI — never PR it directly.

For authoring guidance, see [`skills/firecrawl-workflows/references/workflow-authoring.md`](./skills/firecrawl-workflows/references/workflow-authoring.md).

## License

ISC
