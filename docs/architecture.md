# Architecture

This document defines how Nestmovie is organized. Everyone on the team follows it so that five people can work in parallel without breaking each other's code.

---

## 1. Overview

| Decision | Choice | Reason |
|---|---|---|
| Technology | HTML, CSS, JavaScript (ES6 modules) only | Project requirement |
| Page model | **Multi-page site**, one HTML file per screen | Each person owns their own pages, so fewer merge conflicts |
| JavaScript organization | ES6 modules, **one entry script per page** | Clear separation, matches the learning goals |
| Persistence | `localStorage` | There is no backend |
| Movie data | One `data/movies.json` file, read through one service | Single source of truth, and a real loading state to design for |
| Running locally | Needs a local server (Live Server) | Modules and `fetch` don't work over `file://` |

---

## 2. Folder structure

```
nestmovie/
│
├── index.html                      Home / Discover                (P4)
├── README.md
├── .gitignore
│
├── pages/
│   ├── search.html                 Search + filters               (P3)
│   ├── movie.html                  Movie details (?id=...)        (P5)
│   ├── watchlist.html              My watchlist                   (P2)
│   ├── login.html                  Login                          (P1)
│   ├── register.html               Registration                   (P1)
│   └── profile.html                Profile + preferences          (P1)
│
├── data/
│   └── movies.json                 All movie records              (everyone adds data)
│
├── assets/
│   ├── images/
│   │   ├── posters/
│   │   ├── backdrops/
│   │   ├── gallery/
│   │   ├── avatars/                Default profile pictures
│   │   └── icons/                  Logo, favicon, UI icons
│   └── videos/                     Hero video (keep it short and compressed)
│
├── css/
│   ├── base/
│   │   ├── reset.css
│   │   ├── variables.css           Colors, spacing, fonts, light + dark tokens
│   │   ├── typography.css
│   │   └── layout.css              Container, grid helpers, utilities
│   ├── components/
│   │   ├── navbar.css              (P4)
│   │   ├── footer.css              (P4)
│   │   ├── buttons.css             (shared)
│   │   ├── forms.css               (P1)
│   │   ├── movie-card.css          (P4)
│   │   ├── modal.css               (P5)
│   │   ├── filter-panel.css        (P3)
│   │   └── states.css              Loading, empty, error      (P3)
│   └── pages/
│       ├── home.css                (P4)
│       ├── search.css              (P3)
│       ├── movie-details.css       (P5)
│       ├── watchlist.css           (P2)
│       ├── auth.css                (P1)
│       └── profile.css             (P1)
│
├── js/
│   ├── core/                       Shared foundation, changed rarely
│   │   ├── config.js               Storage keys, constants, path helpers
│   │   ├── storage.js              Safe localStorage wrapper (JSON + error handling)
│   │   ├── utils.js                Debounce, query-param reader, formatters
│   │   └── app.js                  Bootstrap that runs on every page
│   │
│   ├── services/                   Business logic and data access
│   │   ├── movieService.js         Load/cache movies, find by id        (P4)
│   │   ├── authService.js          Register, login, logout, current user (P1)
│   │   ├── preferencesService.js   Theme, favorite genres, settings      (P1)
│   │   ├── watchlistService.js     Add, remove, watched, favorite, count (P2)
│   │   └── ratingService.js        User ratings per movie                (P5)
│   │
│   ├── components/                 Reusable UI pieces
│   │   ├── navbar.js               Nav, login state, watchlist counter   (P4)
│   │   ├── footer.js               (P4)
│   │   ├── movieCard.js            ONE card used everywhere              (P4)
│   │   └── modal.js                Generic modal                         (P5)
│   │
│   ├── features/                   Feature-specific logic, grouped by owner
│   │   ├── auth/                   (P1)
│   │   │   ├── validators.js       Regex rules
│   │   │   ├── passwordToggle.js
│   │   │   ├── profilePicture.js
│   │   │   ├── themeSwitcher.js
│   │   │   └── authGuard.js        Redirects if not logged in
│   │   ├── watchlist/              (P2)
│   │   │   ├── watchlistRender.js
│   │   │   ├── watchlistFilters.js
│   │   │   └── watchlistSort.js
│   │   ├── search/                 (P3)
│   │   │   ├── searchEngine.js     Title matching algorithm
│   │   │   ├── filters.js          Genre, rating, year, language
│   │   │   ├── sorters.js          Rating, newest, oldest, A–Z
│   │   │   └── searchUI.js         Form, panel, results rendering
│   │   ├── home/                   (P4)
│   │   │   ├── hero.js
│   │   │   ├── carousel.js
│   │   │   └── genreTabs.js
│   │   └── details/                (P5)
│   │       ├── trailerModal.js
│   │       ├── gallery.js
│   │       ├── ratingWidget.js
│   │       └── similarMovies.js
│   │
│   └── pages/                      One entry script per HTML page
│       ├── home.js                 (P4)
│       ├── search.js               (P3)
│       ├── movie-details.js        (P5)
│       ├── watchlist.js            (P2)
│       ├── login.js                (P1)
│       ├── register.js             (P1)
│       └── profile.js              (P1)
│
└── docs/
    ├── architecture.md             This file
    ├── data-contract.md
    ├── team-roles.md
    └── workflow-and-conventions.md
```

---

## 3. Layered architecture

```
┌──────────────────────────────────────────────┐
│  HTML pages + CSS                            │  What the user sees
├──────────────────────────────────────────────┤
│  js/pages/*        Page controllers          │  Wire one page together
├──────────────────────────────────────────────┤
│  js/features/*     Feature logic             │  Filtering, carousel, gallery…
│  js/components/*   Reusable UI               │  Navbar, movie card, modal
├──────────────────────────────────────────────┤
│  js/services/*     Data + business rules     │  Auth, watchlist, movies, ratings
├──────────────────────────────────────────────┤
│  js/core/*         Foundation                │  Storage wrapper, config, utils
├──────────────────────────────────────────────┤
│  localStorage   +   data/movies.json         │  Persistence
└──────────────────────────────────────────────┘
```

### Layer responsibilities

| Layer | Does | Must NOT |
|---|---|---|
| **Pages** (`js/pages`) | Read URL params, call services, hand data to features and components, attach top-level listeners | Contain business rules or long logic |
| **Features** (`js/features`) | Logic and UI behavior for one feature (filtering, carousel, gallery, validation) | Import another feature; touch localStorage directly |
| **Components** (`js/components`) | Build and return reusable UI (card, navbar, modal) | Know which page they're on |
| **Services** (`js/services`) | Own data and rules (auth, watchlist, movies, ratings); the only code allowed to read/write their own storage keys | Touch the DOM |
| **Core** (`js/core`) | Storage wrapper, constants, helper functions | Import anything from other layers |

### The rules

1. **Dependencies only point downward.** Pages use features/components/services. Features/components use services/core. Services use core. Core uses nothing.
2. **Features never import other features.** If two people need the same thing, move it into `services/` or `components/` and tell the team.
3. **Only a service touches its own localStorage keys.** Nobody reads or writes the watchlist key directly. They call `watchlistService`.
4. **Services never touch the DOM.** Only features, components, and pages do.
5. **One movie card.** Home, search, watchlist, and similar movies all use `movieCard.js`.
6. **One movie data shape**, defined in [data-contract.md](data-contract.md).
7. **Don't edit files owned by someone else.** Open a pull request or ask the owner (see [team-roles.md](team-roles.md)).

---

## 4. Page startup flow

Every HTML page loads **one** module script, its own page controller. Each page then follows the same steps:

1. `core/app.js` applies the saved theme (before content shows, to avoid a flash).
2. It renders the navbar and footer, including login state and the watchlist counter.
3. If the page is protected, `authGuard` redirects unauthenticated users to login.
4. The page controller loads movies through `movieService` (showing a loading state).
5. The page controller passes the data to its feature modules and components to render.
6. Event listeners are attached (using event delegation for lists of cards).

---

## 5. Pages and URL contract

Pages talk to each other through **URL query parameters**, so any page can link to any other without importing its code.

| Page | URL | Parameters | Access |
|---|---|---|---|
| Home | `index.html` | none | Public |
| Search | `pages/search.html` | `q`, `genre`, `rating`, `year`, `language`, `sort` | Public |
| Movie details | `pages/movie.html` | `id` (required) | Public |
| Watchlist | `pages/watchlist.html` | none | **Login required** |
| Login | `pages/login.html` | `redirect` (optional, where to go after login) | Public |
| Register | `pages/register.html` | none | Public |
| Profile | `pages/profile.html` | none | **Login required** |

Examples of how pages link to each other:
- A genre section on Home links to Search with the `genre` parameter set.
- The hero search or navbar search box links to Search with the `q` parameter set.
- Any movie card links to Movie details with the `id` parameter set.
- A protected page links unauthenticated users to Login with the `redirect` parameter set to the page they wanted.

**Sort values** (use exactly these): `rating`, `newest`, `oldest`, `az`.

---

## 6. Integration points (where features meet)

| Integration | Provided by | Used by |
|---|---|---|
| "Add to Watchlist" button | `watchlistService` (P2) | Movie card (P4), details page (P5) |
| Watchlist counter in navbar | `watchlistService` (P2) | Navbar (P4) |
| Login state in navbar | `authService` (P1) | Navbar (P4) |
| Login protection | `authGuard` (P1) | Watchlist page (P2), profile page (P1) |
| Dark/light theme on every page | `preferencesService` (P1) via `core/app.js` | All pages |
| Movie data | `movieService` (P4) | Home, search, details, watchlist |
| Movie card UI | `movieCard.js` (P4) | Home, search, watchlist, similar movies |
| Modal | `modal.js` (P5) | Trailer (P5); any page that needs one |
| Watchlist per user | User ID from `authService` (P1) | `watchlistService` (P2) |
| Favorite genres influence | `preferencesService` (P1) | Optional: Home highlights (P4) |

```
P1 auth/theme      ──▶ authService, preferencesService, authGuard
     ▲                     used by: navbar, watchlist page, profile page, every page (theme)
     │
P2 watchlist       ──▶ watchlistService
     ▲                     called by: movieCard (P4), details page (P5), navbar counter
     │
P3 search          ──▶ uses movieService + movieCard
P4 home            ──▶ owns movieCard, navbar, movieService, movies.json structure
P5 details         ──▶ uses movieService, watchlistService, modal, movieCard
```

---

## 7. Shared files and who may change them

| File / folder | Owner | Change policy |
|---|---|---|
| `js/core/*` | Shared | Any change needs a pull request reviewed by at least one other member |
| `css/base/variables.css` | P1 maintains light/dark palettes | Team agrees on token names; additions are fine, renames need approval |
| `css/base/*` (others) | Shared | Same as core |
| `data/movies.json` | P4 owns the structure; everyone adds movies | Never change field names without updating the data contract |
| `js/components/movieCard.js` | P4 | Others request changes via pull request or issue |
| `js/services/*` | Each service's owner | Others call the public functions listed in the data contract |
| `docs/*` | Shared | Update docs in the same pull request as the change |

---

## 8. Build phases

| Phase | Who | Output |
|---|---|---|
| **0. Foundation** | All together, one session | Folder skeleton, data contract agreed, `variables.css`, `config.js`, `storage.js`, 15–20 sample movies in `movies.json`, shared navbar placeholder |
| **1. Parallel build** | Each person | Their own pages, features, and services on their own branch |
| **2. Integration** | All | Watchlist button, counter, auth guard, theme, URL links between pages all working together |
| **3. Polish** | All | Responsive testing, empty/error states, accessibility pass, bug fixes, screenshots, final demo |

---

## 9. Technical notes and limitations

- **Local server required.** ES modules and `fetch` fail over `file://`. Use Live Server.
- **Paths from the `pages/` folder.** Pages inside `pages/` are one level deeper than `index.html`. HTML links to CSS and assets use `../`, and `core/config.js` provides a helper so asset paths stored in `movies.json` (always relative to the project root) resolve correctly from any page.
- **localStorage limit is about 5 MB.** Keep profile pictures small (restrict file size, or downscale before saving).
- **Client-side login is not real security.** It is acceptable for this project. Passwords should at least not be stored as plain text (see the data contract).
- **Always wrap storage and `fetch` operations in error handling.** Storage can be full or disabled, and fetch can fail.
- **Guests are allowed.** Browsing, searching, and viewing details work without logging in. Only the watchlist and profile require login.
