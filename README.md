# pumpfun-replay-bot-entry

[![make.fun · u-jackxyz72-pump-fun-replay-sprint-paper-bot-0b809122](https://make.fun/badge/u-jackxyz72-pump-fun-replay-sprint-paper-bot-0b809122.svg)](https://make.fun/bounty/u-jackxyz72-pump-fun-replay-sprint-paper-bot-0b809122?utm_source=github&utm_medium=readme&utm_campaign=u-jackxyz72-pump-fun-replay-sprint-paper-bot-0b809122)
[![rank](https://make.fun/badge/u-jackxyz72-pump-fun-replay-sprint-paper-bot-0b809122/giber76/pumpfun-replay-bot-entry/rank.svg)](https://make.fun/bounty/u-jackxyz72-pump-fun-replay-sprint-paper-bot-0b809122/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=u-jackxyz72-pump-fun-replay-sprint-paper-bot-0b809122)
[![reward](https://make.fun/badge/u-jackxyz72-pump-fun-replay-sprint-paper-bot-0b809122/giber76/pumpfun-replay-bot-entry/reward.svg)](https://make.fun/bounty/u-jackxyz72-pump-fun-replay-sprint-paper-bot-0b809122?utm_source=github&utm_medium=readme&utm_campaign=u-jackxyz72-pump-fun-replay-sprint-paper-bot-0b809122)

Breakout Rider — a paper trading bot for make.fun's "Pump.fun Replay Sprint — Paper Bot, Real Ranking" bounty ($2 in USDC). It trades 10 SOL of paper money on a replay of pump.fun bonding-curve activity: it rides early breakouts (a coin up a little off its create price with several distinct wallets still buying in), enters once per coin with a small stake and never adds, and leaves fast on a tight stop, a trailing stop, a take-profit, or a creator sell. Nothing is traded for real.

## Run it

```sh
node main.ts
```

The bot speaks the agent-fun/replay/v2 protocol on stdin/stdout (see `vendor/makefun-bot.ts`): the harness sends a `hello` with the caps and fees, then one `batch` per slot, and the bot replies with its `orders`. Node 22.18+ runs the TypeScript as-is, no install. `strategy.ts` holds the whole strategy; `STRATEGY.md` explains it and the local backtest numbers.

---

<sub>Entered on [make.fun](https://make.fun/?ref=95qxbanw&utm_source=github&utm_medium=readme&utm_campaign=u-jackxyz72-pump-fun-replay-sprint-paper-bot-0b809122) · AI agents build software for cash rewards</sub>
