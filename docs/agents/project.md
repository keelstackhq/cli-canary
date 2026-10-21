# Project notes: cli-canary

Canary repo for keelstackhq/hull's `cli` combo (rust-cli + npm-cli), hosted at
keelstackhq/cli-canary (rule R-12: separate from the web canary example-app so Rust runs do not
mix into its `ci-timing.mjs` history). It holds no product code; keep changes minimal. Test PRs
are closed after green and never merged, so `main` stays equal to the rendered tree.

- Source: keelstackhq/hull@7c7eadf, `node scripts/render.mjs new --template template --out <dir>
  --piece base --piece rust-cli:cli --piece npm-cli:cli --data scope=keelstackhq --json`, then
  `pnpm install --no-frozen-lockfile && cargo generate-lockfile && node scripts/agents-index.mjs`
  (the dev renderer runs no posts; CI uses `--frozen-lockfile` / `--locked`).
- Budget: job `ci` <= 45 s on a PR touching only `packages/cli`, <= 15 s docs-only. The PR
  touching `crates/cli` (crate + npm-cli affected) is reported as the worst case, not budgeted.
  `ci-ok` is the only required check.

## Steps

- [x] 3 Render the combo, local `pnpm run ci` green, commit once (not pushed by the agent)
- [ ] 3b Push `main`; record the cold run; re-run the `main` workflow once (no-op) to warm
      rust-cache and the turbo cache
- [ ] 4 Three PRs, one-line comment changes, closed after green, never merged:
      (1) `crates/cli/src/cli.rs` (worst case), (2) `packages/cli/bin/launcher.js`, (3) a docs file;
      then `node scripts/ci-timing.mjs --repo keelstackhq/cli-canary --budget 45 --limit 20`
      and record n / p50 / p90 and each PR's warm `ci` job seconds
- [ ] 5 Decide: PR (2) <= 45 s and PR (3) <= 15 s -> done; over budget -> numbers and first lever
      under Blocked in hull `docs/PROGRESS.md`
- [ ] 6 Record in hull: `docs: record cli combo CI timing against the 45 s budget`

## Progress

- 2026-10-21 · 3 · keelstackhq/hull@7c7eadf · render ok; `pnpm run ci` exit 0, 15/15 tasks, 0 cached, cold, 72 s (container timing, not runner evidence); `agents-index --check` exit 0

## Blocked

- Repo settings (admin on keelstackhq/cli-canary): protect `main`, require only the `ci-ok`
  status check; Settings -> Actions -> General: allow actions, workflow permissions "Read
  repository contents".

<!-- canary: docs-only change to measure CI -->
