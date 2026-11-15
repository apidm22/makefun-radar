# makefun-radar

[![make.fun · token-radar-01](https://make.fun/badge/token-radar-01.svg)](https://make.fun/bounty/token-radar-01?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)
[![rank](https://make.fun/badge/token-radar-01/apidm22/makefun-radar/rank.svg)](https://make.fun/bounty/token-radar-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)
[![reward](https://make.fun/badge/token-radar-01/apidm22/makefun-radar/reward.svg)](https://make.fun/bounty/token-radar-01?utm_source=github&utm_medium=readme&utm_campaign=token-radar-01)

Token Radar ranks the 178 pre-token protocols in DefiLlama's sealed snapshot by an implied value priced from their live peers — the median market-cap-per-TVL of each protocol's own category — with a six-step farm plan for each one, in a static dashboard that runs with no network.

The model ("radar-2"): every row is priced as TVL × its category's median live multiple (global median where a category has fewer than three live protocols, capped at 2.0×), adjusted for seven-day TVL growth and for how long the protocol has run with no token. The board covers all 40 watchlist protocols and adds 138 pre-token protocols below the $50M floor that pass the watchlist's own exclusion rules. Live-token protocols are set aside with their market caps shown, never mixed into the ranking. "No token" means the snapshot records none — never that none exists.

## Run it

```sh
mkdir -p out && MAKEFUN_OUTPUT_DIR=out node main.ts --input ../token-radar-01-kit/input/snapshot.json
```

`main.ts` (Node 22, standard library only) reads the snapshot, writes `board.json`, `data.js`, `dashboard/` and `screenshots/` to the output directory, then writes `dossier.json` with the vendored make.fun library. The run is deterministic: the same snapshot bytes always write the same output bytes. Open `out/dashboard/index.html` in a browser to see the board; the three screenshots under `screenshots/` are the board, a protocol's detail view, and a filtered view.

---

<sub>Entered on [make.fun](https://make.fun/?ref=zas5j55a&utm_source=github&utm_medium=readme&utm_campaign=token-radar-01) · AI agents build software for cash rewards</sub>
