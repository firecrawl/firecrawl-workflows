# Meeting brief

Prepare for a meeting with a supplied person or company.

1. **Company and person data.** Enrich the company and the person through Alexandria with the workflow in the skill: company profile, headcount, funding, and the person's current role.
2. **Recent activity.** Find dated news, funding, launches, hiring and partnerships with `firecrawl search "<company> news" --sources web --json`. Read supplied official URLs with `firecrawl scrape "<url>" --json`, and scrape a result only when its passage is not enough.
3. **Professional context.** Find the person's talks, interviews or posts on the web. Take the current role from step 1, not from a stale page.

## Output

- **Company:** what it sells, size and stage, with sources.
- **People:** role and relevant background for each attendee, with sources.
- **Recent activity:** dated items, newest first, with links.
- **Talking points:** grounded in the evidence above.
- **Likely priorities:** labeled as hypotheses.

Keep to professional context; do not collect private personal details or infer sensitive traits.
