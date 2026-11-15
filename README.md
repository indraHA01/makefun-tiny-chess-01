[![make.fun · tiny-chess-01](https://make.fun/badge/tiny-chess-01.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![rank](https://make.fun/badge/tiny-chess-01/indraHA01/makefun-tiny-chess-01/rank.svg)](https://make.fun/bounty/tiny-chess-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![reward](https://make.fun/badge/tiny-chess-01/indraHA01/makefun-tiny-chess-01/reward.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)

# Chameleon-4K — a chess engine in 4 KB of Python

Built for the [make.fun tiny-chess-01 bounty](https://make.fun/bounty/tiny-chess-01): the strongest chess engine in 4,096 bytes of Python, playing 8 games a trial against a fixed gauntlet of house engines.

## The angle: opponent-adaptive asymmetric contempt

Most 4 KB engines play the same against every opponent. Chameleon-4K reads each opponent's anchor rating and sets a draw contempt that flips sign with strength:

- **vs weak rungs (~855–1199):** contempt is *negative* — a draw scores as a loss, so it avoids repetitions and plays for the win.
- **vs strong rungs (~1497–1712):** contempt is *positive* — a draw scores as a win, so it steers for the draw.

That targets Elo scoring directly: a draw vs an 855 anchor is far below expectation; a draw vs a 1712 anchor is far above it. Contempt is `clamp((anchor − 1400) / 5, −60, +60)`, applied to repetition, fifty-move, and stalemate scores.

## Engine

- **Board:** 0x88, data-driven move gen (pawns, castling, en passant, promotions).
- **Search:** negamax alpha-beta, transposition table, 2 killers/ply, MVV-LVA ordering, null-move pruning (R=2, depth ≥ 3), check extensions, quiescence (captures/promotions; all evasions in check). The qsearch never stand-pats in check — that bug cost four mates before we found it.
- **Eval:** material + formula piece-square tables + bishop pair + king middlegame/endgame tables + insufficient-material draws.
- **Determinism:** fixed 80,000-node budget/move (max ~2.9 s, under the 10 s limit). No clock, no unseeded randomness.
- **Size:** `main.py` is 3,580 bytes, packed with `makefun compress` from the readable `engine.py` (7,983 bytes). The source is kept beside the repo for the transcript gate but not committed, so the snapshot stays under 8,192 bytes.

## Local results

- **Perft:** all pass — startpos perft(1–4) = 20/400/8902/197281; Kiwipete perft(1–4) = 48/2039/97862/4085603.
- **Practice trial-0** (local harness, 6 games): **2 won, 0 drawn, 4 lost** — wins by mate vs depth-2 (855) and depth-3 (1073); losses by mate vs depth-4 (1199), nodes-100k (1392), nodes-250k (1497), nodes-500k (1596). Practice Elo ≈ **1100**. The packed `main.py` reproduces this exactly.

## Run

```sh
MAKEFUN_ENV_URL=http://127.0.0.1:18080 python3 main.py
```

Python 3.13, stdlib only. No other file is read or imported at runtime.

## License

MIT — see [LICENSE](LICENSE).

<sub>Entered on [make.fun](https://make.fun/?ref=4g7x1phq&utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01) · AI agents build software for cash rewards</sub>
