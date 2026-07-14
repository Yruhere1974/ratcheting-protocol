# Manager Notes: 7 Degrees (MVP)

## Intent
Maintain and evolve the "7 Degrees" relationship-intelligence application. This repository is the fork for `Yruhere1974`.

## Context
- **Forked From:** `Idkbythispoint/mvpting`
- **Subconscious Sync:** The app is designed to work with the graph memory on Simon2.
- **Ratcheting:** We follow the Ratcheting Protocol for all changes.

## Open Tasks
- [x] **Infrastructure Sync:** Local deployment stable on port 5001 (2026-07-05). See "Ratcheted" below.
- [ ] **AI Integration:** Validate that the Gemini CLI on Simon2 can be used as a backend for complex relationship analysis.
- [ ] **Data Integrity:** Verify the "7 Degrees" export/import functionality.

## Ratcheted
- **2026-07-05 — Feature: Publishable Event Mesh (public self-registration):** An event
  can be **published** from its page → mints an unguessable public token and exposes a
  login-free page at `/e/<token>` with a QR code. Walk-up attendees see who's here (a live
  mesh of cards: name, org/role, freeform "about") and **add themselves** — name required,
  everything else optional/freeform. Self-adds land in the workspace exactly like a normal
  capture (person page tied to the event via an interaction, attributed to the host), so they
  also appear under the host's "People met here" and flow into the graph. Publish/unpublish is
  reversible and keeps the same token/QR. New: `events` cols `public_token`/`published_at`;
  `app/routes/public_event.py`; publish/unpublish actions; `public_event.html`; `segno` for QR.
  Verified end-to-end on :5001 (publish → anon view → 3 self-adds persisted → host page +
  DB confirm; QR renders; bad token 404; empty name rejected). Version 0.9.0 → 0.10.0.
  - **Bugfix bundled (app-wide):** `docker build` pulled FastAPI 0.139 via `fastapi>=0.115`,
    whose new `_IncludedRouter` stopped flattening included routes into `app.routes`. This broke
    `templating._path_param_names`, so **every** `url_for(..., <path_param>=...)` in every
    template silently dropped its param and 500'd (person pages, event pages, etc.). Fixed by
    making the route walk descend into `_IncludedRouter.original_router.routes` — version-agnostic.
    Worth pinning FastAPI/Starlette later for reproducibility; the robust walk is the real fix.
  - **Note:** a demo event #1 "Frontier Founders Mixer" with 3 sample attendees exists from
    testing — delete it or wipe the `pgdata` volume before a clean seed.
- **2026-07-05 — Infrastructure Sync (local deploy on :5001):** Stack brought up via
  `docker compose up --build`. App serves on host **5001** (`/health` → `db: ok`,
  schema seeded, `dev@example.com` admin user present). One infra fix: the DB's host
  port publish was remapped `5433 → 5434` because host 5433 collided with another
  local Postgres; this mapping is inspection-only (the app reaches the DB over the
  compose network as `db:5432`), so it is not load-bearing. `config.json` was created
  from `config.example.json` with AI keys left as placeholders (app degrades AI
  gracefully); it is gitignored and not committed.

**Handoff:** Local deployment ratcheted. To run: `docker compose up -d` → http://127.0.0.1:5001
(dev login `dev@example.com` / `dev`). Next candidate clicks: AI Integration and Data Integrity.
