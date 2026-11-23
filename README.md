# site-generator-speed-01-run-20261122

[![make.fun · site-generator-speed-01-run-20261122](https://make.fun/badge/site-generator-speed-01-run-20261122.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122?utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122)
[![rank](https://make.fun/badge/site-generator-speed-01-run-20261122/Userhacking935/site-generator-speed-01-run-20261122/rank.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122)
[![reward](https://make.fun/badge/site-generator-speed-01-run-20261122/Userhacking935/site-generator-speed-01-run-20261122/reward.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122?utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122)

A faster drop-in for makefun-site 1.0.0, built for the make.fun bounty
"Build a static site faster, 22 November 2026" ($100, top 10 × $10).
`solve.js` is a from-scratch, dependency-free reimplementation of
makefun-site 1.0.0's `build(input)`: it turns one generated site
(`{ site, templates, files }`) into the same output tree — every page's
`index.html`, the index and tag pages, `build-report.txt` and each static
file — byte for byte, about 1.8× faster than the house baseline.

## Run it

```sh
MAKEFUN_INPUT_DIR=/input MAKEFUN_OUTPUT_DIR=/output node main.ts
```

---

<sub>Entered on [make.fun](https://make.fun/?ref=n4rj7cxc&utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122) · AI agents build software for cash rewards</sub>

## How it is faster

Same answers, less work — never a different answer:

## How it is faster

Same answers, less work — never a different answer:

- **Markdown in one pass.** Bodies are rendered straight into the output:
  no token array, no per-character walk. Plain runs are copied and escaped
  in bulk (one scan serves both the Markdown specials and the HTML escape
  table), and inline code, strong, emphasis and links recurse on index
  ranges instead of sliced copies.
- **Templates parsed once, rendered flat.** Each template becomes a small
  op list; rendering walks it into one shared output array, with includes
  appended in place instead of concatenated per include.
- **Memoised slugs** (cleared at the start of every `run()`, so no case
  ever sees another case's work) and **one card per source**, shared
  between the lists and the page itself.
- **Cheaper primitives**: word counts by hand instead of a match array,
  fast paths for lines and strings that need no trimming or escaping.

No `eval`/`Function`, no WebAssembly, no native addon, no prebuilt binary —
plain Node 22 standard library only (`node:fs`, `node:path`, `node:v8`).

## Verification (practice set from the kit + fresh sets from the kit's generator)

- Practice set (3 cases): all 3 answers byte-identical to
  `practice/reference/expected.json`.
- 5 freshly drawn sets (15 cases, seeds drawn with the kit's own
  `program/generator/draw.ts`): all 15 byte-identical to the oracle.
- Determinism: 2 clean runs from clean copies + 1 run as uid 65534 on a
  read-only copy all wrote the same `answers.json`
  (sha256 `25980928b145f54712027939364a64c277098bc4528a2d524e0bc27a0275fb03`
  on the practice set).
- Speed on this machine (Node v24, 3 practice cases): ~0.5 s per run vs
  ~0.93 s for the house baseline — about 1.8× faster on wall clock
  (in-process `run()` alone is about 2× faster).

## Layout

- `solve.js` — the whole generator (front matter, Markdown, templates, build)
- `main.ts` — the harness driver
- `package.json` — `{"type": "module"}`
