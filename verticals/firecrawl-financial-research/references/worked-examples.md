# Worked Examples

Runs from November 1, 2026 (UTC) with the Firecrawl CLI 1.26.24. Output is trimmed to the first results; counts and costs are as returned. Figures change as companies file and markets trade, so rerun before relying on them. No provider call in these runs returned `data.alexandria[0].error`, and none needed terms acceptance on the test key; another key can differ.

## Company financials: Fiscal.ai checked against SEC XBRL facts

Question: Apple's reported annual revenue and net income for recent fiscal years, with revenue checked against the SEC's XBRL facts.

```bash
firecrawl search "company income statement and segment financials" --sources alexandria --json
firecrawl scrape fiscal-ai/company/list --options '{"compact":true}' --json
firecrawl list fiscal-ai financials/income-statement --include-costs --pretty
firecrawl scrape fiscal-ai/financials/income-statement --options '{"companyKey":"NASDAQ_AAPL","periodType":"annual"}' --json
```

Discovery surfaced `fiscal-ai` `company/segments-and-kpis`, `financials/income-statement` and `financials/income-statement-as-reported` first. The free `company/list` page returned `totalCount: 12011` companies over 13 pages; Apple's row gave `companyKey: NASDAQ_AAPL`, `cik: 320193` and its `availableDatasets`, which include `financials` and `segments_and_kpis`.

The income statement returned 17 standardized metrics and 21 annual periods, newest first. The two newest periods, condensed:

```text
FY2025  period ending 2025-09-27  earningsDate 2025-10-30  lastSourceFilingDate 2025-10-31
  Total Revenues        416161000000 USD
  Gross Profit          195201000000 USD
  Operating Profit      133050000000 USD
  Consolidated Net Income  112010000000 USD
  source: https://www.sec.gov/Archives/edgar/data/320193/000032019325000079/aapl-20250927.htm (page 32)
FY2024  period ending 2024-09-28  earningsDate 2024-10-31  lastSourceFilingDate 2025-10-31
  Total Revenues        391035000000 USD
```

The response labels these four totals `"unit": "M"`, but the values are whole US dollars: the revenue matches the SEC value below to the dollar, and only revenue was checked against the SEC in this run. Every period came back with `isRestated: true`, and FY2024's `lastSourceFilingDate` is the later 10-K. Each of them carries `asReportedValues` with the company's own line item (such as "Total Net Sales" for revenue) and the source filing and page. Not every metric does: diluted EPS for FY2025 came back with no `asReportedValues`, so its source has to come from the filing. The same figure straight from the SEC:

```bash
firecrawl find-tools --options '{"query":"SEC XBRL company facts revenue","limit":5}' --json
firecrawl scrape sec-gov/filings/concept --options '{"cik":320193,"concept":"RevenueFromContractWithCustomerExcludingAssessedTax","form":"10-K","unit":"USD","limit":6}' --json
```

`total_facts: 37`, 6 returned (the second column is the `fiscal_year` of the filing that reported the fact, not of the period), from `https://data.sec.gov/api/xbrl/companyconcept/CIK0000320193/us-gaap/RevenueFromContractWithCustomerExcludingAssessedTax.json`:

```text
2024-09-29 to 2025-09-27  FY2025  10-K  filed 2025-10-31  0000320193-25-000079  416161000000
2023-10-01 to 2024-09-28  FY2025  10-K  filed 2025-10-31  0000320193-25-000079  391035000000
2023-10-01 to 2024-09-28  FY2024  10-K  filed 2024-11-01  0000320193-24-000123  391035000000
2022-09-25 to 2023-09-30  FY2025  10-K  filed 2025-10-31  0000320193-25-000079  383285000000
```

Both sources gave 416161000000 USD for the fiscal year ending 2025-09-27, from accession 0000320193-25-000079. The SEC facts repeat each year across later 10-Ks, so dedupe on the period. The newest period in both responses ended 2025-09-27; check `filings/filings` for a later 10-K before calling it the latest. Firecrawl credits used: 15 for the Fiscal.ai statement and 5 for the SEC facts (discovery, `company/list` and the contract read cost 0).

## Company announcements: Benzinga press releases

Question: Apple press releases in October 2026.

```bash
firecrawl list benzinga news/press-releases --include-costs --pretty
firecrawl scrape benzinga/calendar/removed --options '{"type":"ratings","pageSize":5}' --json
firecrawl scrape benzinga/news/press-releases --options '{"tickers":"AAPL","dateFrom":"2026-10-01","dateTo":"2026-11-01","pageSize":5,"displayOutput":"headline"}' --json
```

The free `calendar/removed` call returned 5 records with no `error`, which confirms the key can reach Benzinga before spending Firecrawl credits. The Benzinga tools listed no earnings or economic calendar, so earnings dates come from `fiscal-ai` `events/calendar`. The press release call returned 5 releases, `"attribution": "Powered by Benzinga Data"`, with no total. With no `sort` set they came back oldest first (October 3 to October 13), so this is the first page of the month, not all of it. The first three:

```text
PRNewswire     Sat, 03 Oct 2026 07:30:00 -0400  tickers IBKR, XYZ, AAPL
  Online Trading Platform Market Projected to Reach $18.18 Billion by 2031 as Mobile Becomes the Front Door for Retail Investors
  https://www.benzinga.com/pressreleases/26/10/n62151276/online-trading-platform-market-projected-reach-18-18-billion-2031-mobile-becomes-front-door-retail-i
Business Wire  Tue, 13 Oct 2026 08:56:00 -0400  tickers AAPL
  Meet Apple HomeView, a new way to enjoy entertainment, stay connected, and manage the home
  https://www.benzinga.com/pressreleases/26/10/b62322307/meet-apple-homeview-new-way-enjoy-entertainment-stay-connected-and-manage-home
Business Wire  Tue, 13 Oct 2026 08:57:00 -0400  tickers AAPL
  Apple unveils the new Apple TV 4K, supercharged by Siri AI
  https://www.benzinga.com/pressreleases/26/10/b62322335/apple-unveils-new-apple-tv-4k-supercharged-siri-ai
```

The first release is a market report from another issuer that lists AAPL among three tickers, so `tickers` matches mentions; use `primaryTickers` to keep releases whose primary subject is the company. Firecrawl credits used: 15 (`calendar/removed` cost 0).

## Economic series: BLS consumer price index

Question: the US CPI-U all items index from January 2025.

```bash
firecrawl find-tools --options '{"query":"consumer price index inflation","limit":5}' --json
firecrawl list bls-gov economic-statistics/bls_cpi --include-costs --pretty
firecrawl scrape bls-gov/economic-statistics/bls_cpi --options '{"category":"all_items","start_year":2025,"end_year":2026}' --json
```

Discovery returned `bls-gov` `economic-statistics/bls_cpi` first. The call returned series `CUUR0000SA0` (not seasonally adjusted, U.S. city average), units "index (1982-84=100 unless the item footnotes say otherwise)", `count: 21`, source `https://data.bls.gov/timeseries/CUUR0000SA0`. Selected observations:

```text
2025-09  324.800
2025-10  -        footnote: X: Data unavailable due to the 2025 lapse in appropriations
2025-11  324.122
2026-08  334.980
2026-09  336.940  latest
```

The latest observation is September 2026, and October 2025 has no value. The response gives index levels, not percent changes; show both index values when stating a change. Firecrawl credits used: 5.

## Market prices: Coinbase Exchange and Polymarket

Question: the current BTC-USD price and prediction market prices on the next Federal Reserve rate decisions.

```bash
firecrawl find-tools --options '{"query":"crypto price ticker","limit":5}' --json
firecrawl scrape api-exchange-coinbase-com/markets/get_ticker --options '{"product_id":"BTC-USD"}' --json
firecrawl find-tools --options '{"query":"prediction market odds","limit":5}' --json
firecrawl scrape polymarket/search/query --options '{"q":"Fed interest rate","limit_per_type":5,"events_status":"active"}' --json
```

The ticker, from `https://api.exchange.coinbase.com/products/BTC-USD/ticker`:

```text
BTC-USD  price 77696  bid 77695.99  ask 77696  time 2026-11-01T20:03:09.634776964Z
24h stats: open 77406.89  high 78125.91  low 77283.19  volume 2456.942667
```

Discovery returned `api-exchange-coinbase-com` `markets/get_ticker` first. Prices are decimal strings in the quote currency (USD), exactly as the exchange sends them. For Polymarket, discovery returned `markets/list` first and did not list `search/query` in its top 5, so the contract was read directly. Polymarket search returned `totalResults: 4` active events. The first, "Fed Decision in December?" (slug `fed-decision-in-december-20260729232808632`, end date 2026-12-10T04:59:00Z), with each market's Yes and No share prices, updated 2026-11-01T20:02:10Z:

```text
50+ bps decrease  Yes 0.0035  No 0.9965
25 bps decrease   Yes 0.0055  No 0.9945
No change         Yes 0.275   No 0.725
25 bps increase   Yes 0.705   No 0.295
50+ bps increase  Yes 0.0165  No 0.9835
```

The response has no page URL; the event page is https://polymarket.com/event/fed-decision-in-december-20260729232808632. The event description names the FOMC statement after the December 8-9, 2026 meeting as its resolution source. `outcomePrices` are traded share prices; report them as prices with their time, not as a forecast. Firecrawl credits used: 5 for the ticker; Polymarket and discovery cost 0.
