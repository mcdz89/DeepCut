# Deepcut

**A self-hosted video library manager built for people who actually have video files.**

Deepcut is a metadata-first video library manager that works with 1,000+ sites via yt-dlp. Import files you already have, build a catalog of content you don't have yet, and enrich everything with full metadata — without re-downloading a single thing.

---

## Why Deepcut

Every existing self-hosted video tool is YouTube-only, download-first, and ignores the files you already have sitting in folders. Deepcut was built to fill those gaps.

| | TubeArchivist | TubeSync | Youtarr | **Deepcut** |
|---|---|---|---|---|
| Multi-site (1,000+ sources) | ✗ | ✗ | ✗ | ✅ |
| Import existing files | ✗ | ✗ | ✗ | ✅ |
| Metadata-only entries (no download required) | ✗ | ✗ | ✗ | ✅ |
| Flexible file naming | ✗ | Partial | ✗ | ✅ |
| SQLite / PostgreSQL choice | ✗ | ✓ | ✗ | ✅ |
| Docker + native OS installer | Docker only | Docker only | Docker only | ✅ |
| Low RAM footprint | ✗ (4GB+) | Moderate | Moderate | ✅ |
| Arr stack integration | Partial | Partial | Partial | ✅ |

---

## Features

### Library Management
- **Metadata-first catalog** — add content to your library without downloading anything. `file_path` is null until you decide you want the file.
- **Import existing files** — point Deepcut at a folder of videos you already have. ffprobe scans them locally with no internet required.
- **1,000+ supported sites** — anything yt-dlp supports, Deepcut supports.
- **Multi-platform source separation** — YouTube content, Vimeo content, and local imports all tracked independently with their own extractor context.

### Metadata & Enrichment
- **Free local scan** — ffprobe extracts codec, resolution, duration, bitrate, and container format from every local file at no cost.
- **Paid metadata enrichment** — a one-time $5 API key unlocks full yt-dlp metadata: titles, descriptions, thumbnails, tags, channel info, upload dates, and more.
- **Pre-purchase triage run** — before you buy, Deepcut scans and identifies your entire library so you can see exactly what enrichment will do before committing.

### File Handling
- **Default or custom file structure** — use Deepcut's opinionated naming scheme or define your own template.
- **Auto-detect library changes** — if files move, Deepcut detects the change and prompts you to confirm the new location rather than silently breaking.
- **Storage calculator** — see real disk usage projections before downloading, with codec and quality comparisons.

### Integrations
- **Arr stack** — Jellyfin, Plex, Emby, and Kodi compatible NFO output and library paths.
- **SponsorBlock** — optional segment removal on download via yt-dlp's built-in SponsorBlock support.

---

## How It Works

Deepcut has three distinct import paths:

**New downloads**
yt-dlp fetches metadata and streams → ffmpeg processes the file → ffprobe validates and writes technical specs to the database.

**Existing files**
ffprobe scans the file locally → available metadata written to the database immediately → missing fields queued for enrichment on API key purchase.

**Metadata-only entries**
yt-dlp `-j` fetches full JSON without downloading → database entry created with all available metadata → `file_path` is null → download button available when ready.

---

## Tech Stack

| Layer | Technology |
|---|---|
| Backend | Python 3.11 + FastAPI |
| Frontend | React |
| Database | SQLite (default) or PostgreSQL |
| Processing | yt-dlp · ffmpeg-python · ffprobe |
| Deployment | Docker Compose · native OS installer |

---

## Getting Started

### Docker (recommended)

```bash
git clone https://github.com/yourusername/deepcut
cd deepcut
cp .env.example .env
docker compose up -d
```

Open `http://localhost:8000` in your browser.

### Native Install

> Requirements: Python 3.11+, ffmpeg, ffprobe

```bash
git clone https://github.com/yourusername/deepcut
cd deepcut
pip install -e .
deepcut serve
```

---

## Configuration

All configuration lives in `.env`. Key options:

```env
# Database
DATABASE_URL=sqlite:///./deepcut.db
# DATABASE_URL=postgresql://user:password@localhost/deepcut

# Library
LIBRARY_PATH=/media/videos
FILE_TEMPLATE={extractor}/{channel}/{upload_date}_{title}.{ext}

# API
DEEPCUT_API_KEY=your_key_here
```

---

## Project Structure

```
deepcut/
├── backend/
│   ├── app/
│   │   ├── main.py          # FastAPI entry point
│   │   ├── models.py        # SQLAlchemy models
│   │   ├── database.py      # DB connection + migrations
│   │   ├── routers/
│   │   │   ├── library.py   # Library CRUD
│   │   │   ├── scan.py      # Scan + import endpoints
│   │   │   ├── enrich.py    # API key enrichment
│   │   │   └── download.py  # yt-dlp download queue
│   │   └── services/
│   │       ├── ytdlp.py     # yt-dlp wrapper
│   │       ├── ffprobe.py   # ffprobe wrapper
│   │       └── ffmpeg.py    # ffmpeg-python wrapper
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

## Roadmap

- [ ] Core library CRUD + SQLite support
- [ ] ffprobe local scanner
- [ ] yt-dlp metadata-only fetch
- [ ] Enrichment API + key system
- [ ] React frontend — library view
- [ ] React frontend — triage UI
- [ ] Download queue with progress
- [ ] File template engine
- [ ] Jellyfin / Plex NFO export
- [ ] PostgreSQL support
- [ ] Native OS installer
- [ ] Storage calculator
- [ ] SponsorBlock integration
- [ ] Multi-user support

---

## Contributing

Deepcut is in early development. Issues and PRs welcome. Please open an issue before starting any large feature work.

---

## License

MIT
