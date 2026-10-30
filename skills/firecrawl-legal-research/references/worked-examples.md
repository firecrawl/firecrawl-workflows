# Worked Examples

Runs from October 30, 2026 with the Firecrawl CLI 1.26.24. Output is trimmed to the first results; counts and costs are as returned. Results change as new documents are published, so rerun before relying on them.

## Court opinions: Firecrawl Government Index

Question: Ninth Circuit opinions on qualified immunity in excessive force cases.

```bash
firecrawl gov "qualified immunity excessive force" --court ca9 --limit 5 --json
```

Five results. The snippets below are search descriptions that quote each opinion's court staff summary, which states it "constitutes no part of the opinion of the court". The results carry no decision date, so read it from the document. The first three:

```text
https://govinfo.gov/content/pkg/USCOURTS-ca9-23-15465/pdf/USCOURTS-ca9-23-15465-0.pdf
  UNITED STATES COURT OF APPEALS FOR THE NINTH CIRCUIT > Excessive Force/Qualified Immunity
  The panel reversed the district court's denial, on summary judgment, of qualified immunity
  to City of Sparks police officers ...
https://cdn.ca9.uscourts.gov/datastore/opinions/2024/08/09/23-15465.pdf
  [same snippet as above]
https://govinfo.gov/content/pkg/USCOURTS-ca9-23-15953/pdf/USCOURTS-ca9-23-15953-0.pdf
  UNITED STATES COURT OF APPEALS FOR THE NINTH CIRCUIT > Excessive Force/Qualified Immunity
  The panel affirmed on qualified immunity grounds the district court's summary judgment in
  favor of police officers in an action brought pursuant to 42 U.S.C. § 1983 ...
```

The first two results share docket number 23-15465 and the same summary, one from govinfo.gov and one from the court's site. Reading the first two pages of the court's copy gives the caption and date:

```bash
firecrawl scrape "https://cdn.ca9.uscourts.gov/datastore/opinions/2024/08/09/23-15465.pdf" --max-pages 2 --json
```

Condensed from the first page:

```text
FOR PUBLICATION
UNITED STATES COURT OF APPEALS FOR THE NINTH CIRCUIT
JOSEPH WILLIAMS, No.23-15465, Plaintiff-Appellee, v. CITY OF SPARKS; ... Defendants-Appellants.
Appeal from the United States District Court for the District of Nevada
Argued and Submitted March 7, 2024 Las Vegas, Nevada
Filed August 9, 2024
```

That scrape cost 2 Firecrawl credits, one per parsed page. The opinion has 19 pages, so parsing all of it would cost 19. Treat results as one document only when the court, docket number and decision date match. Firecrawl credits used for the search: 0.

## SEC filings: EDGAR full-text search

Question: 8-K filings in 2026 that contain the phrase "material cybersecurity incident".

```bash
firecrawl search "full-text search of SEC EDGAR filings" --sources alexandria --json
firecrawl list sec-gov filings/search --include-costs --pretty
firecrawl scrape sec-gov/filings/search --options '{"query":"\"material cybersecurity incident\"","forms":["8-K"],"from":"2026-01-01","to":"2026-10-30"}' --json
```

`total: 18`, `has_more: false`. The first three hits, all with Item 1.05:

```text
8-K/A  2026-02-03  WYTEC INTERNATIONAL INC  Item 1.05 Material Cybersecurity Incidents
       https://www.sec.gov/Archives/edgar/data/1560143/000168316826000732/wytec_8ka1.htm
8-K    2026-09-23  Astrana Health, Inc.     Item 1.05 Material Cybersecurity Incidents
       https://www.sec.gov/Archives/edgar/data/1083446/000110465926109813/asth-20260922x8k.htm
8-K    2026-04-08  Bitcoin Depot Inc.       Items 1.05 and 9.01
       https://www.sec.gov/Archives/edgar/data/1901799/000119312526147772/btm-20260406.htm
```

Only 3 of the 18 hits are 8-K documents listing Item 1.05. The other 15 are exhibits (EX-99.1, EX-10.1 and EX-2.1) to 8-Ks filed under other items, such as 2.02 and 1.01, that contain the phrase. Check the `items` codes or read the document before calling a filing an incident disclosure. The `forms` filter also matched an amendment (8-K/A). Firecrawl credits used: 5 (discovery and the contract read cost 0).

Pass the CIK from any hit to `filings/filings` to list that company's own filings. For Bitcoin Depot (CIK 1901799) the ticker `BTM` returned `not_found` at 0 Firecrawl credits, and the CIK worked:

```bash
firecrawl scrape sec-gov/filings/filings --options '{"cik":1901799,"forms":["8-K"],"from":"2026-01-01","limit":5}' --json
```

```text
8-K  2026-05-19  Item 3.01
     https://www.sec.gov/Archives/edgar/data/1901799/000119312526230708/d228011d8k.htm
8-K  2026-05-18  Items 1.03, 2.04, 5.02, 7.01, 9.01
     https://www.sec.gov/Archives/edgar/data/1901799/000119312526227832/d130621d8k.htm
8-K  2026-05-12  Items 2.02, 5.02
     https://www.sec.gov/Archives/edgar/data/1901799/000119312526219660/btm-20260512.htm
8-K  2026-04-08  Items 1.05, 9.01
     https://www.sec.gov/Archives/edgar/data/1901799/000119312526147772/btm-20260406.htm
8-K  2026-03-24  Items 5.02, 7.01, 9.01
     https://www.sec.gov/Archives/edgar/data/1901799/000119312526121822/btm-20260319.htm
```

These records carry the filing link as `primary_document_url`; `filings/search` hits call it `document_url`.

Firecrawl credits used: 5.

## Patents: USPTO Patent Public Search

Question: recent US patents and pre-grant publications with "quantum computing" in the title. With `databases` omitted, the search covers pre-grant publications (US-PGPUB), granted patents (USPAT) and OCR of pre-1976 patents (USOCR).

```bash
firecrawl search "search US patents by title or assignee" --sources alexandria --json
firecrawl list uspto-gov intellectual-property/patent_search --include-costs --pretty
firecrawl scrape uspto-gov/intellectual-property/patent_search --options '{"query":"\"quantum computing\".ti.","page_size":10}' --json
```

`total_results: 683` (patent families, each collapsed to one row), 10 returned, `next_start: 10`. The first three, with applicant:

```text
US 20260340474 A1  2026-10-29  LG ELECTRONICS INC.
  QUANTUM COMPUTING DEVICE FOR INVERSE NOISE CHARACTERIZATION AND ERROR MITIGATION BY
  RESTRICTED EVOLUTION AND METHOD THEREOF
  https://ppubs.uspto.gov/pubwebapp/external.html?q=20260340474.pn.&db=US-PGPUB
US 20260340466 A1  2026-10-29  eleQtron GmbH
  QUANTUM COMPUTING DEVICE, QUANTUM COMPUTING ARRANGEMENT, QUANTUM COMPUTER AND METHOD FOR
  OPERATING A QUANTUM COMPUTING DEVICE
  https://ppubs.uspto.gov/pubwebapp/external.html?q=20260340466.pn.&db=US-PGPUB
US 20260340467 A1  2026-10-29  eleQtron GmbH
  QUANTUM COMPUTING ARRANGEMENT AND QUANTUM COMPUTER
  https://ppubs.uspto.gov/pubwebapp/external.html?q=20260340467.pn.&db=US-PGPUB
```

The record for the first result:

```bash
firecrawl scrape uspto-gov/intellectual-property/patent_document --options '{"number":"US-20260340474-A1"}' --json
```

It returned the abstract, inventors and applicants with their locations, CPC codes and continuity data (a US provisional application, 63794979). Claims and description were null because `include_full_text` was not set; that option adds the claims and description HTML. Examiners and cited references were empty for this pre-grant publication. Firecrawl credits used: 5 for the search and 5 for the document.
