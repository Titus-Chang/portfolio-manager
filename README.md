# Portfolio Manager — AI Commercial Portfolio Generator

A zero-build, single-file admin dashboard for managing and showcasing your AI project portfolio.  
Built with **Vue 3 + Tailwind CSS CDN** — no Node.js, no bundler, just open and go.

---

## Quick Start

No server required. Just double-click:

1. Open **admin.html** — create / edit / delete projects visually.
2. Click **Export JS** — downloads `projects.js`.
3. Drop it next to `index.html`, overwriting the old copy.
4. Double-click **index.html** — your polished portfolio is live.

Open **quick_start.html** for the full walkthrough.

### Why two data files?

Browsers treat `file://` as an opaque origin and block `fetch`, but `<script src>` loads
fine — so the data ships in two interchangeable wrappers with identical content:

| File | Loaded via | Needs a server? |
|------|-----------|-----------------|
| `projects.js` | `<script src>` → `window.PROJECTS` | No — double-click works |
| `projects.json` | `fetch()` | Yes |

`index.html` checks `window.PROJECTS` first and falls back to fetching
`projects.json`, so both paths work from the same file. If you keep both, re-export
both after editing — otherwise the stale `.js` silently wins.

Since uploaded cover images are embedded as base64, `index.html` + `projects.js`
is a complete, self-contained portfolio you can zip and email.

### With a server

Needed only for the `projects.json` path, or when images use relative paths:

```bash
python -m http.server 8000   # or: npx serve .
# then open http://localhost:8000/index.html
```

### Workflow

```
                    ┌──Export JS────▶  projects.js   ──script src──┐
admin.html  ────────┤                                             ├──▶  index.html
                    └──Export JSON──▶  projects.json  ──fetch─────┘
   ▲                                        │
   └──────────── Import JSON ◀──────────────┘
```

> **Tip:** `admin.html` always works via `file://` — no server needed either way.

---

## Project Structure

```
product_menu/
├── quick_start.html # 5-minute onboarding guide
├── admin.html       # CRUD admin dashboard (Vue 3 + Tailwind CSS)
├── index.html   # Public-facing portfolio page
├── projects.js      # Same data as JS — enables the double-click path
├── projects.json    # Project data (exported from admin)
├── images/          # Optional — only for the relative-path image mode
├── example.html     # Visual style reference
├── input.txt        # Original requirements
└── README.md
```

---

## Features

| Feature | Description |
|---------|-------------|
| **CRUD** | Create, read, update, delete projects with a visual form + live preview |
| **Cover Images** | Drag-drop / click / paste (Ctrl+V) upload, auto-downscaled to 1600px JPEG and embedded as base64 — or point at a URL / relative path instead |
| **Storage Gauge** | Live localStorage usage meter, since embedded images eat the ~5 MB quota |
| **Custom Categories** | Add / rename / recolor / reorder categories in the admin — the set ships with the data, so the portfolio's filter buttons follow. Deleting a category in use forces a reassignment first |
| **Category Filter** | Filter by any defined category (defaults: Automation, Marketing, Enterprise, Engineering) |
| **Tags** | Dynamic add / remove with Enter key or button |
| **localStorage** | Auto-saves every change — no data loss on refresh |
| **Import JSON** | Load an existing `projects.json` into the editor |
| **Export JSON** | Download current projects as `projects.json` (for the HTTP path) |
| **Export JS** | Download `projects.js` — lets `index.html` run by double-click, no server |
| **Duplicate** | One-click project cloning |
| **Live Preview** | Real-time card preview as you type |
| **Dark Mode** | Tech-blue accent on deep dark background |
| **Spotlight FX** | Cursor-tracking glow effect on portfolio cards |
| **Responsive** | Mobile-friendly grid layout |
| **Zero Build** | Pure CDN — no npm install, no webpack, no vite |

---

## Data Schema

`projects.json` wraps two things — the category definitions and the projects:

```json
{
  "categories": [
    { "value": "automation", "label": "Automation & Orchestration", "color": "#7dd3fc" }
  ],
  "projects": [ ... ]
}
```

A bare array (no wrapper) is still accepted: categories are then derived from the
`category` values found in the projects, labelled from the slug. `projects.js` uses the
same split — `window.CATEGORIES` and `window.PROJECTS`; the latter works on its own.

### `categories[]`

| Field | Type | Values |
|-------|------|--------|
| `value` | string | Slug the projects reference. Auto-generated from the label, **immutable once created** — otherwise renaming would orphan every project using it |
| `label` | string | Display name — the portfolio's filter button text. Freely editable |
| `color` | string | Hex, e.g. `#7dd3fc`. Drives the admin's category chips (background and border alphas are derived from it) |

Array order sets the order of the portfolio's filter buttons.

### `projects[]`

```json
{
  "category": "automation",
  "metric": "-85%",
  "metricLabel": "Manual Workload",
  "title": "Autonomous Workflow System",
  "link": "https://example.com",
  "desc": "Project description here.",
  "image": "images/cover.jpg",
  "tags": ["GPT-4o API", "Zapier Agents"]
}
```

| Field | Type | Values |
|-------|------|--------|
| `category` | string | One of `categories[].value`. Defaults ship as `automation` · `marketing` · `enterprise` · `engineering`, but the set is editable |
| `metric` | string | e.g. `-85%`, `3x`, `100%` |
| `metricLabel` | string | e.g. `Manual Workload`, `CPA` |
| `title` | string | Project title |
| `link` | string | URL (used as card href in portfolio) |
| `desc` | string | Project description |
| `image` | string | *Optional.* Cover image — `data:image/…` base64, absolute URL, or relative path. Empty or unreachable → card falls back to the text-only layout |
| `tags` | string[] | Technology / keyword tags |

### Cover images

No backend means no server-side uploads, so there are two modes:

- **Embedded (default for uploads)** — the admin downscales to a 1600px long edge, re-encodes as JPEG (`MAX_EDGE` / `JPEG_QUALITY` in `admin.html`) and stores base64 in the JSON. The whole portfolio stays one portable file, images included, and even works over `file://`.
- **Referenced** — type a URL or a relative path like `images/cover.jpg` into the field below the dropzone. Keeps the JSON small; you manage the files yourself and relative paths need HTTP serving.

Embedded images share the browser's ~5 MB localStorage quota, so the admin shows a usage gauge in the nav bar. If the quota is exceeded it keeps the text data, warns, and asks you to Export immediately — the exported JSON is always complete. Note that JPEG re-encoding flattens transparency (onto `#0f172a`) and drops GIF animation; use the referenced mode to preserve either.

---

## Tech Stack

- [Vue 3](https://vuejs.org/) — CDN global build
- [Tailwind CSS](https://tailwindcss.com/) — CDN v2
- [Inter](https://rsms.me/inter/) — Google Fonts
- Vanilla JS for portfolio page (no framework dependency)

---

## License

MIT
