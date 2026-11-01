# Worked Examples

Runs from November 1, 2026 (UTC) with the Firecrawl CLI 1.26.24. Output is trimmed to the first results; counts and costs are as returned. No provider in these runs required terms acceptance on the key used. Prices and listings change by the hour, so rerun before relying on them.

## US prices: Best Buy, Shopify stores and Target

Question: air fryer prices at US stores.

```bash
firecrawl find-tools --options '{"query":"Best Buy product search prices","limit":5}' --json
firecrawl list bestbuy-com products/search --include-costs --pretty
firecrawl scrape bestbuy-com/products/search --options '{"query":"air fryer"}' --json
```

`count: 19`, `has_more: true`, `next_page: 2`, `observed_at_ms: 1793564480775` (2026-11-01 20:21 UTC). The first three, as `sku`, `price.current` and `price.regular` in `price.currency`, rating and review count:

```text
6645806  USD 199.99 (regular 279.99)  4.9 (95)
  Ninja - French Door Premier Air Fryer, Convection Oven, Toaster - Stainless Steel
  https://www.bestbuy.com/product/ninja-french-door-premier-air-fryer-convection-oven-toaster-stainless-steel/JXJVXLJ5SR
6570528  USD 99.99 (regular 179.99)   4.8 (595)
  Ninja - Air Fryer Pro XL 6-in-1 with 6.5 QT Capacity - Gray
  https://www.bestbuy.com/product/ninja-air-fryer-pro-xl-6-in-1-with-6-5-qt-capacity-gray/JXJVXGVTJP
6570527  USD 79.99 (regular 129.99)   4.9 (537)
  Ninja - Air Fryer Pro 4-in-1 with 5 QT Capacity - Gray
  https://www.bestbuy.com/product/ninja-air-fryer-pro-4-in-1-with-5-qt-capacity-gray/JXJVXGVHJT
```

Firecrawl credits used: 5 (discovery and the contract read cost 0).

A generic query found none of these stores. `firecrawl search "compare air fryer prices at US retailers" --sources alexandria --json` returned `doordash-com` (three capabilities), `amazon-com` `products/prices` and `littlecaesars-com` `restaurants/get_deals`, at 0 Firecrawl credits. Name the store in the query, or read the contract of a provider from the table directly.

The Shopify catalog searches across Shopify stores:

```bash
firecrawl scrape shopify/catalog/search_catalog --options '{"query":"air fryer","country":"US","limit":5}' --json
```

5 products returned, `pagination.total_count: 296`, `has_next_page: true`. Prices are in minor units, so `18500` is USD 185.00. The response has no `observed_at_ms`, so the retrieval time is the call time, 2026-11-01 20:21 UTC. The first three, as `price.amount` and `price.currency` from each product's first variant, with `seller.name`:

```text
Wonder Oven                     18500 USD   Our Place
  https://fromourplace.com/products/wonder-oven?variant=43276070682818&_gsid=R3sGQbp38XzE&utm_source=shopify&utm_medium=catalog
Typhur Dome 2 Air Fryer         39999 USD   Typhur
  https://www.typhur.com/products/dome-air-fryer?_gsid=R3sGQbp38XzE&utm_source=shopify&utm_medium=catalog
Tastee Sensor Magic Smart Air Fryer (Ceramic Coated)  19999 USD  Tastee
  https://www.tastee.net/products/tastee-air-fryer?variant=47402497081666&_gsid=R3sGQbp38XzE&utm_source=shopify&utm_medium=catalog
```

The fifth result also carried a list price beside the price:

```text
Granitestone Glass Air Fryer    price 12999 USD, list_price 14999 USD   Granitestone.com
  https://granitestone.com/products/glass-air-fryer-xl?variant=45515969069219&_gsid=R3sGQbp38XzE&utm_source=shopify&utm_medium=catalog
```

That is USD 129.99 against a list price of USD 149.99. The contract describes results as estimates, not an exhaustive merchant inventory. Firecrawl credits used: 5.

Target has no keyword search among its capabilities, so a web search found the product URL first. With an API key, that search also returned the `target-com` tools beside the web results:

```bash
firecrawl search "air fryer site:target.com/p" --limit 5 --json
firecrawl scrape target-com/products/product --options '{"url":"https://www.target.com/p/ninja-air-fryer-pro-4-in-1-af141/-/A-90569368"}' --json
```

```text
id 90569368  brand Ninja  price null  availability null
title: Ninja Pro 4-in-1 Air Fryer Gray: 5-Quart, 1750W, Dishwasher-Safe Parts, Metal &#38; Plastic
specifications: Capacity (Volume): 5 Quart | Model #: AF141 | Wattage Output: 1750 Watts | ...
return_policy: This item must be returned within 90 days of the date it was purchased ...
warnings: US public product data only. ... Prices and availability are unknown; ...
```

Target gave specifications and the return policy but no price, which is a gap to record. The title carries an HTML entity (`&#38;`) as returned. Firecrawl credits used: 2 for the search and 5 for the product.

## Recall check: CPSC on saferproducts.gov

Question: CPSC recalls of air fryers.

```bash
firecrawl find-tools --options '{"query":"CPSC product recalls saferproducts","limit":5}' --json
firecrawl list saferproducts-gov product-recalls/search_recalls --include-costs --pretty
firecrawl scrape saferproducts-gov/product-recalls/search_recalls --options '{"product_name":"air fryer","limit":5}' --json
```

`total_results: 6`, 5 returned, newest first. Each carries the recall number, recall date, last publish date and the cpsc.gov notice URL:

```text
24159  2024-03-14  Best Buy Recalls Insignia® Air Fryers and Air Fryer Ovens Due to Fire, Burn and Laceration Hazards
       https://www.cpsc.gov/Recalls/2024/Best-Buy-Recalls-Insignia-Air Fryers-and-Air-Fryer-Ovens-Due-to-Fire-Burn-and-Laceration-Hazards
24051  2023-12-14  Empower Brands Recalls Power XL Dual Basket Air Fryers Due to Burn Hazard
       https://www.cpsc.gov/Recalls/2024/Empower-Brands-Recalls-Power-XL-Dual-Basket-Air-Fryers-Due-to-Burn-Hazard
23794  2023-09-28  Secura Recalls Air Fryers Due to Fire and Burn Hazards (Recall Alert)
       https://www.cpsc.gov/Recalls/2023/Secura-Recalls-Air-Fryers-Due-to-Fire-and-Burn-Hazards-Recall-Alert
```

The other two:

```text
23130  2023-02-23  Two Million COSORI® Air Fryers Recalled by Atekcity Due to Fire and Burn Hazards
       https://www.cpsc.gov/Recalls/2023/Two-Million-COSORI-Air-Fryers-Recalled-by-Atekcity-Due-to-Fire-and-Burn-Hazards
23012  2022-10-13  Newair Recalls Magic Chef Air Fryers Due to Fire and Burn Hazards
       https://www.cpsc.gov/Recalls/2023/Newair-Recalls-Magic-Chef-Air-Fryers-Due-to-Fire-and-Burn-Hazards
```

The 24159 URL contains a space as returned. A brand in this list is not a recall of every product of that brand: the Shopify search above returned a COSORI TurboBlaze, and only the full notice shows which models a recall names. Firecrawl credits used: 5.

Reading the 24159 notice, with the space in its URL replaced by a hyphen:

```bash
firecrawl scrape "https://www.cpsc.gov/Recalls/2024/Best-Buy-Recalls-Insignia-Air-Fryers-and-Air-Fryer-Ovens-Due-to-Fire-Burn-and-Laceration-Hazards" --json
```

Condensed from the notice (`statusCode: 200`):

```text
Name of Product: Air Fryers and Air Fryer Ovens
Remedy: Refund
Recall Date: March 14, 2024
Units: About 187,400 (In addition, about 99,900 were sold in Canada)
Description: This recall involves Insignia Air Fryers and Insignia Air Fryer Ovens, model numbers
  NS-AF34D2, NS-AF5DSS2, NS-AF5MSS2, NS-AF8DBD2, NS-AF10DBK2, and NS-AF10DSS2. ...
```

The model numbers come from the notice, not from the search result, so compare them with the model of each shortlisted product. Firecrawl credits used: 1.

The same capability with no filter failed, as its contract says it would:

```bash
firecrawl scrape saferproducts-gov/product-recalls/search_recalls --options '{"limit":5}' --json
```

```json
"error": { "code": "invalid_option", "message": "The workflow rejected these input options.", "status": 400 }
```

The top-level response still had `"success": true`, the CLI exited with code 1, and the call cost 0 Firecrawl credits.

## Outside the US: Amazon.nl, Etsy and Alibaba

Question: airfryer prices in the Netherlands, air fryer liners from Etsy sellers, and wholesale air fryer offers.

```bash
firecrawl find-tools --options '{"query":"Amazon Netherlands product search","limit":5}' --json
firecrawl scrape amazon-nl/products/search --options '{"query":"airfryer"}' --json
```

`count: 48`, `has_more: true`, `observed_at_ms: 1793564476781` (2026-11-01 20:21 UTC), none sponsored. The first three:

```text
B0DKKH9H5V  € 85,09 (list € 154,99)  4.7 (1358)  Philips Dubbele Airfryer 1000 7,1L - 2 manden met RapidAir
            https://www.amazon.nl/dp/B0DKKH9H5V
B0D9S9Y16Y  € 87,35 (list € 113,99)  4.7 (497)   Philips Airfryer 3000 6,2L - RapidAir Plus met kijkvenster
            https://www.amazon.nl/dp/B0D9S9Y16Y
B0H9558S91  € 152,62 (no list price) 5 (1)       Ninja CRISPi Airfryer, 3,8 L & 2,3 L, 4-in-1: ...
            https://www.amazon.nl/dp/B0H9558S91
```

Each price also comes as `amount` with `currency: "EUR"`. Firecrawl credits used: 5.

Etsy keyword search is not available through the provider, so a web search found a seed listing, and `similar_listings` fanned out from it:

```bash
firecrawl search "air fryer liner site:etsy.com/listing" --limit 5 --json
firecrawl scrape etsy-com/listings/similar_listings --options '{"listing_id":4526090946,"limit":5}' --json
```

`count: 5`, `currency: "USD"`, `observed_at_ms: 1793564491331` (2026-11-01 20:21 UTC). The first three, with price, sale price and shop:

```text
4526092094  USD 26.67, sale USD 18.67  Perfecshopper
  Air Fryer Liners Disposable 150PCS 8 Inch Square Unbleached Parchment Paper ...
  https://www.etsy.com/listing/4526092094/air-fryer-liners-disposable-150pcs-8
4526080349  USD 21.65, sale USD 15.15  Perfecshopper
  Air Fryer Paper Liners 125PCS 8 Inch Disposable Parchment Sheets ...
  https://www.etsy.com/listing/4526080349/air-fryer-paper-liners-125pcs-8-inch
4562651466  USD 25.39, sale USD 18.22  ShopsphereByMe
  Air Fryer Paper Liners 8 Inch, Pack of 100, Disposable Air Fryer Parchment Sheets, ...
  https://www.etsy.com/listing/4562651466/air-fryer-paper-liners-8-inch-pack-of
```

The contract says these recommendations vary between calls. Firecrawl credits used: 2 for the search and 5 for the listings.

```bash
firecrawl scrape alibaba/retail/search --options '{"query":"air fryer"}' --json
```

40 offers on page 1, `source_total: 99651`, `has_more: true`, `next_cursor: "2"`, `observed_at_ms: 1793564477179` (2026-11-01 20:21 UTC). The first three:

```text
US $42.50-$45.50  MOQ 1,000 pieces  Handinhand (Zhejiang) Electronic Commerce Co., Ltd.  CN
  OEM Factory Custom 10L Capacity Touch Screen Oil Free Dual Basket Air Fryer
  https://www.alibaba.com/product-detail/OEM-Factory-Custom-10L-Capacity-Touch_1601367413203.html
US $85.52-$92.35  MOQ 500 pieces    Wenzhou Demian Enterprise Management Consulting Co., Ltd.  CN
  14L Dual Basket Air Fryer DEMIAND 8L+6L WiFi Large Capacity Electric Air Fryer Black
  https://www.alibaba.com/product-detail/14L-Dual-Basket-Air-Fryer-DEMIAND_12000003222006.html
US $18.90-$21.90  MOQ 1,000 pieces  Handinhand (Zhejiang) Electronic Commerce Co., Ltd.  CN
  Factory Direct Kitchen Nonstick Automatic Air Fryers Wholesale
  https://www.alibaba.com/product-detail/Factory-Direct-Kitchen-Nonstick-Automatic-Air_1601017467319.html
```

`display_currency` was `null` on every record: the price is the supplier's display string for a minimum order, not a retail unit price. Firecrawl credits used: 5.
