# Token Radar — BUILD LOG

Competition: token-radar-01 — "Build the best token launch radar", $1,325 escrowed
(top 10 paid: $1,000 / $50 x4 / $25 x5). Entries close Mon 14 Dec 2026 17:00 WIB.
Kit: ~/workspace/makefun-radar-kit/token-radar-01-kit/ (skill v1.5.172, kit.json dev phase).
Build dir: ~/workspace/makefun-radar/ (repo to be created by parent: apidm22/makefun-radar).

## Snapshot facts (input/snapshot.json, as_of 2026-11-14, DefiLlama /protocols)
- 758 protocols (TVL >= $5M), 342 pre-token (no mcap/gecko/symbol), watchlist 40 (TVL >= $50M,
  no token, not wrapped/staked/bridged/versioned per house rule).
- Below-floor pre-token: 210; after my checkable exclusion rules (category in
  Bridge/Canonical Bridge/Liquid Staking/Liquid Restaking/Restaked BTC/CEX; versioned name;
  whole word wrapped/staked/staking; exchange-name prefix): 138 kept.
- Board = 40 watchlist + 138 early = 178 rows (gate needs >= 100, all 40 watchlist covered).
- Live-token protocols: 129 with mcap; median mcap/TVL 0.205, mean 5.0 (skewed, max 400) —
  median is the robust multiple. Per-category medians (n>=3): Dexs 0.342, Lending 0.132,
  Yield 0.180, RWA 0.275, Basis Trading 0.654, CDP 0.169, Yield Aggregator 0.151,
  Liquid Staking 0.041, CEX 0.075, Canonical Bridge 0.746, Bridge 0.557,
  Cross Chain Bridge 1.220, Privacy 0.969, Indexes 0.148. Many categories have <3 live
  comps -> global median fallback (0.205).
- Pre-token age median 513d; all-protocol age median 677d.

## Model — "radar-2" (lib/model.ts), NOT the starter's flat 0.25x
Implied value is priced from live peers, not a hardcoded multiple:
  multiple = median(mcap_usd/tvl_usd) of live protocols in the same category (n>=3),
             else the global median of all live multiples; capped at 2.0x, floored at 0.01x.
  growth   = clamp(change_7d, -60, 120); growthMult = 1 + growth/100 * 0.5
  maturity = 0.7 + min(age_days, 1095)/1095 * 0.3; unknown age -> median age of the
             snapshot's listed protocols (computed at runtime, 677d here)
  implied  = max(1, round(tvl_usd * multiple * growthMult * maturityMult))
No protocol name is special-cased; every input is a snapshot figure; the dashboard shows
the multiple, its comp count and its source (category/global) per row, plus a comparables
table of every category multiple. Live-token protocols are set aside (top 24 by mcap) with
their market caps shown, never mixed into the ranking.

## Farm plans (6 steps, 20..600 chars each, derived from category + chains + TVL + age)
- Capital tiers by TVL: >=$5B $250, >=$1B $150, >=$100M $100, else $50.
- Gas tiers by primary chain (Ethereum ~$4, Bitcoin ~$3, Solana ~$0.02, L2s ~$0.10,
  BSC/Avalanche ~$0.30, Polygon ~$0.05, default ~$1) — concrete per-protocol costs.
- Category actions expanded beyond the starter (Risk Curators, Basis Trading, Chain,
  Payments, CeDeFi, Collateral Markets, Staking Pool, Anchor BTC, Governance Incentives,
  Stablecoin Wrapper, ...), hold days by age (60/45/30), weekly action cadence, points-page
  check (conditional wording — never claims a program exists), farm log.

## Dashboard (dashboard/index.html + app.css + app.js, static, no network)
- Dark "radar" theme (ink #0b0f0e, amber accent, mint positive) — deliberately unlike the
  starter's light green. Source budget: keep index+css+js <= ~60 KB so the dossier text
  (65,536 B) shows it whole.
- Board table: sortable (Rank/Protocol/TVL/7d/Age/Multiple/Implied), search, category filter,
  chain filter, scope tabs (All / Watchlist / Early), status tabs (Pre-token / Live token),
  hash state (#q, #category, #chain, #scope, #p).
- Detail drawer per protocol: full breakdown (TVL x multiple [n comps, category/global] x
  growth x maturity = implied), page recomputes the row and says whether it matches
  board.json, farm plan with cost/time per step.
- Model panel: formula + comparables table (every category multiple + comp count).
- Screenshots (1440x900, target 300-350 KB each, cap 512 KiB, long side <=1440,
  short side >=800): 1-board.png (board), 2-detail.png (rank #1 detail), 3-filtered.png
  (category=Lending filtered view). Taken with Chromium 2 headless on the built out/.

## Gate self-checks to run before handing to parent
1. `node main.ts` twice into two dirs -> byte-identical outputs (determinism).
2. Local gate validator: board rows 100..4000, ranks 1..N once each, implied non-increasing,
   not all equal, all 40 watchlist covered, farm_steps 3..12 x 20..600 chars, slugs in snapshot.
3. Dashboard: <=400 files, <=4 MiB, no http(s):// in HTML attrs / CSS / JS imports.
4. Dossier: rebuild with buildDossier and compare bytes; dossier <= 1.5 MiB; text <= 65,536 B
   with all source files shown whole.
5. `makefun check --kit <kit> --repo <dir>` (repo-side checks; transcripts are the parent's).
6. Screenshots: whole PNG/JPEG, dimensions, sizes.

## Trial / grade results
- 2026-11-15 ~21:47 WIB, run 1 (no screenshots yet): board.json 253,163 B / data.js 74,575 B
  written, then exit 1 on missing screenshots (expected). Board validated in Python:
  178 rows, all slugs in snapshot, ranks 1..178, implied non-increasing, 178 distinct
  values, all 40 watchlist covered, farm steps 3..12 x 20..600 chars, implied ints > 0.
  Top: #1 Steakhouse Financial $494,281,607; #2 Gauntlet; #3 Veda; #4 USDD; #5 Upshift.
- BUG FOUND via the detail screenshot: the page's recompute said "which differs from the
  board" — board.json stored the multiple rounded to 4dp while implied was priced from the
  full-precision multiple. FIX (lib/model.ts impliedValue): round the multiple to 4dp FIRST,
  then price from it. Re-check (Node recompute of all 178 rows from data.js): 0 mismatches.
- 2026-11-15 ~21:52 WIB, runs 2+3 (two output dirs): byte-identical outputs
  (determinism verified). Full run exit 0: 178 rows (138 early, 24 live set aside),
  global multiple 0.2138x over 128 live protocols; dossier.json 902,715 B.
- Gate self-check (/tmp/gate-check.mjs, mirrors all 7 gate rows): 15 passed, 0 failed.
  Dashboard source 40,438 B (dossier text 40,704 B, nothing cut, all 3 files shown whole).
  Screenshots 1440x900 PNG: 208,489 / 239,426 / 197,671 B (all < 512 KiB).
- Kit `makefun check --kit . --repo <dir>` (no transcripts — parent's step): 4 passed
  (competition_open, snapshot caps, license_osi, entrypoint), 1 failed
  (transcripts_present — expected, no bundle yet), 14 skipped, 5 checked at submission
  (repo_public, repo_owner, repo_created_after_open, commit_in_repo, duplicate_snapshot).
  No repo-side failure. There is no local grade for this competition; the server grades.
- Screenshots visually checked (board / detail / filtered-Lending): dark theme renders,
  40 WATCHLIST + 138 EARLY tags, "178 rows shown", detail drawer recompute matches
  board.json ("the same number board.json carries"), filtered view shows 14 Lending rows.
- Repo cleaned: out/ and out2/ removed (regenerable with one command); 12 files, 746,635 B.
  No secret patterns found by grep (no API keys anywhere).
