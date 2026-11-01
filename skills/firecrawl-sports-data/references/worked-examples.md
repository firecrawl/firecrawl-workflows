# Worked Examples

Runs from November 1, 2026 (20:01 to 20:02 UTC) with the Firecrawl CLI 1.26.24. Output is trimmed to the first results; counts and costs are as returned. Scores, injury designations and prices change during the day, so rerun before relying on them.

## Scores and schedules: ESPN and Baseball Savant

Question: today's NFL scores and schedule, and the MLB schedule for October 31.

```bash
firecrawl find-tools --options '{"query":"ESPN scores and schedules","limit":8}' --json
firecrawl list espn-com sports-data/scoreboard --include-costs --pretty
firecrawl scrape espn-com/sports-data/scoreboard --options '{"sport":"football","league":"nfl","date":"20261101"}' --json
```

Discovery returned `espn-com/sports-data/scoreboard` first. The scoreboard returned `count: 12`, `count_by_status: {"live": 8, "scheduled": 4}`, `week: 8`, observed at 2026-11-01T20:01:21Z. Four of the twelve, as returned:

```text
2026-11-01T18:00Z  BAL @ BUF  BUF 16  BAL 7   14:51 - 3rd             https://www.espn.com/nfl/game?gameId=401873026
2026-11-01T18:00Z  IND @ JAX  JAX 35  IND 0   9:08 - 3rd              https://www.espn.com/nfl/game?gameId=401873028
2026-11-01T21:25Z  KC @ DEN   DEN -   KC -    11/1 - 4:25 PM EST      https://www.espn.com/nfl/game/_/gameId/401873033/chiefs-broncos
2026-11-02T01:20Z  PHI @ WSH  WSH -   PHI -   11/1 - 8:20 PM EST      https://www.espn.com/nfl/game/_/gameId/401873035/eagles-commanders
```

`score` is null for scheduled games (shown as `-`). The live scores were true at the observation time only. Firecrawl credits used: 5.

```bash
firecrawl list baseballsavant-mlb-com baseball-statcast/get_schedule --include-costs --pretty
firecrawl scrape baseballsavant-mlb-com/baseball-statcast/get_schedule --options '{"date":"2026-10-31"}' --json
```

The request for October 31 returned `total_games: 5`, all World Series games, none of them on October 31:

```text
2026-10-23  TB 2  @ MIL 3   Final  World Series  https://baseballsavant.mlb.com/gamefeed?gamePk=849805
2026-10-24  TB 4  @ MIL 5   Final  World Series  https://baseballsavant.mlb.com/gamefeed?gamePk=849802
2026-10-26  MIL 10 @ TB 1   Final  World Series  https://baseballsavant.mlb.com/gamefeed?gamePk=849804
2026-10-27  MIL 3 @ TB 4    Final  World Series  https://baseballsavant.mlb.com/gamefeed?gamePk=849803
2026-10-28  MIL 4 @ TB 1    Final  World Series  https://baseballsavant.mlb.com/gamefeed?gamePk=849799
```

The first column is each game's `official_date`. Report that date, not the date in the request. Firecrawl credits used: 5.

## Injuries: NFL.com, StartWho and Sleeper

Question: the Kansas City Chiefs' injury report before their November 1 game.

```bash
firecrawl search "NFL injury report for a team this week" --sources alexandria --json
firecrawl scrape sleeper-com/fantasy-football-leagues/nfl_state --options '{}' --json
firecrawl scrape nfl-com/sports-league-data/injury_report --options '{"team":"KC"}' --json
firecrawl scrape startwho-com/fantasy-sports-rankings/injury_report --options '{"team":"KC"}' --json
```

The search returned two tools, `nfl-com/sports-league-data/injury_report` and `startwho-com/fantasy-sports-rankings/injury_report`, at 0 Firecrawl credits. Sleeper's `nfl_state` returned `"week": 8, "season": "2026", "season_type": "regular"`, observed at 2026-11-01T20:01:21Z. The response carries no source link, so cite the provider and capability.

The NFL.com report for week 8 returned `count: 12`, `report_date: 2026-10-30` and `source_url: https://www.nfl.com/injuries/league/2026/reg8`. The players with a game designation, as returned:

```text
Mansoor Delane       CB  Shoulder                                 practice DIDNOT, DIDNOT           QUESTIONABLE
Omarr Norman-Lott    DT  Knee                                     practice LIMITED, LIMITED, LIMITED OUT
L'Jarius Sneed       CB  Knee                                     practice DIDNOT, LIMITED, LIMITED  QUESTIONABLE
R Mason Thomas       DE  Not injury related - personal matter     practice DIDNOT                    QUESTIONABLE
```

The other eight players had `injury_status: null`, such as Travis Kelce (TE, Ankle, practice FULL, LIMITED, FULL) and Rashee Rice (WR, Hamstring and Knee, practice LIMITED, LIMITED, FULL).

StartWho returned `count: 5` for KC, all with `status_short: IR`, from `source_url: https://startwho.com/api/injuries` (list updated 2026-11-01T19:47Z):

```text
Tyquan Thornton          WR  Ankle      IR  updated 2026-10-13T18:35Z
Brashard Smith           RB  Hamstring  IR  updated 2026-10-24T18:10Z
Jimmy Holiday            WR  (none)     IR  updated 2026-08-31T17:45Z
John Michael Gyllenborg  TE  Knee       IR  updated 2026-07-28T22:00Z
Jeff Caldwell            WR  Knee       IR  updated 2026-08-25T23:15Z
```

The two lists share no player. NFL.com lists the week's practice report, while StartWho lists skill-position players on injured reserve, so report both with their dates. Firecrawl credits used: 15 (5 per call).

## Prediction markets: Polymarket

Question: the current Polymarket prices for the Chiefs at Broncos game.

```bash
firecrawl find-tools --options '{"query":"polymarket sports prediction markets","limit":8}' --json
firecrawl scrape polymarket/sports/list --options '{}' --json
firecrawl list polymarket search/query --include-costs --pretty
firecrawl scrape polymarket/search/query --options '{"q":"Chiefs vs. Broncos","limit_per_type":3,"events_status":"active"}' --json
```

`sports/list` returned 494 entries; the NFL entry has `primaryTagId: 450` and `resolution: https://www.nfl.com/`. The search returned `totalResults: 2` events. The game event `nfl-kc-den-2026-11-01` (event page built from the slug: https://polymarket.com/event/nfl-kc-den-2026-11-01, updated 2026-11-01T20:00:20Z, `endDate` 2026-11-01T21:25:00Z, the game's start time) held 305 markets across 32 `sportsMarketType` values, including 34 `spreads` and 21 `totals`. Its moneyline market, as returned:

```json
{
  "id": "5603325",
  "question": "Chiefs vs. Broncos",
  "outcomes": "[\"Chiefs\", \"Broncos\"]",
  "outcomePrices": "[\"0.505\", \"0.495\"]",
  "lastTradePrice": 0.51,
  "bestBid": 0.5,
  "bestAsk": 0.51,
  "updatedAt": "2026-11-01T20:00:08.403361Z",
  "sportsMarketType": "moneyline"
}
```

The second event was the season series market (event page built from the slug: https://polymarket.com/event/pro-football-broncos-vspt-chiefs-season-series-winner), with 3 markets. Both pages returned HTTP 200 when checked on the run date. These are market prices at the stated time, reported as data. Firecrawl credits used: 0.

A failed call: `events/by-slug` with a slug that does not exist (`nfl-kc-den-2026-11-99`) returned, inside `"success": true`:

```json
{
  "code": "provider_error",
  "message": "We couldn’t complete this request. Please try again later.",
  "status": 502
}
```

The CLI exited 1 and the call cost 0 Firecrawl credits. Copy slugs from a search result rather than building them.

## Tickets: SeatGeek and StubHub

Question: upcoming Kansas City Chiefs games with ticket listings.

```bash
firecrawl scrape seatgeek-com/events/search_events --options '{"performer_slug":"kansas-city-chiefs","per_page":5,"sort":"datetime_utc.asc"}' --json
firecrawl scrape stubhub-com/events/search --options '{"query":"Kansas City Chiefs"}' --json
```

SeatGeek returned `total: 11`, 5 on this page, `next_page: 2`. The first three, with `availability` as returned (the contract states no currency and does not say whether fees are included):

```text
2026-11-01T21:25 UTC  Kansas City Chiefs at Denver Broncos   lowest 275  median 485  listings 156
  https://seatgeek.com/denver-broncos-tickets/11-1-2026-denver-colorado-empower-field-at-mile-high/nfl/18014230
2026-11-08T18:00 UTC  New York Jets at Kansas City Chiefs    lowest 268  median 449  listings 690
  https://seatgeek.com/kansas-city-chiefs-tickets/11-8-2026-kansas-city-missouri-geha-field-at-arrowhead-stadium/nfl/18014284
2026-11-15T18:00 UTC  Kansas City Chiefs at Atlanta Falcons  lowest 148  median 323  listings 973
  https://seatgeek.com/atlanta-falcons-tickets/11-15-2026-atlanta-georgia-mercedes-benz-stadium/nfl/18016671
```

StubHub returned `total_count: 4805` and 20 events on page 1, in relevance order rather than by date (November 1, then November 15, then November 8). Nine of the 20 are tailgate or add-on events, such as "Premium Tailgate Party: Denver Broncos vs. Kansas City Chiefs", so filter by name. The first three:

```text
Nov 01 2:25 PM   Kansas City Chiefs at Denver Broncos   https://www.stubhub.com/denver-broncos-denver-tickets-11-1-2026/event/160425045/
Nov 15 1:00 PM   Kansas City Chiefs at Atlanta Falcons  https://www.stubhub.com/atlanta-falcons-atlanta-tickets-11-15-2026/event/160436162/
Nov 08 12:00 PM  New York Jets at Kansas City Chiefs    https://www.stubhub.com/kansas-city-chiefs-kansas-city-tickets-11-8-2026/event/160425134/
```

StubHub's `formatted_time` is local to the venue: the Denver game's `starts_at_ms` (1793568300000) is 2026-11-01T21:25:00Z, the same start as ESPN and SeatGeek. Firecrawl credits used: 10.

## Gaps from these runs

- Sleeper league calls (`league`, `league_rosters`, `matchups`) need a user's `league_id` and were not run; only `nfl_state` was.
- `espn-com/sports-data/injuries` and the StubHub `events/listings` call were read as contracts only.
- No provider in these runs asked for terms acceptance. The only failed call was the Polymarket `provider_error` above.
- Firecrawl credits for all runs: 35. Discovery and contract reads cost 0.
