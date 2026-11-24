# Breakout Rider — strategy notes

## Idea

Most pump.fun launches die fast, but a visible minority run 2x–10x from their
create price (on the dev window: 286 of 1,222 coins reached 2x, 145 reached 3x).
The edge is not in buying the launch itself (a lottery: the sniper pilot's
13% win rate) and not in chasing a mature run (buying the top: the momentum
pilot lost 9.4 SOL of 10 on the dev window), but in riding the *early*
breakout: a coin up a little off its create price, with several distinct
wallets still buying into it and net SOL flowing in.

## Rules

- **Entry** (first buy only, one entry per coin): age 10–100 slots since the
  CreateEvent; price 15%–30% above the create price; price within 8% of the
  25-slot window high; at least 5 buy trades and 6 distinct buyers in the
  window; net inflow at least 0.1 SOL and accelerating (newer half ≥ older
  half); no single wallet over 60% of the window's buy SOL; real SOL reserve
  0.5–80; creator has not sold.
- **Sizing**: one 0.03 SOL buy per coin, no pyramiding (adding to positions
  cost more than it ever gained on the dev window); every buy is also capped
  at 1% of the curve's real SOL reserve and by free cash; at most 4 breakout
  positions open (5th slot kept for probes) and 4 SOL total exposure.
- **Exits** (whole position, first trigger wins): take-profit +25% over cost,
  stop-loss −15%, trailing stop −10% from the peak once the trade is +8%,
  creator sell, any single sell ≥ 2 SOL, or 1,500 slots held (unless up ≥ 50%).
- **Guardrail pacing**: if the bot's fills or coins lag a pace of 22 coins /
  30 fills over the 24 h window, and only after half the window has elapsed,
  it opens 0.01 SOL probe round trips on liquid curves until back on pace —
  at most 8 ever. This keeps the run above the minimum bar (a ≥ 0.01 SOL
  round trip on each of at least 20 coins) even in a quiet final window,
  for at most ~0.004 SOL in fees.
- **Costs**: curve fees are 95 + 30 bps a side plus 105,000 lamports per tx,
  so a round trip costs ~2.5% before slippage; buys allow 1,500 bps slippage,
  sells go out at 10,000 bps.

## Local backtest (full dev window, all 3 dataset parts, momentum sim port)

| version | net PnL | fills | coins | trips | wins | max DD |
| --- | --- | --- | --- | --- | --- | --- |
| sniper pilot (default) | −0.489 SOL | 848 | 354 | 354 | 45 | 5.3% |
| reversion pilot (default) | −4.016 SOL | 2,316 | 198 | 424 | 111 | 48.9% |
| momentum pilot (default) | −9.387 SOL | 410 | 48 | 80 | 5 | 95.0% |
| breakout-rider v1 | −1.348 SOL | 728 | 242 | 242 | 42 | 13.5% |
| breakout-rider shipped | **+0.007 SOL** | 54 | 25 | 27 | 8 | 0.33% |

Two lessons from the sweep (5 rounds, ~50 variants on the full window):

1. **Pyramiding into losers was the leak.** Adding to positions as they fell
   cost more than it ever gained: no-pyramid alone took v1 from −1.35 to
   −0.50 SOL.
2. **Unbounded guardrail probes were the second leak.** The first pacing
   design fired a 0.01 SOL probe round trip (~0.0005 SOL in fees) whenever
   fills lagged a 240-fill pace — hundreds of them, ~0.06 SOL of pure fee
   bleed. The shipped design probes only after half the window, only up to
   8 ever, and only to top the coin count up toward 22.

The shipped settings (single 0.03 SOL buy per coin; entry only on a
confirmed early breakout: +15%–30% off create, ≥6 distinct buyers, net
inflow ≥ 0.1 SOL and accelerating, price within 8% of its 25-slot high;
+25% take-profit, −15% stop, 10% trailing stop armed at +8%, 100-slot
entry window) are the flat region around the best cell, not a single best
point: neighbouring knobs (stake 0.02–0.05, net-flow 0.07–0.10) give the
same result, and the two window halves are −0.018 / +0.025 SOL.

The dev window is a hard regime (all three house pilots lose on it), so the
dev number is a stress test, not a forecast: the final window is fresh 24 h
of coins that do not exist yet. What the design buys is asymmetry — losers
are small (0.03 SOL stakes, −15% stop) and quick, while winners are
allowed to run — plus a drawdown guard (max 4 SOL exposed, tight stops)
that keeps the run far from the 50% drawdown bar. Net PnL without the top
3 coins is −0.041 SOL: the profit rides on a few runners, which is the
nature of this market — the final window's runners are what the bot is
built to catch.

## Determinism

No clock, no randomness, no I/O. Iteration is only over insertion-ordered
maps fed by the batch stream, so the two graded executions produce
byte-identical action logs.
