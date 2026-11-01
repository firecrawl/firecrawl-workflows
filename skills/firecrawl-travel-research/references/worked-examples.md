# Worked Examples

Runs from November 1, 2026 (UTC) with the Firecrawl CLI 1.26.24. Output is trimmed to the first results; counts and costs are as returned. Prices and availability are snapshots from the retrieval time shown, so rerun before relying on them.

## Campsites: Recreation.gov

Question: campgrounds in Joshua Tree National Park with a site open for the nights of December 11 and 12, 2026 (arrive December 11, leave December 13).

```bash
firecrawl search "campsite availability recreation.gov" --sources alexandria --json
firecrawl list recreation-gov camping-availability/search --include-costs --pretty
firecrawl scrape recreation-gov/camping-availability/search --options '{"query":"Joshua Tree National Park","start_date":"2026-12-11","end_date":"2026-12-13","size":5}' --json
```

Retrieved 2026-11-01T20:21:01Z (`observed_at_ms` 1793564461290). `total: 5`, `next_start: null`. All five are National Park Service campgrounds under Joshua Tree National Park:

```text
272299    COTTONWOOD CAMPGROUND (CA)  available            {"available": 6}             59 sites  USD 35/night
          https://www.recreation.gov/camping/campgrounds/272299
232471    COTTONWOOD GROUP            unavailable                                        3 sites  USD 55/night
272300    JUMBO ROCKS CAMPGROUND      partially available  {"partially available": 5}  119 sites  USD 30/night
          https://www.recreation.gov/camping/campgrounds/272300
232470    SHEEP PASS GROUP            unavailable                                        6 sites  USD 55/night
10056207  Ryan Campground             unavailable                                       30 sites  USD 30/night
```

The price is `price_range` (`amount_min` and `amount_max` with `currency: "USD"` and `per_unit: "night"`), the listed nightly fee, not a quote. Cottonwood's own description text says "There are 62 sites" while `campsites_count` is 59, so report the count the call returned and note the difference. Firecrawl credits used: 5 (discovery and the contract read cost 0).

Site-level nights for Cottonwood in December:

```bash
firecrawl scrape recreation-gov/camping-availability/campground_availability --options '{"campground_id":"272299","month":"2026-12"}' --json
```

Retrieved 2026-11-01T20:22:37Z. `campsite_count: 59`, `returned_count: 59`. The `daily_counts` for the two nights:

```text
2026-12-11  available 39  reserved 20  closed 0  not_yet_released 0
2026-12-12  available  6  reserved 53  closed 0  not_yet_released 0
```

Sites with `Available` on both nights: A10, A14, A21, B09, B14 and B22, six sites, which matches `availability_counts` from the search. The first site shows why per-night counts are not enough: site A01 is `Available` on December 11 and `Reserved` on December 12. Firecrawl credits used: 5.

## Hotels: Trip.com

Question: hotels in London with their room types.

```bash
firecrawl list trip-com hotels/hotels --include-costs --pretty
firecrawl scrape trip-com/hotels/hotels --options '{"url":"https://www.trip.com/hotels/london-hotels-list-338/"}' --json
```

Retrieved 2026-11-01T20:21:56Z. Nine records, `complete: false`, with the warning "Published directory/profile content only. No date-specific availability, offers, tax-inclusive totals or booking. Room types do not establish vacancy. Source marketing prices are omitted." The first three:

```text
Park Grand Heathrow ★★★★    8.2  (745 reviews)
  https://www.trip.com/hotels/london-hotel-detail-3037865/park-grand-heathrow/
Royal National Hotel ★★★   8.2  (3,333 reviews)
  https://www.trip.com/hotels/london-hotel-detail-2985758/royal-national-hotel-london/
President Hotel ★★★        8.6  (1,051 reviews)
  https://www.trip.com/hotels/london-hotel-detail-21875469/the-president-hotel/
```

Every record had `availability: null`. Firecrawl credits used: 5.

Room types for the second hotel:

```bash
firecrawl scrape trip-com/hotels/rooms --options '{"url":"https://www.trip.com/hotels/london-hotel-detail-2985758/royal-national-hotel-london/"}' --json
```

Retrieved 2026-11-01T20:22:06Z. Four rooms, each with `price: null` and `availability: null`:

```text
507559297  Standard Plus Family Room  1 Single bed and 1 Double bed
96366730   Family Room                1 Single bed and 1 Double bed
328535041  Standard Plus Double Room  1 Double bed
328536850  Standard Plus Triple Room  3 Single bed
```

Firecrawl credits used: 5. No hotel rate came back, so a deliverable lists these hotels without prices and records the missing rates in Gaps And Limits.

## Vacation rentals: Vrbo

Question: rentals in Asheville, North Carolina for two adults, December 11 to 14, 2026.

```bash
firecrawl find-tools --options '{"query":"vacation rentals","limit":5}' --json
firecrawl scrape vrbo-com/vacation-rentals/locations --options '{"query":"Asheville"}' --json
```

Retrieved 2026-11-01T20:21:01Z. Eight destinations; the first two:

```text
6034866  CITY          Asheville, North Carolina, United States of America
6214697  NEIGHBORHOOD  Downtown Asheville, Asheville, North Carolina, United States of America
```

Firecrawl credits used: 5. The search for the stay failed:

```bash
firecrawl scrape vrbo-com/vacation-rentals/search --options '{"region_id":"6034866","checkin":"2026-12-11","checkout":"2026-12-14","adults":2,"limit":5}' --json
```

```json
{
  "provider": "vrbo-com",
  "capability": "vacation-rentals/search",
  "error": {
    "code": "provider_rate_limited",
    "message": "The data source is rate-limiting requests. Retry after the Retry-After interval.",
    "status": 429,
    "retryAfterSeconds": 2
  }
}
```

The same error came back on ten `search` attempts between 20:21:09Z and 20:42:07Z, spaced from a few seconds to about four minutes apart (one of them the contract's example request, `{"region_id":"178234","limit":5}`), and on one `offers` attempt with the contract's example `property_id` (`96798806`) for the same stay. Each failed call cost 0 Firecrawl credits. A deliverable for this question reports no rental prices, records `provider_rate_limited` in Gaps And Limits and moves to the public-web fallback.

## Flights: `skyscanner-net` not reachable

Question: daily fares for a route over a month. The capability names below are the ones this workflow targets; no contract confirmed them, and the scrape options were a test, not options from a contract.

```bash
firecrawl list skyscanner-net flights/price_calendar --include-costs --pretty
firecrawl list skyscanner-net flights/monthly_prices --include-costs --pretty
firecrawl scrape skyscanner-net/flights/price_calendar --options '{"origin":"LHR","destination":"JFK"}' --json
firecrawl scrape skyscanner-net/flights/monthly_prices --options '{}' --json
```

Both contract reads returned `invalid_option` with "Unknown or unavailable categories. Browse the catalogue for accessible IDs; use query for natural-language search." A Find Tools call scoped to `"providers":["skyscanner-net"]` returned the same code with "Unknown or unavailable providers", and both scrapes returned `unknown_capability` (404) with messages such as `Unknown capability "flights/price_calendar" on skyscanner-net.` The provider did not appear in the full provider listing for this key (`firecrawl list --providers`, every page). All cost 0 Firecrawl credits. Record the codes as a gap and do not quote fares. A discovery query for flights (`firecrawl find-tools --options '{"query":"skyscanner flights","limit":10}' --json`) ranked a different flight search tool first; check it with `firecrawl list` before using it, as in the Collection Plan.

## Cost summary

| Run                                       | Calls that returned data | Failed calls                               | Firecrawl credits |
| ----------------------------------------- | ------------------------ | ------------------------------------------ | ----------------- |
| Recreation.gov search and December nights | 2                        | 0                                          | 10                |
| Trip.com directory and rooms              | 2                        | 0                                          | 10                |
| Vrbo locations, search and offers         | 1                        | 11 (`provider_rate_limited`)               | 5                 |
| `skyscanner-net` flight tools             | 0                        | 5 (`invalid_option`, `unknown_capability`) | 0                 |
