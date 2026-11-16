# Token Radar — the model (radar-2)

This dashboard ranks every pre-token protocol the snapshot can rank: the 40-protocol
watchlist, plus every other rankable pre-token protocol below the floor. Nothing is
hand-picked and no protocol name is ever special-cased.

## The valuation

Implied value is a comparables valuation, computed from the snapshot itself at run time:

```
implied = round( tvl_usd x category_multiple
                 x (1 + clamp(change_7d, -50, +100) / 100 x 0.35)
                 x (0.5 + 0.5 x min(age_days, 1095) / 1095)
                 x (1 + 0.04 x min(chains - 1, 10)) )
```

- **category_multiple** — the protocol's category median market-cap / TVL among this
  snapshot's own live-token protocols, shrunk toward the market-wide median (weight
  n / (n + 8)) and clamped to [0.08, 0.60]. A category with no live peers takes the
  market median. Sectors price differently, and the snapshot says by how much.
- **growth** — the seven-day TVL change, clamped and dampened so a one-week spike
  cannot carry a row. No reported change scores 0%.
- **maturity** — age since first listing, the best record of how long a protocol has
  run with no token: 0.5 at day 0, 1.0 at 1,095 days. Unknown listing date counts as
  180 days.
- **breadth** — each extra chain beyond the first adds 4%, up to 10 extra chains.

## What is ranked, and what is set aside

A protocol is ranked when it records no token (no market cap, no CoinGecko id, no
symbol) and is not a wrapped, staked or bridged product of a token that already
trades — the house's own watchlist rule, minus the TVL floor. That leaves out the
Bridge, Canonical Bridge, Liquid Staking, Liquid Restaking, Restaked BTC and CEX
categories, version-marked names, names carrying a whole word wrapped, staked or
staking, and names beginning with an exchange's name. Live-token protocols are set
aside in their own view, shown at market value — and their market-cap / TVL ratios
are what calibrate the board.

Two honest limits. The snapshot carries no parent-protocol field, so a curator of
another protocol can only be read as pre-token by its own record. And DefiLlama's
record is not proof: the board says a protocol records no token, never that it has
none.

## How to check any number

Open any row: the detail view shows every factor, and the page recomputes the row
with `impliedOf` in app.js and checks it against the number board.json carries.
The calibration table (category, live peers, peer median, applied multiple) is on
the Model tab. The farm plan under each row is derived from the protocol's own
record — category, chains, TVL and age — with a cost and a time on every step.
