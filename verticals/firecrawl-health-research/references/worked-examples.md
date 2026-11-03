# Worked Examples

Runs from November 1, 2026 (UTC and America/Cordoba) with the Firecrawl CLI 1.26.24. Output is trimmed to the first results; counts and costs are as returned. Records change as FDA, ClinicalTrials.gov and PubMed publish updates, so rerun before relying on them. These are source lists for research, not medical advice.

## Drug label: fda-gov records/drug_labels

Question: what the current labeling for semaglutide warns about.

```bash
firecrawl search "FDA adverse event reports for a drug" --sources alexandria --json
firecrawl list fda-gov records/drug_labels --include-costs --pretty
firecrawl scrape fda-gov/records/drug_labels --options '{"query":"openfda.generic_name:semaglutide","limit":3}' --json
```

The first discovery call returned `TOOL_DISCOVERY_UNAVAILABLE` (HTTP 503). The retry a few seconds later returned `fda-gov/records/drug_events`, `drug_labels`, `drug_applications`, `drug_application_documents` and `drug_shortages` in `data.tools[]`, with `creditsUsed: 0`.

The label search returned `total_matching: 5`, 3 records, `next_skip: 3`, dataset `last_updated: 2026-10-30`. Each record has a `record_id`, a `source_url` on api.fda.gov (a JSON API link that returns the record) and the unmodified label in `data`:

```text
6834044a-22f1-4f35-b846-b3448ee84c16  effective 20260130  version 14
  OZEMPIC, RYBELSUS (ORAL SEMAGLUTIDE)  Novo Nordisk Pharmaceutical Industries, LP  NDA213051
  https://api.fda.gov/drug/label.json?search=id%3A%226834044a-22f1-4f35-b846-b3448ee84c16%22&skip=0&limit=1
4b9bdfa7-1075-45cd-baac-66632beb38df  effective 20260730  version 1
  Ozempic (SEMAGLUTIDE)  A-S Medication Solutions  NDA209637
  https://api.fda.gov/drug/label.json?search=id%3A%224b9bdfa7-1075-45cd-baac-66632beb38df%22&skip=0&limit=1
a5c23c93-1c61-4768-b7f2-1e430cd21a8d  effective 20231122  version 3
  Ozempic (SEMAGLUTIDE)  A-S Medication Solutions  NDA209637
  https://api.fda.gov/drug/label.json?search=id%3A%22a5c23c93-1c61-4768-b7f2-1e430cd21a8d%22&skip=0&limit=1
```

All three carry the same boxed warning heading. From the first record's `boxed_warning`:

```text
WARNING: RISK OF THYROID C-CELL TUMORS • In rodents, semaglutide causes dose-dependent and
treatment-duration-dependent thyroid C-cell tumors at clinically relevant exposures. ...
```

The second and third records come from a repackager, and the third has a 2023 effective date. Report the manufacturer, `effective_time` and version of each label rather than treating them as one document. Every response carries the openFDA disclaimer: "Do not rely on openFDA to make decisions regarding medical care." Firecrawl credits used: 5 (discovery and the contract read cost 0).

## Adverse event reports: fda-gov records/drug_events

Question: FAERS reports received in 2025 that list semaglutide and the reaction term pancreatitis.

```bash
firecrawl list fda-gov records/drug_events --include-costs --pretty
firecrawl scrape fda-gov/records/drug_events --options '{"query":"patient.drug.openfda.generic_name:semaglutide AND patient.reaction.reactionmeddrapt:pancreatitis AND receivedate:[20250101 TO 20251231]","limit":3}' --json
```

`total_matching: 357` reports, 3 returned, dataset `last_updated: 2026-07-30` (three months behind the label dataset). The contract states: "A report is not evidence that the drug caused the reaction; FAERS lags the calendar by months." The three reports, with the drugs each lists:

```text
24802185  received 20250103  serious 1  country US
  reactions: Malnutrition, Impaired gastric emptying, Dyspepsia, Nausea, Constipation,
             Abdominal pain, Diarrhoea, Pancreatitis, Product use issue
  drugs: OZEMPIC, TRULICITY, JARDIANCE, ATORVASTATIN, CYCLOBENZAPRINE, METOPROLOL, ...
  https://api.fda.gov/drug/event.json?search=safetyreportid%3A%2224802185%22&skip=0&limit=1
24823724  received 20250109  serious 1  country EU
  reactions: Lactic acidosis, Ketoacidosis, Hypothermia, Pancreatitis
  drugs: DAPAGLIFLOZIN, METFORMIN, OZEMPIC
  https://api.fda.gov/drug/event.json?search=safetyreportid%3A%2224823724%22&skip=0&limit=1
24838077  received 20250113  serious 1  country IN
  reactions: Immunoglobulin G4 related disease, Autoimmune pancreatitis
  drugs: OZEMPIC
  https://api.fda.gov/drug/event.json?search=safetyreportid%3A%2224838077%22&skip=0&limit=1
```

The third report matched on the word "pancreatitis" inside "Autoimmune pancreatitis", so read the reaction terms of every report before counting it. Two reports list several drugs. `drugcharacterization` and `primarysource.qualification` are coded values; decode them from the openFDA field documentation, not from memory. The 357 is a count of reports, not of patients, and not a rate. Each report's `source_url` looks it up by `safetyreportid`. Firecrawl credits used: 5.

## Device recalls: fda-gov records/device_recalls

Question: insulin pump recalls reported since January 2025.

```bash
firecrawl list fda-gov records/device_recalls --include-costs --pretty
firecrawl scrape fda-gov/records/device_recalls --options '{"query":"product_description:\"insulin pump\" AND report_date:[20250101 TO 20261031]","limit":5}' --json
```

`total_matching: 36`, 5 returned, `next_skip: 5`, dataset `last_updated: 2026-10-21`:

```text
Z-1745-2026  Class II  Ongoing  Medtronic MiniMed, Inc.     report 20260408  event 98186
  MiniMed 720G Insulin Pump (MMT-1809, MMT-1810, MMT-1859, MMT-1860, MMT-1867)
  https://api.fda.gov/device/enforcement.json?search=recall_number%3A%22Z-1745-2026%22&skip=0&limit=1
Z-1749-2026  Class II  Ongoing  Medtronic MiniMed, Inc.     report 20260408  event 98186
  Paradigm REAL-Time Insulin Pump (MMT-522, MMT-722)
  https://api.fda.gov/device/enforcement.json?search=recall_number%3A%22Z-1749-2026%22&skip=0&limit=1
Z-1752-2026  Class II  Ongoing  Medtronic MiniMed, Inc.     report 20260408  event 98186
  Paradigm REAL-Time Veo Insulin Pump (MMT-554, MMT-754)
  https://api.fda.gov/device/enforcement.json?search=recall_number%3A%22Z-1752-2026%22&skip=0&limit=1
Z-1265-2025  Class II  Ongoing  Medtronic MiniMed, Inc.     report 20250312  event 96200
  MiniMed  insulin pump,  REF: 770G (MMT-1880, MMT-1881, MMT-1882, MMT-1890, MMT-1891, MMT-1892)
  https://api.fda.gov/device/enforcement.json?search=recall_number%3A%22Z-1265-2025%22&skip=0&limit=1
Z-1519-2025  Class I   Ongoing  Tandem Diabetes Care, Inc.  report 20250423  event 96417
  t:slim X2 Insulin Pump with Interoperable Technology
  https://api.fda.gov/device/enforcement.json?search=recall_number%3A%22Z-1519-2025%22&skip=0&limit=1
```

The first three recall numbers share event 98186 and the same reason, which opens: "All Medtronic MiniMed infusion pumps (Paradigm series, 600 series, and BLE 700 series) were found to be affected by unintended over- and under-delivery of insulin when the pump is elevated or lowered relative to the infusion site ..." Group recall numbers by `event_id` and keep each classification and status as the source gives it. All five are marked "Voluntary: Firm initiated". Firecrawl credits used: 5.

## Trials and the papers that report them: clinicaltrials-gov, PubMed and the Firecrawl Research Index

Question: registered trials of semaglutide in Alzheimer's disease, and the publication that reports the phase 3 results.

```bash
firecrawl find-tools --options '{"query":"clinical trial registry search by condition and drug","limit":5}' --json
firecrawl list clinicaltrials-gov clinical-trials/search --include-costs --pretty
firecrawl scrape clinicaltrials-gov/clinical-trials/search --options '{"intervention":"semaglutide","condition":"Alzheimer Disease","page_size":5}' --json
```

Discovery returned `clinicaltrials-gov/clinical-trials/search` first, plus `clinical-trials/suggest`, `clinical-trials/study_dates` and two registries outside this workflow, at 0 Firecrawl credits. The search returned `total_count: 8`, 5 studies and a `next_page_token`:

```text
NCT05891496  COMPLETED           PHASE3         Novo Nordisk A/S
  primary completion 2024-08-19 ACTUAL     has_results true   last update 2026-08-18
  https://clinicaltrials.gov/study/NCT05891496
NCT07135245  NOT_YET_RECRUITING  PHASE2         Rune Skovgaard Rasmussen
  primary completion 2030-09-30 ESTIMATED  start 2026-01-01 ESTIMATED  last update 2025-08-22
  https://clinicaltrials.gov/study/NCT07135245
NCT07651319  RECRUITING          PHASE1,PHASE2  The Methodist Hospital Research Institute
  primary completion 2029-06 ESTIMATED     last update 2026-09-23
  https://clinicaltrials.gov/study/NCT07651319
NCT07200622  NOT_YET_RECRUITING  PHASE2         Imperial College London
  primary completion 2027-12-31 ESTIMATED  last update 2025-10-01
  https://clinicaltrials.gov/study/NCT07200622
NCT07677865  WITHDRAWN                          West China Hospital
  start 2007-01-01 ACTUAL  primary completion 2024-12-31 ACTUAL  last update 2026-10-16
  why_stopped: poor accrual and funding issues
  https://clinicaltrials.gov/study/NCT07677865
```

NCT07135245 still lists an estimated start of 2026-01-01 and has not been updated since 2025-08-22. NCT07200622 lists an estimated start of 2025-09-25 and was last updated 2025-10-01. Report each status as the registry shows it on the search date, and note the passed estimated starts. The full record for the first study:

```bash
firecrawl scrape clinicaltrials-gov/clinical-trials/study --options '{"nct_id":"NCT05891496"}' --json
```

It returned the protocol without the results section (set `include_results` for posted results), `resultsFirstPostDateStruct` 2026-02-24, enrollment 23 ACTUAL, a study protocol and a statistical analysis plan in `documentSection`, and no `referencesModule`. The arm descriptions include dosing; quote them only as the protocol states them.

Looking for a paper that cites the NCT number:

```bash
firecrawl scrape pubmed-ncbi-nlm-nih-gov/publications/search --options '{"query":"NCT05891496","limit":5}' --json
```

`total_count: 0` and the message "No items found." at 5 Firecrawl credits (`source_url` https://pubmed.ncbi.nlm.nih.gov/?term=NCT05891496). No hit for an NCT number does not mean the trial is unpublished. A topic search in the Firecrawl Research Index costs 0 Firecrawl credits:

```bash
firecrawl scrape firecrawl-research-index/search --options '{"query":"semaglutide Alzheimer disease randomized trial","k":5,"from":"2023-01-01"}' --json
```

Five papers, the first:

```text
paperId 8812597353777838423  pmid:41865758  doi 10.1016/S0140-6736(26)00459-9
  Efficacy and safety of oral semaglutide 14 mg (flexible dose) in early-stage symptomatic
  Alzheimer's disease (evoke and evoke+): two phase 3, randomised, placebo-controlled trials.
```

The first `inspect` call passed `id` instead of `paperId` and returned `{"code":"missing_option","message":"inspect requires paperId.","status":400}` at 0 Firecrawl credits. With `paperId` it returned the authors, MeSH-style `categories`, `createdDate` 2026-05-30 and `updateDate` 2026-10-02. The contract does not define those two dates, so the publication date below comes from PubMed.

```bash
firecrawl scrape firecrawl-research-index/inspect --options '{"paperId":"8812597353777838423"}' --json
firecrawl scrape firecrawl-research-index/read --options '{"paperId":"8812597353777838423","query":"ClinicalTrials.gov registration numbers NCT","k":3}' --json
```

`read` returned `passages: []`; the contract notes that empty passages do not mean the full paper was read. The PubMed record holds the publication date and the full abstract:

```bash
firecrawl scrape pubmed-ncbi-nlm-nih-gov/publications/article --options '{"pmid":"41865758"}' --json
```

```text
Lancet 407(10544):2167-2179  pub_date 2026 May 30  ppublish
publication_types: "Journal Article", "Randomized Controlled Trial", "Clinical Trial, Phase III", ...
https://pubmed.ncbi.nlm.nih.gov/41865758/
METHODS: "... These trials were registered at ClinicalTrials.gov (NCT04777396 and
NCT04777409); both trials have been discontinued due to negative clinical outcome."
```

Those two trials were not on the first page of the registry search, and this paper does not report NCT05891496, so no paper was linked to that trial in this run. The PubMed record's `url` is the link to cite. Registry status of the two trials:

```bash
firecrawl scrape clinicaltrials-gov/clinical-trials/study_dates --options '{"nct_ids":["NCT04777396","NCT04777409"]}' --json
```

```text
NCT04777396  COMPLETED  start 2021-05-18 ACTUAL  primary completion 2025-09-16 ACTUAL
  completion 2026-01-30 ACTUAL  why_stopped null  last update 2026-07-07
  https://clinicaltrials.gov/study/NCT04777396
NCT04777409  COMPLETED  start 2021-05-18 ACTUAL  primary completion 2025-09-15 ACTUAL
  completion 2026-01-30 ACTUAL  why_stopped null  last update 2026-06-26
  https://clinicaltrials.gov/study/NCT04777409
```

`found_count: 2`, `not_found_ids: []`. The paper says the trials were discontinued, and the registry lists both as completed with no `why_stopped`. Put both statements under Discrepancies, each attributed to its source. Firecrawl credits used: 25 (5 each for the registry search, the study record, the PubMed search, the PubMed article and the study dates; the Research Index calls cost 0).

## Gaps hit in these runs

- One `TOOL_DISCOVERY_UNAVAILABLE` (HTTP 503) on `firecrawl search --sources alexandria`; the retry worked.
- One `missing_option` (HTTP 400) on `firecrawl-research-index/inspect` from an unlisted option name, at 0 Firecrawl credits.
- PubMed returned no records for the NCT number NCT05891496.
- No provider in these runs asked for terms acceptance.

Total for the four examples: 40 Firecrawl credits.
