# Firecrawl Workflows

Outcome-focused Firecrawl skills for AI coding agents. These workflows use Firecrawl to produce concrete deliverables such as research reports, SEO audits, QA reports, knowledge bases, lead lists, and agent-ready website design systems.

This repo is the workflow counterpart to the two existing Firecrawl skill segments:

- **Core skills**: live web work with the Firecrawl CLI
- **Build skills**: integrate Firecrawl into application code
- **Workflows**: use Firecrawl to complete repeatable business and creative tasks

## Install

The Firecrawl CLI setup should install all three segments together:

```bash
npx -y firecrawl-cli@latest init --all --browser
```

Or install this workflow pack directly:

```bash
npx skills add firecrawl/firecrawl-workflows
```

## Available Skills

| Skill | Outcome |
| --- | --- |
| [`firecrawl-workflows`](./skills/firecrawl-workflows) | Umbrella skill for choosing and running Firecrawl workflow skills |
| [`firecrawl-deep-research`](./skills/firecrawl-deep-research) | Multi-source sourced research reports from web evidence (not literature reviews) |
| [`firecrawl-seo-audit`](./skills/firecrawl-seo-audit) | Site maps, on-page SEO checks, SERP comparison, and prioritized recommendations |
| [`firecrawl-lead-research`](./skills/firecrawl-lead-research) | Pre-meeting company/person intelligence briefs |
| [`firecrawl-qa`](./skills/firecrawl-qa) | Live-site QA reports with issues and reproduction steps |
| [`firecrawl-competitive-intel`](./skills/firecrawl-competitive-intel) | Recurring pricing, feature, and changelog monitoring |
| [`firecrawl-company-directories`](./skills/firecrawl-company-directories) | Directory extraction into structured company lists |
| [`firecrawl-dashboard-reporting`](./skills/firecrawl-dashboard-reporting) | Metrics extraction from dashboards and internal web tools |
| [`firecrawl-knowledge-base`](./skills/firecrawl-knowledge-base) | LLM-ready reference docs, RAG chunks, training data, or docs mirrors |
| [`firecrawl-knowledge-ingest`](./skills/firecrawl-knowledge-ingest) | Auth-gated or JS-heavy docs portal ingestion |
| [`firecrawl-lead-gen`](./skills/firecrawl-lead-gen) | Prospect list generation from databases and directories |
| [`firecrawl-market-research`](./skills/firecrawl-market-research) | Market, financial, earnings, and industry research |
| [`firecrawl-research-papers`](./skills/firecrawl-research-papers) | Literature reviews from the paper index — biomedical and life-science work (PubMed, bioRxiv, medRxiv) plus arXiv preprints — and from PDFs and whitepapers |
| [`firecrawl-demo-walkthrough`](./skills/firecrawl-demo-walkthrough) | Product flow walkthroughs and UX teardown reports |
| [`firecrawl-shop`](./skills/firecrawl-shop) | Product research and shopping recommendations |
| [`firecrawl-website-design-clone`](./skills/firecrawl-website-design-clone) | Extract a website's design system into an agent-ready `DESIGN.md` |

## Authoring Principles

Every workflow skill in this repo must:

- stay harness-agnostic; do not name editor-specific APIs such as `AskUserQuestion`
- infer from context first, then ask at most 1-3 blocking onboarding questions only if needed
- target a real user deliverable, not a Firecrawl feature demo
- be structured enough for recurring automation
- call out independently parallelizable work for sub-agents or equivalent parallel task runners
- use Firecrawl through the CLI or the host's equivalent Firecrawl tool surface

## Reference Layouts

This repo takes the useful parts of:

- [`anthropics/skills`](https://github.com/anthropics/skills): simple `skills/<name>/SKILL.md` layout
- [`anthropics/financial-services`](https://github.com/anthropics/financial-services): outcome-focused domain workflows and optional agent packaging
- [`anthropics/claude-for-legal`](https://github.com/anthropics/claude-for-legal): verticalized workflows with concrete deliverables

The heavier managed-agent and partner-plugin scaffolding is intentionally omitted until the workflow pack needs it.

## Scope

These skills are for repeatable workflows that use Firecrawl during an agent session. If the task is to add Firecrawl API calls to a product, use the build skills instead. If the task is one-off web access, use the core CLI skills directly.

## License

ISC
