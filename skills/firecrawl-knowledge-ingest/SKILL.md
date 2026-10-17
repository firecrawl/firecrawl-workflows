---
name: firecrawl-knowledge-ingest
description: "Export documentation portals, help centers and API reference sites into local markdown or JSON with Firecrawl browser, including pages that render with JavaScript, hide content behind collapsible navigation or paginate. Use when the user wants a docs site, help center or support knowledge base saved as files, even without an explicit request to scrape."
license: ISC
metadata:
  author: firecrawl
  version: "0.1.0"
  homepage: https://www.firecrawl.dev
  source: https://github.com/firecrawl/firecrawl-workflows
inputs:
  - name: FIRECRAWL_API_KEY
    description: Firecrawl API key for hosted Firecrawl requests.
    required: true
---

# Firecrawl Knowledge Ingest

Use this when a docs portal needs browser navigation, auth, pagination, or JS rendering.

## Onboarding Interview

Infer the portal URL, output format, auth needs, and page limit from context. If the portal is clear, proceed immediately.

Ask at most 1-3 concise questions only if blocked, such as the portal URL, whether authentication is required, or the desired output format.

## Firecrawl Collection Plan

Use Firecrawl browser to:

- open the portal and inspect navigation
- identify sections, categories, sidebar links, and article URLs
- follow sidebar navigation, next links, pagination, load-more controls, or search
- scrape article content as markdown
- extract metadata such as title, section, last updated date, author, and tags

Try Firecrawl map as a supplement for public URLs, but use browser navigation for auth-gated or JS-heavy content.

## Final Deliverable

```markdown
# Knowledge Ingest: [Portal]

## Summary
[Pages extracted, sections covered, limitations]

## Output
[JSON/markdown/merged file path or content]

## Sections
[Section names and article counts]

## Failed Or Restricted Pages
[Any access/loading issues]

## Sources
[URLs extracted]

## Rerun Inputs
workflow: firecrawl-knowledge-ingest
url: [portal url]
format: [json/markdown/merged]
max_pages: [number]
```

## JSON Shape

Use `source`, `url`, `extractedAt`, `totalArticles`, and `sections[]` with article `title`, `url`, `section`, `content`, and `metadata`.

## Quality Bar

- Preserve code examples, tables, and formatting.
- Strip nav chrome, headers, and footers.
- Track extraction progress and page failures.
- Respect authentication boundaries.
