# development

Meta-repo for `~/development/` — the top-level workspace holding ~35 independent projects.

This repo tracks **only** the top-level meta files:

- [`CLAUDE.md`](CLAUDE.md) — full project index, grouped by purpose (ign8 family, trackers, real-estate, GitLab CI tooling, web apps/games, enterprise infra, sandboxes). Read this first.
- `.gitignore` — excludes everything by default; un-ignores the tracked meta files.
- `netbox.quickstart.txt` — loose notes.

Each subdirectory is its own independent git repo with its own toolchain — nothing is a submodule.

## Conventions

- **Hosting:** Hetzner Cloud + Cloudflare DNS + Let's Encrypt via Caddy/nginx. Personal apex `jakobholst.dk`; some Traeck-family deployments on `traeck.it`.
- **Languages:** mixed Python (poetry / pip / vendored `venv/`) and TypeScript (npm for Next.js apps, pnpm + turbo for the `ign8/` monorepo).
- **IaC:** Pulumi (Python) for older projects, Terraform for newer ones.
- **CI:** GitLab CI for work projects (`pipeline-components`, `release_*`); GitHub Actions or Bitbucket Pipelines for personal ones.

See [`CLAUDE.md`](CLAUDE.md) for the full breakdown.
