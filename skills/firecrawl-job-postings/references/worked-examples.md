# Worked Examples

Runs from November 1, 2026 (UTC) with the Firecrawl CLI 1.26.24. Output is trimmed to the first results; counts and costs are as returned. Boards change daily as employers open and close roles, so rerun before relying on them.

## Greenhouse: one employer's engineering openings

Question: engineering roles open on Discord's Greenhouse board.

```bash
firecrawl search "list open jobs on an employer's Greenhouse or Ashby job board" --sources alexandria --json
firecrawl list greenhouse-io jobs/jobs --include-costs --pretty
firecrawl scrape greenhouse-io/jobs/jobs --options '{"board_token":"discord","keyword":"engineer","per_page":5}' --json
```

Discovery returned `greenhouse-io` `jobs/jobs`, `ashbyhq-com` `jobs/jobs`, `ashbyhq-com` `jobs/board`, `greenhouse-io` `jobs/search` and `greenhouse-io` `jobs/board`. The list call returned `total: 41` openings on the board, `matched: 26` for the keyword, 5 on this page and `next_page: 2`. The keyword matches title and location as a substring, so "engineer" also matched engineering manager roles. The first three:

```text
Engineering Manager, Machine Learning (Safety)  Remote (US)
  first_published 2026-08-20T13:43:23-04:00  updated_at 2026-10-29T14:21:36-04:00
  https://job-boards.greenhouse.io/discord/jobs/8722538002
Engineering Manager, Notifications  Remote (U.S.)
  first_published 2026-10-13T17:34:29-04:00  updated_at 2026-10-20T19:56:15-04:00
  https://job-boards.greenhouse.io/discord/jobs/8885940002
Engineering Manager, Platform Security  Remote (West Coast)
  first_published 2026-07-27T17:23:50-04:00  updated_at 2026-10-21T14:47:00-04:00
  https://job-boards.greenhouse.io/discord/jobs/8649856002
```

`application_deadline` was null on all five. The posting record for the second result, with `job_id` passed as a string:

```bash
firecrawl scrape greenhouse-io/jobs/job --options '{"board_token":"discord","job_id":"8885940002"}' --json
```

```text
title           Engineering Manager, Notifications
requisition_id  R-107460
pay_ranges      [{"title": "Base Salary", "currency": "USD", "min_cents": 24800000, "max_cents": 31000000, "blurb": "<p><em>The base salary range ..."}]
```

The posting text states the same base salary range, $248,000 to $310,000 USD, and the pay blurb says it covers base salary only, not equity or benefits. Firecrawl credits used: 5 for the list and 5 for the posting (discovery and the contract read cost 0).

## Ashby: remote engineering roles with published pay

Question: remote engineering roles on OpenAI's Ashby board.

```bash
firecrawl list ashbyhq-com jobs/board --include-costs --pretty
firecrawl scrape ashbyhq-com/jobs/board --options '{"board":"openai"}' --json
firecrawl scrape ashbyhq-com/jobs/jobs --options '{"board":"openai","keyword":"engineer","remote":true,"per_page":5}' --json
```

`jobs/board` returned `name: OpenAI`, `job_count: 810`, 86 teams and `timezone: America/Los_Angeles`. `jobs/jobs` returned `total: 810`, `matched: 233`, 5 on this page and `next_page: 2`. The first three:

```text
Data Engineer  Applied AI Engineering  San Francisco  Hybrid  FullTime
  published_at 2023-09-26T22:34:48.884+00:00  salary_summary "$235K - $385K"
  https://jobs.ashbyhq.com/openai/fc5bbc77-a30c-4e7a-9acc-8a2e748545b4
Software Engineer, Financial Engineering  Applied AI Engineering  San Francisco  Hybrid  FullTime
  published_at 2026-08-28T17:41:41.133+00:00  salary_summary "$266K - $445K"
  https://jobs.ashbyhq.com/openai/4ef5bf23-cf0e-4b97-a639-11f963c99b88
Security Engineer, Application Security  Security  San Francisco  Hybrid  FullTime
  published_at 2024-10-02T20:52:24.155+00:00  salary_summary "$266K - $445K"
  https://jobs.ashbyhq.com/openai/0322d6d8-6588-4209-a304-83e768063a25
```

All five results had `is_remote: true`, but four had `workplace_type: Hybrid` and one `Remote`, so report both fields instead of calling a posting remote from the filter alone. One listed posting was published in 2023; show that date as given. The salary components carry `currency: USD` and `interval: 1 YEAR`. Firecrawl credits used: 5 for the board and 5 for the list.

The list has no description, so the shortlisted second result goes to `jobs/job` (run at 21:28 UTC the same day):

```bash
firecrawl scrape ashbyhq-com/jobs/job --options '{"url":"https://jobs.ashbyhq.com/openai/4ef5bf23-cf0e-4b97-a639-11f963c99b88"}' --json
```

```text
title                            Software Engineer, Financial Engineering
date_posted                      2026-08-28
application_deadline             null
is_listed                        true
workplace_type                   Hybrid
job_location_type                TELECOMMUTE
applicant_location_requirements  ["United States"]
base_salary                      {"currency": "USD", "max_value": 445000, "min_value": 266000, "unit_text": "YEAR"}
apply_url                        https://jobs.ashbyhq.com/openai/4ef5bf23-cf0e-4b97-a639-11f963c99b88/application
```

The record also carries `description_text` (5,601 characters here) for qualifications. One posting is labeled `Hybrid`, `TELECOMMUTE` and open to applicants in the United States, so quote all three rather than one. Firecrawl credits used: 5.

## Workday: one career site, then one posting

Question: software engineer roles on Workday's own career site.

```bash
firecrawl find-tools --options '{"query":"read a Workday job posting","limit":5}' --json
firecrawl search "myworkdayjobs.com software engineer job" --limit 5 --sources web --json
firecrawl scrape myworkdayjobs-com/jobs/search_jobs --options '{"career_site":"https://workday.wd5.myworkdayjobs.com/Workday","query":"software engineer","limit":5}' --json
```

`find-tools` ranked `myworkdayjobs-com` `jobs/get_job` and `jobs/search_jobs` first. The web search (2 Firecrawl credits) returned the career site `https://workday.wd5.myworkdayjobs.com/Workday` but no posting URLs, so `search_jobs` supplied them: `total: 167`, 5 returned, `next_offset: 5`. Search results carry only relative dates:

```text
Senior Software Engineer  USA, CA, Pleasanton  Flex  Posted 25 Days Ago
  https://workday.wd5.myworkdayjobs.com/Workday/job/USA-CA-Pleasanton/Senior-Software-Engineer_JR-0107919
Software Engineer/Senior Software Engineer - AI Platform (Agent Cell)  3 Locations  Flex  Posted 9 Days Ago
  https://workday.wd5.myworkdayjobs.com/Workday/job/USA-CO-Boulder/Software-Engineer-Senior-Software-Engineer---AI-Platform--Agent-Runtime-_JR-0109507
Principal Software Engineer, Operations Engineering  USA, OR, Beaverton  Flex  Posted 3 Days Ago
  https://workday.wd5.myworkdayjobs.com/Workday/job/USA-OR-Beaverton/Principal-Software-Engineer--Operations-Engineering_JR-0111127
```

`get_job` on the last one gives the posting date and status:

```bash
firecrawl scrape myworkdayjobs-com/jobs/get_job --options '{"url":"https://workday.wd5.myworkdayjobs.com/Workday/job/USA-OR-Beaverton/Principal-Software-Engineer--Operations-Engineering_JR-0111127"}' --json
```

```text
hiring_organization  Workday, Inc.
posted_date          2026-10-29
posting_end_date     null
can_apply            true
time_type            Full Time
remote_type          Flex
requisition_id       JR-0111127
```

Pay is not a separate field; the description text states "Primary Location Base Pay Range: $182,400 USD - $273,600 USD" and "Additional US Location(s) Base Pay Range: $173,300 USD - $309,600 USD". Firecrawl credits used: 2 for the web search, 5 for the search and 5 for the posting.

## ZipRecruiter: provider errors and the public-web fallback

Question: the salary benchmark for data analysts in Austin, TX, and recent postings.

```bash
firecrawl find-tools --options '{"query":"salary estimate for a job title in a US city","limit":5}' --json
firecrawl list ziprecruiter-com jobs/salary --include-costs --pretty
firecrawl scrape ziprecruiter-com/jobs/salary --options '{"title":"data analyst","location":"Austin, TX"}' --json
firecrawl scrape ziprecruiter-com/jobs/search_jobs --options '{"title":"data analyst","location":"Austin, TX","posted_within_days":5}' --json
```

Discovery returned `ziprecruiter-com` `jobs/salary`. Every execution failed inside a successful response, at 0 Firecrawl credits:

```json
{
  "code": "provider_error",
  "message": "We couldn’t complete this request. Please try again later.",
  "status": 502
}
```

Seven calls between 20:03 and 20:07 UTC, across `jobs/salary`, `jobs/search_jobs` and `jobs/job` (the last with the contract's example URL), all returned this error. Record it in Gaps And Limits. The fallback reads the public salary page directly:

```bash
firecrawl scrape "https://www.ziprecruiter.com/Salaries/Data-Analyst-Salary-in-Austin,TX" --max-age 0 --json
```

The page title was "Salary: Data Analyst in Austin, Texas (November, 2026)". Condensed from the page text:

```text
The median wage is $76K / yr.
$94.5K is the 75th percentile. Wages above this are outliers.
As of Nov 1, 2026, the average annual pay for aData Analyst in Austin is $81,914 a year.
... the majority of Data Analyst salaries currently range between$62,000 (25th percentile) to
$96,100 (75th percentile) with top earners (90th percentile) making $119,440 annually in Austin.
```

The page states two different 75th percentiles ($94.5K in the chart and $96,100 in the text), so quote the sentence you use with its source and date. This is an occupation benchmark, not the pay of any posting. Firecrawl credits used: 1 for the scrape; the failed provider calls cost 0.
