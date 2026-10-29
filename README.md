# 🎬 Nestmovie

Nestmovie is a movie discovery website that lets users search and explore movies by title, genre, rating, and release date. Users can view movie details, discover popular and recommended movies, and save movies to a personal watchlist. It makes it easier to find movies and keep track of what to watch next.

---

## Team

| Name | ID |
|---|---|
| Yasmin Mustefa | ETS1492/17 |
| Tensaye Daniel | ETS1396/17 |
| Tesnim Abdi | ETS1404/17 |
| Wubet Tamrat | ETS1460/17 |
| Worku Wondoson | ETS1459/17 |

Who owns which part is listed in [docs/team-roles.md](docs/team-roles.md).

---

## Features at a glance

| Area | What users can do |
|---|---|
| **Accounts** | Register, log in, log out, edit profile, upload a profile picture, pick favorite genres, switch dark/light mode, save preferences |
| **Discover (Home)** | Hero section, trending carousel, featured movies, genre sections |
| **Search** | Search by title; filter by genre, rating, year, language; sort by rating, newest, oldest, A–Z; results update without reloading |
| **Movie details** | Poster, trailer, synopsis, cast, rating, release date, genre, image gallery, similar movies, rate a movie |
| **Watchlist** | Add/remove movies, mark as watched, favorites, live counter, filter and sort |

---

## Getting started

### 1. Get the project
Clone the repository and switch to the shared development branch (see [docs/workflow-and-conventions.md](docs/workflow-and-conventions.md)).

### 2. Run it with a local server (required)
The project uses **ES6 modules** and loads `data/movies.json` with `fetch`. Browsers block both when a page is opened by double-clicking the file (`file://`). Use one of these:

- **VS Code → "Live Server" extension** → right-click `index.html` → *Open with Live Server* (recommended)
- **Python:** run `python -m http.server 5500` in the project root, then open `http://localhost:5500`

### 3. Open `index.html`
This is the home page. All other pages are in the `pages/` folder.

---

## Project structure (summary)

```
nestmovie/
├── index.html          Home / Discover
├── pages/              search, movie, watchlist, login, register, profile
├── data/movies.json    all movie records
├── assets/             images and videos
├── css/                base/ · components/ · pages/
├── js/                 core/ · services/ · components/ · features/ · pages/
└── docs/               architecture, data contract, roles, workflow
```

The full annotated structure and the reasoning behind it are in [docs/architecture.md](docs/architecture.md).

---

## Documentation

Read these **before writing any code**:

| Document | Read it to learn… |
|---|---|
| [docs/architecture.md](docs/architecture.md) | Folder structure, layers, rules, how pages start, how features connect |
| [docs/data-contract.md](docs/data-contract.md) | Exact movie/user/watchlist data shape, localStorage keys, service functions, URL parameters |
| [docs/team-roles.md](docs/team-roles.md) | Who builds what, files each person owns, feature checklists |
| [docs/workflow-and-conventions.md](docs/workflow-and-conventions.md) | Git branching, pull requests, naming and coding conventions, definition of done |

---

## The five golden rules

1. **Dependencies only point downward:** pages → features/components → services → core.
2. **Features never import other features.** Shared logic goes in `services/` or `components/`.
3. **Only a service touches its own localStorage keys.** Nobody reads or writes another person's data directly.
4. **One movie card** (`js/components/movieCard.js`) is used by every page.
5. **One movie data shape**, defined in the data contract. Don't invent extra fields without updating it.

---

## Workflow in one minute

1. **Phase 0 (together):** create the skeleton, agree on the data contract, fill `movies.json` with sample movies, write the shared CSS variables.
2. **Phase 1 (parallel):** everyone builds inside their own files only, on their own branch.
3. **Phase 2 (integration):** connect the watchlist button, navbar counter, login protection, and theme across all pages.
4. **Phase 3 (polish):** responsive checks, bug fixes, README/screenshots, final demo.

---

## Progress tracker

Update this table as work is merged into `dev`.

| Part | Owner | Status |
|---|---|---|
| Project skeleton + shared CSS + `movies.json` | All | ☐ |
| Accounts, profile, theme | Person 1 | ☐ |
| Watchlist | Person 2 | ☐ |
| Search and filters | Person 3 | ☐ |
| Home / Discover + navbar + movie card | Person 4 | ☐ |
| Movie details | Person 5 | ☐ |
| Integration | All | ☐ |
| Final polish and demo | All | ☐ |

---

## Known limitations

- There is **no backend**. All data (users, watchlists, ratings) lives in the browser's `localStorage`, so it is per-browser and per-device.
- Login is **simulated on the client** and is not real security. This is acceptable for a learning project.
- `localStorage` holds about 5 MB, so profile pictures must be small.
