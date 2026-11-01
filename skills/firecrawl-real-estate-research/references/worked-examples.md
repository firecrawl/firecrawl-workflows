# Worked Examples

Runs from November 1, 2026 (UTC) with the Firecrawl CLI 1.26.24. Output is trimmed to the first results; counts and costs are as returned. Listings, bids and public datasets change, so rerun before relying on them.

## Housing figures for a county: Census ACS

Question: population, housing units, tenure, median gross rent and median home value for Travis County, Texas.

```bash
firecrawl search "US Census housing data by county" --sources alexandria --json
firecrawl list census-gov census/data --include-costs --pretty
firecrawl scrape census-gov/census/geographies --options '{"query":"Travis County, Texas","level":"county","limit":3}' --json
```

One match, with the predicates to pass on:

```text
Travis County, Texas  level: county  geo_id: 0500000US48453
  for: county:453  in: ["state:48"]
```

```bash
firecrawl scrape census-gov/census/data --options '{"dataset":"acs/acs5","vintage":2024,"geography":{"for":"county:453","in":["state:48"]},"variables":["NAME","B01003_001E","B25001_001E","B25003_002E","B25003_003E","B25064_001E","B25077_001E"]}' --json
```

One record, `complete: true`, with values as published (strings) and the variable labels:

```text
Travis County, Texas  acs/acs5 vintage 2024
  B01003_001E  "1330015"  Estimate!!Total                     (table B01003)
  B25001_001E  "618236"   Estimate!!Total                     (table B25001)
  B25003_002E  "304165"   Estimate!!Total:!!Owner occupied
  B25003_003E  "279582"   Estimate!!Total:!!Renter occupied
  B25064_001E  "1744"     Estimate!!Median gross rent         unit: null
  B25077_001E  "523000"   Estimate!!Median value (dollars)    unit: dollars
source_url: https://api.census.gov/data/2024/acs/acs5?get=group(B01003)&ucgid=0500000US48453
source_updated_at: 2025-09-02T17:33:50Z   retrieved_at: 2026-11-01T20:20:45Z
warnings: ["variables span 5 tables; fetched as 5 request(s) and merged by GEO_ID"]
```

The response returns one `source_url` per table in `source_urls`; cite the one for each figure. The `unit` for median gross rent came back null, so the unit comes from the label and table, not from that field. This run asked for estimates only; to get each margin of error, request the same variable with its final `E` replaced by `M` (such as `B25064_001M` and `B25003_002M`). Firecrawl credits used: 5 for the geography and 5 for the data (discovery and the contract read cost 0).

## Auctions in a county: Auction.com

Question: active real estate auctions in Travis County, Texas.

```bash
firecrawl find-tools --options '{"query":"property auction listings","limit":5}' --json
firecrawl list auction-com real-estate-auctions/search_listings --include-costs --pretty
firecrawl scrape auction-com/real-estate-auctions/search_listings --options '{"state":"TX","county":"Travis","limit":5}' --json
```

`total_count: 31`, 5 returned, `next_offset: 5`, `listing_type: active` (the default). The first four, retrieved at about 20:21 UTC:

```text
4509 Hees Ct, Pflugerville, TX 78660   BANK_OWNED  AUCTION_IN_PROGRESS  online
  starting_bid 125000  current_highest_bid 125000 (1 bid)
  start 2026-11-01T13:00:00Z  end 2026-11-03T20:21:40Z
  https://www.auction.com/details/4509-hees-ct-pflugerville-tx-2228766
11908 Hurricane Haze Drive, Austin, TX 78747   BANK_OWNED  AUCTION_IN_PROGRESS  online
  starting_bid 175000  current_highest_bid 331000 (6 bids)
  start 2026-11-01T13:00:00Z  end 2026-11-03T20:25:50Z
  https://www.auction.com/details/11908-hurricane-haze-dr-austin-tx-2228803
16217 Copper Leaf Ln, Leander, TX 78641   BANK_OWNED  AUCTION_IN_PROGRESS  online
  starting_bid 200000  current_highest_bid null (0 bids)
  start 2026-10-31T12:00:00Z  end 2026-11-02T17:12:20Z
  https://www.auction.com/details/16217-copper-leaf-ln-leander-tx-2229601
14408 Nesting Grounds Ct, Elgin, TX 78621   FORECLOSURE  SALE_PENDING  in person
  starting_bid 151740  bidding: null
  start 2026-11-03T16:00:00Z
  https://www.auction.com/details/14408-nesting-grounds-ct-elgin-tx-2143928
```

Bids are a snapshot at retrieval time. In-person auctions carry `bidding: null`. Each card also has a `valuation` field (null on the Leander listing), an estimate displayed on the site, so this workflow leaves it out unless asked. One listing reported `bedrooms: 0` for a single-family home; report such values as returned and do not correct them. Firecrawl credits used: 5.

## Records for an NYC address: NYC Open Data

Question: the tax lot record and recorded documents for 1 Centre Street, Manhattan.

```bash
firecrawl find-tools --options '{"query":"NYC property records","limit":5}' --json
firecrawl scrape data-cityofnewyork-us/property-records/resolve_address --options '{"address":"1 Centre St, New York, NY 10007","limit":3}' --json
```

Three candidates. Two are the Manhattan lot; the third is the same street number in the Bronx:

```text
1 CENTER STREET, New York, NY, USA   bbl 1001210001  bin 1001394  Manhattan
1 CENTRE STREET, New York, NY, USA   bbl 1001210001  bin 1001394  Manhattan
1 CENTER STREET, Bronx, NY, USA      bbl 2056270046  bin 2103477  Bronx
```

```bash
firecrawl scrape data-cityofnewyork-us/property-records/property --options '{"bbl":"1001210001"}' --json
```

```text
1 CENTRE STREET 10007  bbl 1001210001  building_class O4  landmark: INDIVIDUAL LANDMARK
  owner_name: NYC DEPARTMENT OF CITYWIDE ADMINISTRATIVE SERVICES  owner_type: C
  year_built 1914  num_floors 39  building_area_sqft 1056600  zoning_district_1 C6-4
  assessed_total_usd 94114800  exempt_total_usd 94114800
  pluto_version 26v2  data_last_modified: Mon, 24 Aug 2026 20:20:51 GMT
  source_url: https://data.cityofnewyork.us/resource/64uk-42ks.json?bbl=1001210001
```

```bash
firecrawl scrape data-cityofnewyork-us/property-records/acris_documents --options '{"bbl":"1001210001","limit":5}' --json
```

`count: 2`, `next_cursor: null`, `data_last_modified: Thu, 08 Oct 2026 20:07:34 GMT`. Both have `FT_` ids, which the contract describes as microfilm documents, and a null `document_date`; the recorded dates are 1966 and 1967:

```text
FT_1330008668033  CTOR  recorded 1966-12-14  amount 0
  https://a836-acris.nyc.gov/DS/DocumentSearch/DocumentDetail?doc_id=FT_1330008668033
FT_1220008672722  MISC  recorded 1967-04-05  amount 0
  https://a836-acris.nyc.gov/DS/DocumentSearch/DocumentDetail?doc_id=FT_1220008672722
```

A BBL that the city data does not hold returns an error inside a successful response, at 0 Firecrawl credits:

```bash
firecrawl scrape data-cityofnewyork-us/property-records/property --options '{"bbl":"1999999999"}' --json
```

```text
"error": {"code": "not_found", "message": "The workflow could not find that resource.", "status": 404}
```

Firecrawl credits used: 5 for the address, 5 for the property record, 5 for the documents and 0 for the `not_found` call.

## Rentals in a Swedish city: Qasa

Question: current rental listings in Göteborg.

```bash
firecrawl find-tools --options '{"query":"rental listings apartments","limit":5}' --json
firecrawl list qasa real-estate/rental_search --include-costs --pretty
firecrawl scrape qasa/real-estate/rental_search --options '{"city":"Göteborg","limit":5}' --json
```

`total_count: 1068`, 5 returned, `has_next_page: true`. The first three, in SEK per month:

```text
1497912  cottage    Surte, Keillers väg            30 m2  1.5 rooms  unfurnished
  rent 7620  tenantBaseFee 453  monthlyCost 8073   start 2027-01-01  published 2026-11-01T20:06:41Z
  https://qasa.com/se/en/home/1497912
1497830  apartment  Hovås, Brända Berget           60 m2  2 rooms    furnished
  rent 16390  tenantBaseFee 975  monthlyCost 17365  start 2027-01-01  published 2026-11-01T19:54:22Z
  https://qasa.com/se/en/home/1497830
1497798  apartment  Sävedalen, Blankavägen         45 m2  2 rooms    furnished
  rent 14000  tenantBaseFee 833  monthlyCost 14833  start 2026-11-23  published 2026-11-01T19:47:18Z
  https://qasa.com/se/en/home/1497798
```

The Göteborg search area returned listings in surrounding localities (Surte, Hovås, Sävedalen), so give each listing's locality. In all five, `monthlyCost` equals `rent` plus `tenantBaseFee`.

The response's `next_request` holds the next page's options with null fields. Passing it back unchanged fails at 0 Firecrawl credits, so send the same options with only `page` changed (`{"city":"Göteborg","limit":5,"page":2}`):

```text
"error": {"code": "invalid_option", "message": "real-estate/rental_search: firsthand must be boolean.", "status": 400}
```

Firecrawl credits used: 5 for the search and 0 for the failed page request.

## Gaps from these runs

- Two calls failed at 0 Firecrawl credits: the deliberate `not_found` lookup and the Qasa page request copied from `next_request` (`invalid_option`). No provider asked for terms acceptance.
- The Census run requested estimates without margins of error.
- The ACRIS extract returned only two records with no document date for this city-owned lot; the live City Register at the `acris_url` link is the authoritative source.
- Only the first page of each listing search was read.

Total for these examples: 35 Firecrawl credits.
