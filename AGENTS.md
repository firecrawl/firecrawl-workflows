# Firecrawl Workflows Repo

Distribution repo for Firecrawl-powered workflow skills.

## Safe To Edit

Current authored skills:

- `skills/firecrawl-workflows/`
- `skills/firecrawl-website-design-clone/`
- `skills/firecrawl-deep-research/`
- `skills/firecrawl-seo-audit/`
- `skills/firecrawl-lead-research/`
- `skills/firecrawl-qa/`
- `skills/firecrawl-competitive-intel/`
- `skills/firecrawl-company-directories/`
- `skills/firecrawl-dashboard-reporting/`
- `skills/firecrawl-knowledge-base/`
- `skills/firecrawl-knowledge-ingest/`
- `skills/firecrawl-lead-gen/`
- `skills/firecrawl-market-research/`
- `skills/firecrawl-research-papers/`
- `skills/firecrawl-demo-walkthrough/`
- `skills/firecrawl-shop/`

Plugin metadata and top-level docs are also safe to edit:

- `.cursor-plugin/`
- `.claude-plugin/`
- `.codex-plugin/`
- `README.md`
- `AGENTS.md`
- `references/`

## Intent

Use this repo when an agent should produce a real deliverable with Firecrawl, such as:

- website design-system extraction
- recurring market scans
- SEO audits
- QA reports
- research briefs from web evidence
- literature reviews over published papers, including biomedical, clinical, and
  other life-science topics, through the paper index rather than web search
- knowledge bases
- lead lists
- research-backed content or planning artifacts
- other structured workflows that demonstrate Firecrawl in action

Do not use this repo as the main source for:

- one-off web search or scraping during the current session
- product-code Firecrawl integrations
- CLI command reference

Those belong to the core CLI skills and build skills.

## Authoring Rules

- Keep each `SKILL.md` concise and trigger-oriented.
- Keep onboarding lightweight: infer from context first, then ask at most 1-3 blocking questions only if needed.
- Stay harness-agnostic; do not hardcode Claude Code, Cursor, Codex, Hermes, or any specific tool/function API.
- Prefer wording like "ask the user a clarifying question" or "use sub-agents if available."
- Target a named deliverable with a clear output format.
- Make workflows automation-friendly by defining required inputs, steps, artifacts, and verification.
- Flag parallelizable work generically when competitors, URLs, pages, or sources can be processed independently.
- Use Firecrawl through the CLI or equivalent Firecrawl tool surface available in the host environment.
- Keep the paper index and web search distinct. Paper work goes through `firecrawl_research_*` (MCP) or `firecrawl research` (CLI); `categories: ["research"]` on Firecrawl search is a website filter and does not reach that index.

## Repo Shape

Expected layout:

- `skills/<skill-name>/SKILL.md` for each skill
- `references/` for shared authoring and packaging guidance
- plugin metadata under `.cursor-plugin/`, `.claude-plugin/`, and `.codex-plugin/`

Keep supporting references one level deep.
