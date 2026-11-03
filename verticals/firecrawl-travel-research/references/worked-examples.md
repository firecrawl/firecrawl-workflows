# Worked Examples

Runs from November 1 and 2, 2026 (UTC) with the Firecrawl CLI 1.26.24. Output is trimmed to the first results; counts and costs are as returned. Prices and availability are snapshots from the retrieval time shown, so rerun before relying on them.

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
          https://www.recreation.gov/camping/campgrounds/232471
272300    JUMBO ROCKS CAMPGROUND      partially available  {"partially available": 5}  119 sites  USD 30/night
          https://www.recreation.gov/camping/campgrounds/272300
232470    SHEEP PASS GROUP            unavailable                                        6 sites  USD 55/night
          https://www.recreation.gov/camping/campgrounds/232470
10056207  Ryan Campground             unavailable                                       30 sites  USD 30/night
          https://www.recreation.gov/camping/campgrounds/10056207
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
firecrawl list vrbo-com vacation-rentals/search --include-costs --pretty
firecrawl scrape vrbo-com/vacation-rentals/locations --options '{"query":"Asheville"}' --json
```

Retrieved 2026-11-01T20:21:01Z. Eight destinations; the first two:

```text
6034866  CITY          Asheville, North Carolina, United States of America
6214697  NEIGHBORHOOD  Downtown Asheville, Asheville, North Carolina, United States of America
```

Firecrawl credits used: 5. The search for the stay:

```bash
firecrawl scrape vrbo-com/vacation-rentals/search --options '{"region_id":"6034866","checkin":"2026-12-11","checkout":"2026-12-14","adults":2,"limit":5}' --json
```

From 20:21:09Z to 20:42:07Z, ten `search` attempts (one of them the contract's example request) and one `offers` attempt returned this error, spaced from 20 seconds to about four minutes apart, at 0 Firecrawl credits each, every one with `retryAfterSeconds: 2`:

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

The same request retried at 21:30:32Z, about 48 minutes after the last failure, returned data, retrieved 2026-11-01T21:30:36Z (`observed_at_ms` 1793568636970): `total_results: 352`, `next_offset: 5`, for a stay of 3 nights. The first three of five properties:

```text
45693503   Colby Loft | Downtown Asheville Loft Across Pritchard Park  Apartment · 2 bedrooms · 3 beds
           USD, $375 per night, "$1,125 for 3 nights", "All fees included"  9.6 Exceptional (180 reviews)
           https://www.vrbo.com/1870445?chkin=2026-12-11&chkout=2026-12-14&adults=2&expediaPropertyId=45693503
135521881  Sweet Biscuit Inn | 2 Homes • Sleeps 26                     House · 10 bedrooms · 14 beds
           USD, $1,354 per night, "$4,063 for 3 nights", "All fees included"  rating "New"
           https://www.vrbo.com/5462098?chkin=2026-12-11&chkout=2026-12-14&adults=2&expediaPropertyId=135521881
19154028   Welcome to the Bamboo Bungalow!                             House · 2 bedrooms · 3 beds
           USD, $287 per night, "$860 for 3 nights", "All fees included"  9.8 Exceptional (154 reviews)
           https://www.vrbo.com/1094069?chkin=2026-12-11&chkout=2026-12-14&adults=2&expediaPropertyId=19154028
```

The search is sorted `RECOMMENDED` (the default), not by price. Firecrawl credits used: 5. A quote for the first property:

```bash
firecrawl scrape vrbo-com/vacation-rentals/offers --options '{"property_id":"45693503","checkin":"2026-12-11","checkout":"2026-12-14","adults":2}' --json
```

Retrieved 2026-11-01T21:30:49Z (`observed_at_ms` 1793568649003):

```text
available: true   instant_book: true   payment_model: PAY_LATER_WITH_DEPOSIT
nightly_price: $375
price_components: BaseRate 786 USD, GenericTax 485.3 USD
total_price: 1271.3 USD
cancellation: Free cancellation before Tue, Nov 10
```

The quote total (USD 1,271.30) is higher than the search's "$1,125 for 3 nights" with "All fees included", so state the quote, show the search figure beside it, and keep both retrieval times. The components (786 + 485.3) add up to the quote total but not to the nightly price times three nights (1,125), so report them as named without deriving a base rate per night. Firecrawl credits used: 5.

## Flights: Google Flights one way

Question: one-way economy fares from San Francisco to Chicago O'Hare for one adult on December 10, 2026.

```bash
firecrawl list flights-google-com --pretty
firecrawl list flights-google-com flights/search_flights --include-costs --pretty
firecrawl scrape flights-google-com/flights/search_flights --options '{"origin":"SFO","destination":"ORD","departure_date":"2026-12-10","adults":1,"limit":5}' --json
```

Retrieved 2026-11-02T10:55:47Z (`observed_at_ms` 1793616947168). `stage: one_way`, `price_scope: one_way_total`, `available_results: 21`, `returned: 5`, `truncated: true`, `skipped_results: 0`, no `notices`. The five rows in the order returned, times local:

```text
USD 149  nonstop  AA 3137                    SFO 13:54 -> ORD 20:20           266 min  carry-on 1, checked 0
USD 149  nonstop  UA 1147                    SFO 15:00 -> ORD 21:15           255 min  carry-on 0, checked 0
USD 149  nonstop  AA 2436                    SFO 15:15 -> ORD 21:38           263 min  carry-on 1, checked 0
USD 113  1 stop   F9 4402 + F9 2146 via LAS  SFO 18:30 -> ORD 05:01 (Dec 11)  511 min  carry-on 0, checked 0
USD 129  1 stop   F9 2930 + F9 1342 via DFW  SFO 10:11 -> ORD 19:48           457 min  carry-on 0, checked 0
search_url: https://www.google.com/travel/flights?tfs=CBwQAhoeEgoyMDI2LTEyLTEwagcIARIDU0ZPcgcIARIDT1JEQAFIAXABmAEC&hl=en&gl=US&curr=USD
```

The rows follow Google's ranking (`group: top` for the three nonstops, `other` for the two Frontier rows), not price, and the lowest fare arrives at 05:01 the next day. No bags were requested and `price_with_requested_bags` is null on every row; `bags_included` differs between fares of the same amount. `limit: 5` keeps the example short; for the cheapest fare on a date, use `limit: 50` at the same cost. Firecrawl credits used: 5 (the contract reads cost 0).

## Flights: Google Flights round trip

Question: a round trip from New York JFK to London Heathrow for two adults in economy, January 20 to 27, 2027.

```bash
firecrawl scrape flights-google-com/flights/search_flights --options '{"origin":"JFK","destination":"LHR","departure_date":"2027-01-20","return_date":"2027-01-27","adults":2,"limit":5}' --json
```

Retrieved 2026-11-02T10:55:54Z (`observed_at_ms` 1793616954321). `stage: outbound`, `price_scope: round_trip_from`, `available_results: 19`, `returned: 5`, `truncated: true`, `skipped_results: 0`, no `notices`:

```text
USD 1506  nonstop  VS 10   JFK 21:10 -> LHR 09:25 (Jan 21)  435 min
USD 1506  nonstop  B6 7    JFK 21:10 -> LHR 09:30 (Jan 21)  440 min
USD 1506  nonstop  DL 3    JFK 22:30 -> LHR 10:30 (Jan 21)  420 min
USD 1506  nonstop  BA 182  JFK 23:20 -> LHR 11:10 (Jan 21)  410 min
USD 1506  nonstop  AA 104  JFK 23:50 -> LHR 12:05 (Jan 21)  435 min
search_url: https://www.google.com/travel/flights?tfs=CBwQAhoeEgoyMDI3LTAxLTIwagcIARIDSkZLcgcIARIDTEhSGh4SCjIwMjctMDEtMjdqBwgBEgNMSFJyBwgBEgNKRktAAUABSAFwAZgBAQ&hl=en&gl=US&curr=USD
```

Return flights for BA 182, with its `selection` copied into `selected_outbound` and the other options unchanged:

```bash
firecrawl scrape flights-google-com/flights/search_flights --options '{"origin":"JFK","destination":"LHR","departure_date":"2027-01-20","return_date":"2027-01-27","adults":2,"limit":5,"selected_outbound":[{"airline":"BA","departure_date":"2027-01-20","destination":"LHR","flight_number":"182","origin":"JFK"}]}' --json
```

Retrieved 2026-11-02T10:56:11Z (`observed_at_ms` 1793616971594). `stage: return`, `price_scope: round_trip_total`, `available_results: 19`, `returned: 5`, `truncated: true`, `skipped_results: 0`, no `notices`:

```text
USD 1506  nonstop  B6 20                      LHR 12:20 -> JFK 15:52  512 min  carry-on null, checked null
USD 1693  nonstop  AA 141                     LHR 17:00 -> JFK 20:05  485 min  carry-on 2, checked null
USD 1693  nonstop  BA 183                     LHR 19:20 -> JFK 22:25  485 min  carry-on 2, checked null
USD 1506  nonstop  B6 2220                    LHR 07:25 -> JFK 10:50  505 min  carry-on null, checked null
USD 1610  1 stop   IB 716 + IB 325 via MAD    LHR 11:25 -> JFK 19:15  770 min  carry-on 2, checked null
search_url: https://www.google.com/travel/flights?tfs=CBwQAho_EgoyMDI3LTAxLTIwIh8KA0pGSxIKMjAyNy0wMS0yMBoDTEhSKgJCQTIDMTgyagcIARIDSkZLcgcIARIDTEhSGh4SCjIwMjctMDEtMjdqBwgBEgNMSFJyBwgBEgNKRktAAUABSAFwAZgBAQ&hl=en&gl=US&curr=USD
```

The outbound USD 1,506 matched the cheapest returns in this list; returning on BA 183 made the pair USD 1,693. The query had `adults: 2` and the response gives no per-person price, so report the amount with the party size. `bags_included` was null for both `carry_on` and `checked` on all five outbound rows; on the return rows `checked` stayed null and three rows showed `carry_on: 2`. No bags were requested and `price_with_requested_bags` was null throughout. Firecrawl credits used: 10 (5 per search).

## Flights: `skyscanner-net` not reachable

Question: daily fares for a route over a month.

```bash
firecrawl list skyscanner-net --pretty
firecrawl list skyscanner-net flights/price_calendar --include-costs --pretty
firecrawl list skyscanner-net flights/monthly_prices --include-costs --pretty
firecrawl find-tools --options '{"providers":["skyscanner-net"],"level":"tools","limit":20}' --json
firecrawl scrape skyscanner-net/flights/price_calendar --options '{"origin":"LHR","destination":"JFK"}' --json
firecrawl scrape skyscanner-net/flights/monthly_prices --options '{}' --json
```

The four capability-level calls used guessed capability names and test options, kept here to show the errors; no contract confirmed them, so do not reuse them. Check the provider with the first command instead. Results:

```text
list provider            invalid_option        Unknown or unavailable categories. ...
list price_calendar       invalid_option        Unknown or unavailable categories. Browse the catalogue for accessible IDs; use query for natural-language search.
list monthly_prices       invalid_option        Unknown or unavailable categories. ...
find-tools (scoped)       invalid_option        Unknown or unavailable providers. ...
scrape price_calendar     unknown_capability    404  Unknown capability "flights/price_calendar" on skyscanner-net.
scrape monthly_prices     unknown_capability    404  Unknown capability "flights/monthly_prices" on skyscanner-net.
```

The provider did not appear in the full provider listing for this key (`firecrawl list --providers --limit 100`, following each page through all 458 providers; rechecked on November 3, 2026). All six calls cost 0 Firecrawl credits. Record the codes as a gap. `flights-google-com` (above) prices one departure date per call, so a month of daily fares stays a gap; run the dates the user names or use the public-web fallback, and never quote a fare from memory.
