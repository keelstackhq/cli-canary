# keelstackhq

Monorepo generated from hull 0.0.0-dev: pnpm workspaces, Turborepo,
TypeScript, Vite+. Packages live in `apps/*`, `packages/*`, `crates/*`.

## Commands

```bash
pnpm install        # install, also runs in the SessionStart hook
pnpm check          # lint, format:check, typecheck, test (all packages)
pnpm run ci         # the CI task list on changed packages (--affected)
pnpm format         # vp fmt
pnpm agents:index   # regenerate docs/agents/index.md
```

Packages call `vp build/test/lint/fmt/pack` directly. Turbo caches; never wrap tasks in `vp run`.

## Rules

- Test first: write the failing test, watch it fail, implement, watch it pass.
- Verify with real output before saying a task is done or fixed.
- Keep files small and focused; one task per change; no drive-by refactors.
- Versions live in the catalog in `pnpm-workspace.yaml` as exact pins; use `catalog:` in packages.
- Files owned by the template (configs, workflows, `.claude/`) are updated by `splice update`;
  put local changes in seed files, `docs/agents/project.md` or a package's
  `AGENTS.md`.
- Never print or commit secrets; never read `.env*` or `.dev.vars`.
- Releases are tag pushes made by a human; never publish or push tags yourself.
- At most 2-3 parallel agents, on files that barely overlap.
- Before a long task write the next steps to `docs/agents/project.md`; start fresh rather than
  compacting.
- A hook `ask` becomes a denial in `-p`/unattended runs: do the change by hand or run interactively.

## Pieces

- `crates/*` (rust-cli): distributable CLI skeleton with one JSON object on `--json`, fixed exit codes and cargo-deny; scripts use `-p <crate>`; see `crates/<name>/AGENTS.md`.
- `packages/*` (npm-cli): npm launcher for a rust-cli binary, platform packages are staged at release time; see `packages/<name>/AGENTS.md`.

## Where to look

- `docs/agents/project.md`: repo-specific notes; read first.
- `docs/agents/index.md`: generated map of commands, packages and docs.
- `<dir>/AGENTS.md`: notes for one package.
