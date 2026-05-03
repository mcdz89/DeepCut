# Deepcut

**A self-hosted video library manager built for people who actually have video files.**

Deepcut is a metadata-first video library manager that works with 1,000+ sites via yt-dlp. Import files you already have, build a catalog of content you don't have yet, and enrich everything with full metadata — without re-downloading a single thing.

> ⚠️ This project is in active early development. Nothing is stable yet. Architecture and API decisions are still being made.

---

## The Problem

Every existing self-hosted video tool is YouTube-only, download-first, and ignores the files you already have. Deepcut was built to fill those gaps:

- **Multi-site** — anything yt-dlp supports (1,000+ sites), not just YouTube
- **Import-first** — bring your existing library in without re-downloading anything
- **Metadata-first** — catalog content you don't have files for yet; download later
- **Low footprint** — no Elasticsearch, no Redis, no 4GB RAM floor

---

## Planned Architecture

### Tech Stack

| Layer | Technology | Notes |
|---|---|---|
| Backend | Python 3.12 + FastAPI | Async, lightweight, auto-generates OpenAPI docs |
| Frontend | React | Served by FastAPI in production |
| Database | SQLite (default) / PostgreSQL | User's choice via env config |
| Processing | yt-dlp · ffmpeg-python · ffprobe | Bundled in Docker image |
| Deployment | Docker Compose · native OS installer | Docker is primary target |

### Directory Structure

```
deepcut/
├── backend/
│   ├── app/
│   │   ├── main.py          # FastAPI entry point
│   │   ├── models.py        # SQLAlchemy models
│   │   ├── database.py      # DB connection + Alembic setup
│   │   ├── routers/
│   │   │   ├── library.py   # Library CRUD
│   │   │   ├── scan.py      # Scan + import endpoints
│   │   │   ├── enrich.py    # Metadata enrichment
│   │   │   └── download.py  # yt-dlp download queue
│   │   └── services/
│   │       ├── ytdlp.py     # yt-dlp wrapper (library, not subprocess)
│   │       ├── ffprobe.py   # ffprobe JSON extractor
│   │       └── ffmpeg.py    # ffmpeg-python processing wrapper
│   └── alembic/             # DB migrations
├── frontend/
│   └── src/
│       ├── pages/
│       │   ├── Library.jsx
│       │   ├── Triage.jsx
│       │   └── Settings.jsx
│       └── components/
├── docker-compose.yml
├── .env.example
└── README.md
```

---

## Core Design Decisions

### Three Import Paths

**New downloads** — yt-dlp fetches metadata and streams → ffmpeg processes → ffprobe validates → DB written.

**Existing files** — ffprobe scans locally (no internet required) → available metadata written immediately → missing fields queued for enrichment.

**Metadata-only** — yt-dlp `-j` fetches full JSON without downloading → DB entry created → `file_path` is null → download available on demand.

### Monetization Model

Deepcut is free and open source. A one-time **$5 API key** unlocks metadata enrichment — full yt-dlp metadata fetch, thumbnails, descriptions, tags, channel info, and upload dates for existing local files that don't have this data yet.

The pre-purchase flow:
1. Install and run for free
2. Deepcut scans and triages your entire library at no cost
3. You see exactly what enrichment will do before buying
4. Purchase the API key — enrichment runs immediately on everything already queued

No subscription. No account. No phoning home.

### File Management

- Default opinionated file structure, or user-defined template
- Auto-detect library path changes — prompts user to confirm new location rather than silently breaking
- No files are ever moved or renamed without explicit user action

---

## Roadmap

### Phase 1 — Foundation
- [ ] Project scaffold, Docker Compose, environment config
- [ ] SQLAlchemy models + Alembic migrations
- [ ] ffprobe local scanner service
- [ ] FastAPI routes: library CRUD, scan endpoint

### Phase 2 — Import Paths
- [ ] Existing file import (ffprobe-only, no internet)
- [ ] yt-dlp metadata-only fetch (`-j` mode)
- [ ] Triage UI — review scanned library before enrichment

### Phase 3 — Enrichment
- [ ] API key system
- [ ] Full yt-dlp metadata enrichment run
- [ ] Thumbnail and artwork download

### Phase 4 — Downloads
- [ ] Download queue with real-time progress
- [ ] Quality selection per item
- [ ] SponsorBlock integration (via yt-dlp flags)

### Phase 5 — File Management
- [ ] Custom file naming template engine
- [ ] Library path change detection
- [ ] Storage calculator with codec/quality projections

### Phase 6 — Integrations
- [ ] Jellyfin / Plex / Emby NFO export
- [ ] Kodi `.nfo` and `.strm` support
- [ ] PostgreSQL support

### Phase 7 — Polish
- [ ] Native OS installer (Linux, macOS, Windows)
- [ ] Multi-user support
- [ ] Mobile-responsive frontend

---

## Contributing

Issues and PRs are welcome. Please open an issue before starting any significant feature work so we can discuss approach and avoid duplicate effort.

---

## License

GNU Affero General Public License v3.0 — see [LICENSE](LICENSE) for details.
