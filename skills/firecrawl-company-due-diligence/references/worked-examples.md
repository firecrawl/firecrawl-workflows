# Worked Examples

Runs from November 1, 2026 with the Firecrawl CLI 1.26.24. Output is trimmed to the first results; counts and costs are as returned. Registers and sanctions lists change daily, so rerun before relying on them.

## UK company register: Companies House

Question: who runs and controls Arm Holdings plc in the UK register?

```bash
firecrawl search "UK company officers and persons with significant control" --sources alexandria --json
firecrawl list find-and-update-company-information-service-gov-uk companies/search --include-costs --pretty
firecrawl scrape find-and-update-company-information-service-gov-uk/companies/search --options '{"query":"Arm Holdings"}' --json
```

Discovery returned `companies/officers` and `companies/persons_with_significant_control` from the Companies House provider. The search returned 20 records on page 1 (`next_page: 2`). Three of them have carried the Arm Holdings name:

```text
11299879  ARM HOLDINGS PLC            incorporated 9 April 2018
          110 Fulbourn Road, Cambridge, Cambridgeshire, United Kingdom, CB1 9NJ
11278800  CHAFFINCH HOLDINGS LIMITED  incorporated 27 March 2018
          previous name: ARM HOLDINGS LIMITED
02548782  SVF HOLDCO (UK) LIMITED     incorporated 16 October 1990
          previous names: ARM HOLDINGS PLC · ADVANCED RISC MACHINES HOLDINGS LIMITED
```

A name alone would conflate these three companies. The rest of this run uses company number 11299879, the company currently registered as ARM HOLDINGS PLC.

```bash
firecrawl scrape find-and-update-company-information-service-gov-uk/companies/persons_with_significant_control --options '{"company_number":"11299879"}' --json
firecrawl scrape find-and-update-company-information-service-gov-uk/companies/officers --options '{"company_number":"11299879","current_only":true}' --json
```

The PSC response has `state: "exempt"`, two ceased records and this exemption:

```text
From 8 April 2024 the company is exempt from the requirement to obtain information of its PSC
because it has voting shares admitted to trading on a market listed in the Register of People
with Significant Control Regulations 2016

Kronos I (Uk) Limited  registration number 13965760  notified 21 August 2023  ceased 1 September 2023
Arm Limited            registration number 02557590  notified 9 April 2018    ceased 21 August 2023
  nature of control (both): Ownership of shares – 75% or more; Ownership of voting rights - 75% or
  more; Right to appoint or remove directors
```

The officers response reports `count_summary: "9 current officers"`: one secretary and eight directors. The first three directors:

```text
DYKSTRA, Karen E.  Director  appointed 17 August 2023  born December 1958  American
FISHER, Ronald D   Director  appointed 17 August 2023  born November 1947  American
HAAS, Rene Anthony Andrada  Director  appointed 25 July 2023  born July 1962  American
```

Each officer carries an `officer_id` and a link to their appointments page. Firecrawl credits used: 15 (5 per call; discovery and the contract read cost 0).

## Sanctions lists: OFAC and OpenSanctions

Question: does Rosneft appear on the OFAC lists, and is one of the directors above on OpenSanctions?

```bash
firecrawl find-tools --options '{"query":"sanctions screening of a company or person","limit":5}' --json
firecrawl list sanctionssearch-ofac-treas-gov sanctions/search --include-costs --pretty
firecrawl scrape sanctionssearch-ofac-treas-gov/sanctions/search --options '{"name":"Rosneft"}' --json
```

Discovery returned `opensanctions-org` `sanctions-screening/search_entities`, `sanctionslist-fcdo-gov-uk` `sanctions-screening/search`, `opensanctions-org` `sanctions-screening/get_entity`, `sanctionssearch-ofac-treas-gov` `sanctions/entity` and `opensanctions-org` `sanctions-screening/list_datasets`. The OFAC search returned `total: 9` at `min_score: 100`, with `sdn_list_updated: "10/28/2026 10:03:02 AM"` and `non_sdn_list_updated: "10/19/2026 10:02:09 AM"`. The nine rows are four entities, because each alias is its own row:

```text
17022  SDN; Non-SDN  6 rows: OAO ROSNEFT OIL COMPANY, OIL COMPANY ROSNEFT, OJSC ROSNEFT OIL COMPANY,
                     OPEN JOINT-STOCK COMPANY ROSNEFT OIL COMPANY, ROSNEFT, ROSNEFT OIL COMPANY
                     26/1 Sofiyskaya Embankment  RUSSIA-EO14024, UKRAINE-EO13662
18301  Non-SDN       ROSNEFT FINANCE S.A.   46A Avenue John F Kennedy; 2nd Floor  UKRAINE-EO13662
18300  Non-SDN       ROSNEFT TRADE LIMITED  Elenion Building 5 Themistokli Dervi; 2nd floor  UKRAINE-EO13662
18299  SDN; Non-SDN  ROSNEFT TRADING S.A.   Rue Place du Lac 2, 1204  UKRAINE-EO13662, VENEZUELA-EO13850
```

```bash
firecrawl scrape sanctionssearch-ofac-treas-gov/sanctions/record --options '{"entity_id":"18301"}' --json
```

The record gives the address (Luxembourg, postal code 1855), program `UKRAINE-EO13662` and the remark "(Linked To: OPEN JOINT-STOCK COMPANY ROSNEFT OIL COMPANY)". Its `identifications` hold a secondary sanctions risk entry ("Ukraine-/Russia-Related Sanctions Regulations, 31 CFR 589.201 and/or 589.209") and directive determinations ("Subject to Directive 2", "Subject to Directive 4"), and no registration number, so a counterparty named Rosneft Finance S.A. can only be compared on name, address and country. Firecrawl credits used: 10.

For OpenSanctions, the director SON, Masayoshi (born August 1957, Japanese) from the officers list:

```bash
firecrawl scrape opensanctions-org/sanctions-screening/search_entities --options '{"q":"Masayoshi Son","schema":"Person"}' --json
```

`total: 0`, empty facets and `data_current_as_of: null`. Reading the source page confirmed it at 1 Firecrawl credit:

```bash
firecrawl scrape "https://www.opensanctions.org/search/?q=Masayoshi+Son&schema=Person" --json
```

```text
Data current as of 2026-11-01
No matching entities were found.
```

Record that as no record found for that query on that date, not as a clearance. The page also states that OpenSanctions "is free for non-commercial users. Businesses must acquire a data license to use the dataset."

Six later OpenSanctions calls returned `data.alexandria[0].error` `{"code":"provider_error","status":502}` at 0 Firecrawl credits: `search_entities` with `{"q":"Rosneft","schema":"Company","topics":["sanction"]}` (twice), `{"q":"Vladimir Putin","topics":["sanction"]}`, `{"q":"Rosneft","schema":"Company"}` and `{"q":"Rosneft"}`, and `get_entity` with `{"entity_id":"ofac-35096"}`. The OFAC provider covered the list check. Firecrawl credits used: 6 (5 for the search and 1 for the page).

## SEC filer, trademarks and court dockets

Question: tie the UK company to its US listing, then list its trademarks and federal dockets.

```bash
firecrawl search "SEC filer profile by ticker or company name" --sources alexandria --json
firecrawl scrape sec-gov/filings/company --options '{"ticker":"ARM"}' --json
```

```text
ARM HOLDINGS PLC /UK  CIK 0001973239  Nasdaq: ARM  state_of_incorporation X0
business address: 110 FULBOURN ROAD, CAMBRIDGE, CB1 9NJ
former name: Arm Holdings Ltd (2023-04-28 to 2023-09-01)
category: Large accelerated filer  latest_filing_date: 2026-09-23
```

The record's address uses `X0` for the United Kingdom (`state_or_country_description`), and the business address matches the Companies House registered office of 11299879. The profile carries no UK company number (`lei` is null), so report CIK 0001973239 and company 11299879 as a possible association on name and address, not as a confirmed match. Firecrawl credits used: 5.

```bash
firecrawl list branddb-wipo-int intellectual-property/search_trademarks --include-costs --pretty
firecrawl scrape branddb-wipo-int/intellectual-property/search_trademarks --options '{"brand_name":"ARM","brand_name_strategy":"Terms","owner":"Arm Limited","rows":10}' --json
```

`total: 247`, 10 returned, `next_cursor: "10"`. The first three, all from the US office:

```text
ARM  Word  Registered  application 75713155 (1999-05-24)  registration 2397339 (2000-10-24)  class 42
     owner: ARM Limited (GB)  https://branddb.wipo.int/en/similarname/brand/US501999075713155
ARM  Stylized characters  Registered  application 86391092 (2014-09-10)  registration 4980649 (2016-06-21)
     classes 41, 9, 38, 16, 37, 35, 45, 42  owner: ARM Limited (GB)
ARM  Word  Registered  application 75713165 (1999-05-24)  registration 2397340 (2000-10-24)  class 9
```

Two of the ten (applications 99313397 and 99313404, filed 2025-07-31) are `Pending`. The owner on these records is ARM Limited (GB). WIPO records carry no company number, and the Companies House PSC data shows Arm Limited (02557590) as a separate company from 11299879, so these marks are matched on owner name only. Report them as marks owned by Arm Limited, not as marks of the subject. Firecrawl credits used: 5.

```bash
firecrawl find-tools --options '{"query":"federal court dockets by party name","limit":5}' --json
firecrawl scrape courtlistener-com/dockets/search_dockets --options '{"party_name":"Arm Ltd","filed_after":"2022-01-01"}' --json
```

Discovery returned `courtlistener-com` `dockets/search_dockets` and `dockets/docket_entries`. This call, a retry with `{"query":"party:(\"Arm Ltd\")"}` and a lookup with `{"docket_id":64938776}` all returned `provider_error` (502) at 0 Firecrawl credits. The public-web fallback found the docket pages:

```bash
firecrawl search "\"Arm Ltd\" docket courtlistener" --limit 5 --json
```

```text
https://www.courtlistener.com/docket/64938776/arm-ltd-v-qualcomm-inc/
  Arm Ltd. v. Qualcomm Inc., 1:22-cv-01146 – CourtListener.com
https://www.courtlistener.com/docket/68447793/qualcomm-incorporated-v-arm-holdings-plc/?page=2
  Qualcomm Incorporated v. ARM Holdings PLC, 1:24-cv-00490
```

The captions name Arm Ltd. and ARM Holdings PLC, and neither page has been tied to a registration number yet. Read each docket page for its court, filing date and parties before listing it, and name CourtListener (Free Law Project's RECAP archive) as the publisher. All five results were pages of these two dockets (two of 64938776, three of 68447793), and the search cost 2 Firecrawl credits.

## Regulations: Firecrawl Government Index

Question: US beneficial ownership reporting requirements.

```bash
firecrawl gov "beneficial ownership information reporting requirements" --jurisdiction us --limit 5 --json
firecrawl scrape firecrawl-gov-index/search --options '{"query":"beneficial ownership information reporting requirements","jurisdiction":"us","top_k":5}' --json
```

Both returned the same five pages in the same order:

```text
https://uscode.house.gov/view.xhtml?edition=prelim&num=0&req=granuleid:USC-prelim-title31-section5336
  §5336. Beneficial ownership information reporting requirements
https://ecfr.gov/current/title-31/subtitle-B/chapter-X/part-1010
  eCFR :: 31 CFR Part 1010 -- General Provisions
https://fincen.gov/boi
  Beneficial Ownership Information Reporting | FinCEN.gov
https://fincen.gov/boi-faqs
https://fincen.gov/beneficial-ownership-information-reporting-rule-fact-sheet
```

The provider results add `jurisdiction: "us"`, `cites` and `updated_at` (August 25 to 30, 2026 for these pages). Take the effective date of a rule from the page itself. Firecrawl credits used: 0 for each call.
