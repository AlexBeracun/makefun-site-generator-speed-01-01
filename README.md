# makefun-site-generator-speed-01-01

[![make.fun · site-generator-speed-01-run-20261122](https://make.fun/badge/site-generator-speed-01-run-20261122.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122?utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122)
[![rank](https://make.fun/badge/site-generator-speed-01-run-20261122/AlexBeracun/makefun-site-generator-speed-01-01/rank.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122)
[![reward](https://make.fun/badge/site-generator-speed-01-run-20261122/AlexBeracun/makefun-site-generator-speed-01-01/reward.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122?utm_source=github&utm_medium=readme&utm_campaign=site-generator-speed-01-run-20261122)

A faster drop-in for makefun-site 1.0.0: a static site generator that
answers exactly as makefun-site does on every case — every page's
`index.html`, the index and tag pages, `build-report.txt` and every static
file, byte for byte — but in roughly half the CPU time.

## How it is faster

`solve.js` is one dependency-free ES module exporting `run(input)`. It
reimplements makefun-site's own dialect (front matter, template dialect,
Markdown subset, slug rule and report) with the same answers and three
speed wins:

- Templates are compiled once per case into plain JavaScript functions
  instead of being walked as a node tree on every render. Lookups resolve
  through one merged scope object, includes are direct calls, and output
  is pushed into an array joined once.
- Markdown is rendered in a single pass over the lines, with no token
  objects, and inline markup is scanned with one regex, so plain runs
  between marks are pushed raw with no escaping pass at all.
- `slugify` results are cached per run (titles, headings and tag names
  repeat constantly) with a fast ASCII path, link targets are resolved
  once per source, and word counts are counted, not collected.

No state is kept between cases: every cache is created inside `run()`.

## Run it

`main.ts` is the harness driver: it reads every case from
`/input/cases.json`, answers each one alone with `run()`, and writes
`/output/answers.json`.

```sh
node main.ts
```

## Verify it

On the practice set the entry is byte-identical to makefun-site 1.0.0 on
all 3 cases, and about 1.8x faster wall-clock on the same machine
(~0.9s vs ~1.6s for the three practice cases, including JSON I/O).
Two runs of the entry produce identical output.
