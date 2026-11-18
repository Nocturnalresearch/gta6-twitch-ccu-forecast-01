# CHECKS.md — gta6-twitch-ccu-forecast-01

Competition: gta6-twitch-ccu-forecast-01 ($250, forecast GTA VI launch-day Twitch
viewers hour by hour). Deadline 2026-11-19T01:00:00Z (08:00 WIB 19 Nov 2026).

## 1. Competition exists live
Confirmed in the live list 2026-11-18 17:51 WIB: 12,662 submissions,
deadline 2026-11-19T01:00:00Z. Slug `gta6-twitch-ccu-forecast-01`, open and free.

## 2. Grading (read from the kit's competition.yaml + RULES.txt)
- Program (Python 3.13 `main.py`, stdlib only) reads `/input/<id>.in` (hour-1
  print + history + 42 comparables) and writes `/output/<id>.out` (23 rows,
  hours 2-24, q=[q05,q25,q50,q75,q95]).
- Score: mean pinball loss on the log scale over hours 2-24 x 5 quantiles.
  Lower is better. House baseline (persistence, q = p1 * exp(z*0.15*sqrt(h-1)))
  scores ~0.387 on practice. A prize needs a loss strictly below the best house
  entry (baseline or house model forecast) on the final.
- 8-second runtime limit per case. Practice cases p00-p03 (4 past launches).
  The final case h00 is cut at the deadline (2026-11-19T01:00Z).
- Prizes: 20 places pay ($50/$35/$25, $10 for 4th-10th, $7 for 11th-20th).
- No live provider key needed — grading is offline against recorded Twitch data.
  No cost to enter, no fee.

## 3. Credential / cost
None. Free to enter, no provider key, no fee. No secrets pushed.

## Model (adapted from the WINNING FAA-02 approach)
- FAA-02 ("Candid Obelisk", 7th place, $5) used: median anchored to the latest
  print, empirical quantile shape, scale 0.8. It won.
- Here: median = hour-1 print (p1) x the empirical median launch-day ratio per
  hour (from the case's comparables); quantiles = the empirical quantiles of the
  log-ratio ln(viewers_h / viewers_1) across filtered comparables.
- Filter: comparables with hour-1 < 5% of their own peak are dropped (pre-unlock
  games whose hour 1 is ~0 and whose log-ratios explode). 29 of 37 usable curves
  pass in the practice cases.
- Scale = 1.0 (no shrinkage). Leave-one-out CV on the comparable launch curves:
  filtered set pinball 0.310 (mine) vs house baseline 0.359 (ratio 0.864);
  all-curves pinball 0.714 vs house 0.930 (ratio 0.768). Shrinking to 0.9 / 0.8
  worsens it (0.313 / 0.317). The full empirical spread is the calibrated
  choice for this problem (the FAA-02 scale 0.8 does not transfer — this
  problem's spread is genuinely wide and the CV says so).
- The official practice pinball cannot be computed locally (the kit holds no
  answers; the scorer stays on the server). The leave-one-out CV above is the
  practice estimate. Output shape verified on all 4 practice cases (23 rows,
  5 quantiles each, ordered, 0<q<=1e9, <=65,536 bytes, deterministic, 0.21 s
  runtime, well under 8 s).

## Files
- `main.py` — the entrypoint (Python 3.13, stdlib only).
- `LICENSE` — MIT (from the kit starter).
- `.makefun/owner.json` — `{"wallet": "Tyjmw5GN1LTzwx3Tkks9xZgSntEJrjRQ9K5YGWssWir"}`.
- `CHECKS.md` — this file.
