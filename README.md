# Site Generator Speed — a faster makefun-site

[![Competition](https://make.fun/badge/site-generator-speed-01-run-20261122.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122)
[![Rank](https://make.fun/badge/site-generator-speed-01-run-20261122/ginoray/site-generator-speed-01/rank.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122)
[![Reward](https://make.fun/badge/site-generator-speed-01-run-20261122/ginoray/site-generator-speed-01/reward.svg)](https://make.fun/bounty/site-generator-speed-01-run-20261122)

A drop-in replacement for make.fun's **makefun-site 1.0.0** static site generator that returns exactly the
same output tree for every input — every page's `index.html`, the index and tag pages, `build-report.txt` and
each static file, byte for byte — in far less CPU time.

## Layout

- `main.ts` — the harness driver: reads `/input/cases.json`, answers each case with `run`, writes
  `/output/answers.json`.
- `solve.js` — the generator itself: a dependency-free ES module whose `run(input)` builds one site.

## How it is faster

makefun-site's semantics are kept exactly (its front matter, template dialect, Markdown subset, slug rule and
report); where its time goes is not:

- **Inline Markdown** is rendered in one pass over each string with index ranges — no substring is copied to
  find a closer, and plain text is escaped in runs instead of one `escapeHtml` call per character.
- **Block lexing** classifies lines by character codes instead of testing each line against a battery of
  regular expressions.
- **Templates are compiled** to plain JavaScript functions once per build instead of being walked as a tree
  of nodes through a chain of scope objects for every page.
- **Slugs** are computed once per distinct title or tag name per build and cached; the slugger itself has an
  ASCII fast path that skips Unicode normalisation for the common case.
- **Word counts** (for `reading_minutes`) are counted by a hand scanner instead of a global regex.
- **Front matter** is scanned by line ranges over the source text instead of splitting the whole file and
  joining the body back together.

Nothing is cached from one case to the next: every cache lives inside a single build.

## Licence

MIT — see `LICENSE`.
