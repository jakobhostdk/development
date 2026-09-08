# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this directory is

`/Users/jaho/development` is **not a single repository** — it is the user's top-level development folder containing ~35 independent git repositories. There is no shared build, lint, or test command at this level. Each project has its own toolchain; many have their own `CLAUDE.md` that you should read once you `cd` into the project.

When the user gives a task, first identify which subproject they mean, then operate inside it.

## Project index

### The `ign8*` family — infrastructure provisioners (Hetzner Cloud + Cloudflare)
A coherent suite of single-command "ignite a production service" CLIs. They share a recipe: provision a Hetzner VPS via Pulumi or SDK, create Cloudflare DNS, terminate TLS with Let's Encrypt, then SSH-configure the service. Most are pip/pipx-installable.

- [`ign8/`](ign8/) — pnpm/turbo monorepo (apps: `cli`, `web`, `mobile`; packages: `data`, `types`). The umbrella TS/React project. Build via `pnpm dev` / `pnpm build` / `pnpm typecheck` (turbo).
- [`ign8inventory/`](ign8inventory/) — Python CLI that provisions a NetBox DCIM/IPAM instance. Entry: `ign8inventory up`. Vault-integrated `infrastructure.yml`.
- [`ign8mail/`](ign8mail/) — Python CLI that provisions a Postfix/Dovecot/rspamd mail server. Pulumi-based.
- [`ign8vault/`](ign8vault/) — Python CLI that provisions a HashiCorp Vault + Consul stack with restic backups to Hetzner StorageBox.
- [`ign8cloud/`](ign8cloud/) — Provisions on-prem clouds (OpenStack, Proxmox, OKD4) via MAAS. See its `CLAUDE.md`.
- [`ign8k8s/`](ign8k8s/) — Next.js 15 + TypeScript Kubernetes provisioner UI (App Router + `provisioner/`). Pre-deployment.
- [`oldmail/`](oldmail/) — Legacy snapshot of `ign8mail`; do not edit unless asked.
- [`ign8git/`](ign8git/), [`ign8netbox/`](ign8netbox/), [`ign8it/`](ign8it/) — empty/stub repos.

### Trackers & dashboards (Django + Hetzner + Caddy pattern)
Personal/SaaS-style trackers. Most follow: Django backend + DRF + Caddy + Hetzner VPS at `*.jakobholst.dk`, Cloudflare-proxied. Some include a Chrome extension for capture.

- [`lifetracker/`](lifetracker/) — personal task/deviation tracker. Single `Task` model with `task_type` discriminator. Deployed `life.jakobholst.dk`.
- [`shoppingtracker/`](shoppingtracker/) — multi-user/multi-workspace shopping tracker with split-screen UI (tracker + embedded browser iframe) and Chrome extension.
- [`taskmanager/`](taskmanager/) — Django task app; bundles a copy of `ign8mail`.
- [`vulnerability_tracker/`](vulnerability_tracker/) — `ign8cve-tracker`. Syncs NVD feed, scans fleet via OpenVAS/OVAL/RH Insights, opens GitHub Issues for matches. Has alembic migrations, Docker Compose, FastAPI-style structure.
- [`photovault/`](photovault/) — Django photo storage with uvicorn + storage backup script.
- [`gitlab_workflow/`](gitlab_workflow/) — Django 4.2 dashboard for GitLab MR tracking and pipeline status. Entry: `manage.py` with `infra/` folder.

### Real-estate / housing tools
- [`traeck/`](traeck/) — workspace containing the paired **Traeck** two-sided Danish real-estate stack: `boligpuls/` (seller side, `boligpuls.dk`, Stripe + Claude-drafted copy) and `boligtracker/` (buyer side, multi-tenant, `bolig.jakobholst.dk`). Django 5 + DRF. Read `traeck/CLAUDE.md`.
- [`boligjagten/`](boligjagten/) — Next.js (App Router) + Prisma + SQLite (`dev.db`). `npm run dev`.
- [`thycyklering/`](thycyklering/) — Next.js + PM2 (`ecosystem.config.js`) + nginx, Bitbucket pipelines.
- [`boligtinder/`](boligtinder/) — Python; scrapes Chrome history (`chromehistory.py`) and provisions infra (`iac.py` via Pulumi + `pulumi_hcloud`). Uses `mise`.
- [`rentaltraeck/`](rentaltraeck/) — single-file Airbnb scraper (`scraper.py` + `odense_airbnb_data.csv`).

### GitLab CI & release tooling
- [`pipeline-components/`](pipeline-components/) — GitLab CI/CD components library. Reusable YAML templates under `templates/`.
- [`release_powershell/`](release_powershell/) — Python CLI that mirrors PowerShell releases to JFrog Artifactory. `.gitlab-ci.yml` deployed.
- [`release_putty/`](release_putty/) — Python CLI that mirrors PuTTY releases to JFrog Artifactory. `.gitlab-ci.yml` deployed.

### Web apps, games & demos
- [`theartillerist/`](theartillerist/) — Vite + React + Three.js (3D scene).
- [`pacmanclone/`](pacmanclone/) — vanilla HTML5 Canvas Pac-Man clone (ES modules, no build). Entry: `src/main.js`. Deployed to `pacman.traeck.it` via Docker Swarm.
- [`tripify/`](tripify/) — Next.js 16 (App Router) + React 19 + TypeScript, trip co-planning app (TV/participant dual-UI). Entry: `src/app/`.
- [`timeprinter/`](timeprinter/) — tiny Flask JSON time service (Flask + pytest). Entry: `app.py`. Docker image.
- [`localfirst/`](localfirst/) — single-file HTML5 static PWA (manifest + service worker); no build or dependencies.
- [`visualversion/`](visualversion/) — HTML landing page hosting a multi-framework 3D cube demo (Django, Vue, Node, .NET) as sibling folders.

### Enterprise infrastructure (work)
- [`ansible_automation_infrastructure/`](ansible_automation_infrastructure/) — enterprise AAP system managing 100+ APP-XXXX applications across dev/test/nonprod/prod. Oracle APEX/MOOD is source of truth. Syncs to per-app Bitbucket inventory repos; orchestrates patching, security, health checks. Python 3.12 + Ansible + Redis. Bootstrap via `bash bin/setup.sh`. Read its `CLAUDE.md`.
- [`ansible_automation_infrastructure.org/`](ansible_automation_infrastructure.org/) — earlier/parallel copy of the above (organization-flavored variant). Treat as separate; do not cross-edit.

### Sandboxes & scratch
- [`ideas/`](ideas/) — scratch/idea Python project.
- [`sand/`](sand/) — sandbox (`okd/`, `testprj/`).
- [`sandbox/`](sandbox/) — multi-project sandbox with `clone_all.py` and `projects/` subfolders (`ign8-it-group`, `jakobholst`).
- [`gitlab/`](gitlab/), [`vatter/`](vatter/), [`immutablewindows/`](immutablewindows/) — empty/stub repos.
- `netbox.quickstart.txt` — loose notes file at the root.

## Working conventions across these projects

- **Hosting:** almost everything runs on **Hetzner Cloud** with **Cloudflare DNS** (often proxied) and **Let's Encrypt** TLS via **Caddy** or **nginx**. Domain `jakobholst.dk` is the personal apex; `traeck.it` is used for some Traeck-family deployments.
- **Python projects** mix styles: some `pyproject.toml` (poetry or pip), some `requirements.txt`, several with a vendored `venv/` directory committed alongside source. Always activate the project's own venv before running commands.
- **JS/TS projects:** Next.js projects use `npm`; the `ign8/` monorepo uses **pnpm + turbo**.
- **IaC tool of choice is Pulumi** (Python flavor) when not using direct SDK calls; some newer projects use Terraform.
- **CI:** work projects (`pipeline-components`, `release_*`) target **GitLab CI**; personal projects mostly use GitHub Actions or Bitbucket Pipelines.
- Per-project `CLAUDE.md` files exist for the larger projects — read them before making changes.
