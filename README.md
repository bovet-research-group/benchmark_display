# benchmarks-display

Central display site for the ASV (airspeed velocity) benchmark results of
the Bovet research group packages:

- [temporal-networks](https://github.com/bovet-research-group/temporal-networks) (`tempnet`)
- [stochmat](https://github.com/bovet-research-group/stochmat)
- [flow_stability](https://github.com/bovet-research-group/flow_stability)

The deployed site shows one interactive ASV report per package plus a
landing page linking them.

## How it works

- Benchmark **suites, configuration and raw results live in the package
  repositories** (each has its own `asv.conf.json`, `benchmarks/` and
  committed `.asv/results/`). This repository never runs benchmarks; it
  only renders committed results.
- A daily workflow ([publish.yml](.github/workflows/publish.yml)) checks
  each tracked repository for new commits on its tracked branch
  (`git ls-remote` against [state/last-shas.json](state/last-shas.json)).
  The site is rebuilt only if at least one repository changed (or when
  triggered manually with `force`).
- For each repository the workflow clones it, runs `asv publish` on the
  committed results and places the report under `/<repo>/` on the site.
  Repositories without an `asv.conf.json` (or without committed results)
  are skipped and shown as "benchmarks not yet available" on the landing
  page.
- After a successful deployment the workflow commits the published commit
  SHAs back to `state/last-shas.json`.

## Scope

The site shows the performance **history of the tracked branches only**.
Branch-vs-main comparisons (`asv continuous`) are a PR-time tool and live
in the package repositories' pull requests, not here.

## Adding a package

1. Set up ASV in the package repository (`asv.conf.json`,
   `benchmarks/`, commit results under `.asv/results/`).
2. Add the repository name to the `REPOS` list in
   [publish.yml](.github/workflows/publish.yml) and a `null` entry in
   [state/last-shas.json](state/last-shas.json).

## Setup (one-time)

1. Create the GitHub repository and push these files.
2. Repository settings → Pages → Source: **GitHub Actions**.
3. Trigger the workflow manually once (`Run workflow` with `force`).
