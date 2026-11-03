---
name: firecrawl-workflows
description: Run focused Firecrawl workflows for research, documentation lookup, SEO audits, QA, lead discovery and enrichment, business contacts, current jobs, fantasy decisions, sports data, US court opinions, SEC filings and patents, company financials and market prices, drug, device and trial records, company due diligence, travel, real estate, brand assets, knowledge bases and website design systems. Use when the user wants a sourced answer or deliverable from a workflow rather than raw extraction or product-code integration.
license: ISC
metadata:
  author: firecrawl
  version: "0.1.3"
  homepage: https://www.firecrawl.dev
  source: https://github.com/firecrawl/firecrawl-workflows
inputs:
  - name: FIRECRAWL_API_KEY
    description: Firecrawl API key for hosted Firecrawl requests when the workflow runs through the CLI or API.
    required: true
references:
  - references/workflow-authoring.md
---

# Firecrawl Workflows

Use this when the user wants a finished deliverable powered by Firecrawl, not only raw web extraction and not product-code integration.

## Choose The Workflow

- Use [firecrawl-lead-gen](../firecrawl-lead-gen/SKILL.md) to discover and qualify new professional or business prospects, or extract lists from a specified database or directory.
- Use [firecrawl-lead-research](../firecrawl-lead-research/SKILL.md) to research supplied people or companies, enrich a lead CSV while preserving rows, or prepare a meeting brief.
- Use [firecrawl-business-contacts](../firecrawl-business-contacts/SKILL.md) to find published organizational contacts for named or described businesses.
- Use [firecrawl-job-postings](../firecrawl-job-postings/SKILL.md) to find and verify current job openings, reading Greenhouse, Ashby and Workday boards through Firecrawl Alexandria with a public-web fallback.
- Use [firecrawl-fantasy-sports](../firecrawl-fantasy-sports/SKILL.md) for a fantasy decision grounded in current news and league context.
- Use [firecrawl-sports-data](../firecrawl-sports-data/SKILL.md) for dated scores, schedules, standings, rosters, injury reports, Sleeper league data, ticket listings and sports prediction market prices from Firecrawl Alexandria providers, each with a link and timestamp. Start/sit, waiver, trade and draft decisions go to `firecrawl-fantasy-sports`.
- Use the core `firecrawl-developer-index` skill (installed with the Firecrawl CLI skills) for official documentation, setup, usage, migrations, compatibility and upstream implementation or bug evidence.
- Use [firecrawl-website-design-clone](../firecrawl-website-design-clone/SKILL.md) for an existing website's brand assets or its UI system. Return a compact asset reference for a document, or an agent-ready `DESIGN.md` for UI implementation.
- Use [firecrawl-research-papers](../firecrawl-research-papers/SKILL.md) for literature reviews and paper-backed synthesis, including biomedical, clinical, drug, gene, disease, epidemiology, and public-health topics. It queries Firecrawl's paper index — PubMed, bioRxiv, medRxiv, and arXiv abstracts with full text reachable per paper — rather than searching websites. FDA drug labels, adverse event reports, recalls and trial registry records go to `firecrawl-health-research`.
- Use [firecrawl-deep-research](../firecrawl-deep-research/SKILL.md) for sourced multi-source research reports built from **web** evidence: market, policy, technical, or industry topics. Not for literature reviews — when the evidence base is published papers, use `firecrawl-research-papers` above.
- Use [firecrawl-seo-audit](../firecrawl-seo-audit/SKILL.md) for site structure, on-page SEO, keyword, and SERP audits.
- Use [firecrawl-qa](../firecrawl-qa/SKILL.md) for live-site QA testing and bug reports.
- Use [firecrawl-competitive-intel](../firecrawl-competitive-intel/SKILL.md) for recurring pricing, feature, and changelog monitoring.
- Use [firecrawl-company-directories](../firecrawl-company-directories/SKILL.md) for directory extraction into company lists.
- Use [firecrawl-dashboard-reporting](../firecrawl-dashboard-reporting/SKILL.md) for dashboard metrics extraction.
- Use [firecrawl-knowledge-base](../firecrawl-knowledge-base/SKILL.md) for LLM-ready docs, RAG chunks, training data, or docs mirrors.
- Use [firecrawl-knowledge-ingest](../firecrawl-knowledge-ingest/SKILL.md) for auth-gated or JS-heavy docs portal ingestion.
- Use [firecrawl-market-research](../firecrawl-market-research/SKILL.md) for market sizing, industry and trend research.
- Use [firecrawl-financial-research](../firecrawl-financial-research/SKILL.md) for company financials and SEC XBRL facts checked against the filing, company press releases and analyst ratings, BLS economic series, and crypto and non-sports prediction market prices, each with a link and a date. Filing lists and filing documents go to `firecrawl-legal-research`, and sports markets go to `firecrawl-sports-data`.
- Use [firecrawl-health-research](../firecrawl-health-research/SKILL.md) for FDA drug labels, adverse event reports, FDA drug and device recalls, drug approval records and device clearances, ClinicalTrials.gov trial status and the PubMed record behind a trial, returned as a dated source list. Literature reviews stay with `firecrawl-research-papers`.
- Use [firecrawl-company-due-diligence](../firecrawl-company-due-diligence/SKILL.md) for company and counterparty checks: register entries, officers and owners, sanctions lists, court dockets, trademarks and the regulations the user names for the company, with identifiers matched before any record is called a match.
- Use [firecrawl-legal-research](../firecrawl-legal-research/SKILL.md) for US court opinions, statutes and regulations, SEC filings by full-text search or by company, and patents, returned as a cited source list with a note that it is not legal advice. Company financials and XBRL facts go to `firecrawl-financial-research`.
- Use [firecrawl-demo-walkthrough](../firecrawl-demo-walkthrough/SKILL.md) for product flow walkthroughs and UX teardown reports.
- Use [firecrawl-shop](../firecrawl-shop/SKILL.md) for product research and shopping recommendations, with retailer prices, currency, store, link and retrieval time, plus CPSC recall checks.
- Use [firecrawl-travel-research](../firecrawl-travel-research/SKILL.md) for campsites and permits, vacation rental prices and hotel profiles, returned as dated snapshots with currency, dates searched and retrieval time. Hotel rates and flight prices are recorded as gaps.
- Use [firecrawl-real-estate-research](../firecrawl-real-estate-research/SKILL.md) for cited housing and property facts: US Census housing and demographic estimates, Auction.com auction listings, NYC property records and Swedish rentals, each with its source, geography and date.

Seven of these are opt-in vertical skills that `firecrawl init` does not install: `firecrawl-legal-research`, `firecrawl-financial-research`, `firecrawl-health-research`, `firecrawl-company-due-diligence`, `firecrawl-sports-data`, `firecrawl-travel-research` and `firecrawl-real-estate-research`. If the one the task needs isn't installed, tell the user and offer `npx skills add firecrawl/skills --skill <name>` (or `firecrawl setup <name>` with a recent Firecrawl CLI). Until it's installed, use the generic process below.

If no existing workflow fits, use this generic process and produce a reusable pattern that could become a new skill.

For a focused workflow, follow its deliverable and stopping conditions. A contact card, documentation answer or fantasy recommendation does not require a research report. Reuse adequate evidence and preserve explicit source and no-browse instructions.

## Required Intake

Infer the workflow, inputs, audience, and output format from the user's request and surrounding context. If enough is clear, start immediately.

Ask at most 1-3 concise clarifying questions only when a missing input would block the work, such as:

- the URL, company, topic, or source to analyze
- the desired deliverable or output format
- a constraint that would materially change the workflow

Use the host agent's normal way to ask clarifying questions. Do not depend on a harness-specific function name.

## Default Process

1. Identify the matching workflow and requested artifact.
2. Collect web evidence with Firecrawl through the CLI or equivalent Firecrawl tool surface.
3. Save or cite source evidence so the final claims are traceable.
4. Run independent research units in parallel when available.
5. Synthesize findings into the requested deliverable.
6. Include a short "rerun inputs" block when the workflow could be automated.

## Parallel Work

If appropriate, use sub-agents or equivalent parallel task runners for independent units such as:

- one competitor per researcher
- one URL or page per researcher
- one source category per researcher
- one analysis dimension per reviewer

Keep the handoff generic: provide the unit of work, source URLs or search terms, expected extracted fields, and output format.

## Deliverable Standards

Every workflow should return:

- a concise executive summary
- the evidence base used
- the analysis or artifact requested by the user
- recommendations or next actions when useful
- automation inputs for reruns

For authoring new workflow skills, see [workflow-authoring.md](references/workflow-authoring.md).
