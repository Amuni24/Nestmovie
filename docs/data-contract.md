# Data Contract

This is the agreement between all five team members about **what data looks like** and **which functions each service exposes**. If you need to change anything here, update this file in the same pull request and tell the team.

---

## 1. Movie record (`data/movies.json`)

`movies.json` holds a list of movie records. Every record has these fields:

| Field | Type | Required | Notes |
|---|---|---|---|
| `id` | string | Yes | Unique and **never changes**, e.g. `m001`. Used in URLs and in the watchlist |
| `title` | string | Yes | |
| `releaseDate` | string | Yes | Format `YYYY-MM-DD`. The year is derived with a helper in `utils.js`, not stored separately |
| `genres` | list of strings | Yes | Values from the fixed genre list below. First genre is the "primary" one |
| `rating` | number | Yes | Average score from 0 to 10, one decimal (e.g. `7.8`) |
| `language` | string | Yes | Value from the fixed language list below |
| `runtime` | number | No | Minutes |
| `director` | string | No | |
| `poster` | string | Yes | Image path **relative to the project root** |
| `backdrop` | string | Yes | Wide image for hero and details header, root-relative path |
| `trailerUrl` | string | Yes | Embeddable video URL (e.g. a YouTube embed URL) |
| `synopsis` | string | Yes | Short description |
| `cast` | list of objects | Yes | Each entry has `name` and `character`; optional `photo` |
| `gallery` | list of strings | No | Root-relative image paths |
| `isTrending` | boolean | Yes | Used by the Home carousel |
| `isFeatured` | boolean | Yes | Used by the Home featured section |

### Fixed value lists (spelling and capitalization must match exactly)

**Genres:** Action, Adventure, Animation, Comedy, Crime, Documentary, Drama, Fantasy, Horror, Mystery, Romance, Sci-Fi, Thriller

**Languages:** English, Amharic, Spanish, French, Hindi, Korean, Japanese *(add more by agreement; one team member updates this list and the filter panel together)*

### Path rule
All image and video paths inside `movies.json` are stored **relative to the project root** (for example `assets/images/posters/inception.jpg`). Code never hard-codes `../`. Use the path helper in `core/config.js`.

### Similar movies rule (Person 5)
"Similar movies" are computed in JavaScript, not stored. Suggested scoring: shared genres first, then same language or close rating as tie-breakers, always excluding the current movie.

---

## 2. User record

Stored by `authService` (Person 1).

| Field | Type | Notes |
|---|---|---|
| `id` | string | Unique, never changes |
| `fullName` | string | |
| `email` | string | Unique, stored in lowercase |
| `passwordHash` | string | **Never plain text.** Use the browser's built-in Web Crypto hashing (no libraries) |
| `avatar` | string | Small image stored as a data URL, or the id of a default avatar |
| `favoriteGenres` | list of strings | Values from the fixed genre list |
| `preferences` | object | Settings such as default sort order; Person 1 defines the keys and documents them here |
| `createdAt` | string | ISO date-time |

**Session:** only the current user's `id` is stored as the logged-in marker. Never store the password hash in the session.

---

## 3. Watchlist item

Stored by `watchlistService` (Person 2), **separately for each user**.

| Field | Type | Notes |
|---|---|---|
| `movieId` | string | Matches `movies.json` `id` |
| `addedAt` | string | ISO date-time. Used for sorting |
| `watched` | boolean | |
| `watchedAt` | string or empty | Set when marked as watched |
| `favorite` | boolean | |

The watchlist stores only the `movieId` and state. Movie details always come from `movieService`, so there is never duplicate or outdated movie data.

---

## 4. User rating

Stored by `ratingService` (Person 5), separately for each user.

| Field | Type | Notes |
|---|---|---|
| `movieId` | string | |
| `value` | number | 1 to 5 stars |
| `ratedAt` | string | ISO date-time |

This is the **user's own rating**. It is different from the movie's average `rating` (0–10) in `movies.json`.

---

## 5. localStorage keys

All keys are declared **once** in `core/config.js`, and nobody types a key as a string anywhere else. The prefix `nestmovie:` avoids clashes with other projects on the same localhost.

| Key | Contents | Owner |
|---|---|---|
| `nestmovie:users` | List of all user records | P1 `authService` |
| `nestmovie:currentUser` | Id of the logged-in user | P1 `authService` |
| `nestmovie:theme` | `light` or `dark` (works for guests too) | P1 `preferencesService` |
| `nestmovie:watchlist:<userId>` | List of watchlist items for that user | P2 `watchlistService` |
| `nestmovie:ratings:<userId>` | List of ratings for that user | P5 `ratingService` |

---

## 6. Public service functions

These are the **only** ways other people's code may use your service. Names are fixed so everyone can code against them from day one. Implement them exactly; extra internal functions are up to you.

### `movieService` (Person 4)
| Function | Purpose |
|---|---|
| `getAllMovies` | Load (once, then cache) and return all movies |
| `getMovieById` | Return one movie by id, or nothing if not found |
| `getTrending` | Movies where `isTrending` is true |
| `getFeatured` | Movies where `isFeatured` is true |
| `getByGenre` | Movies containing a given genre |
| `getAllGenres` / `getAllLanguages` | The fixed lists above |

### `authService` (Person 1)
| Function | Purpose |
|---|---|
| `register` | Create a new account (after validation) |
| `login` / `logout` | Start or end the session |
| `getCurrentUser` | Return the logged-in user or nothing |
| `isLoggedIn` | True or false |
| `updateProfile` | Change name, avatar, favorite genres |

### `preferencesService` (Person 1)
| Function | Purpose |
|---|---|
| `getTheme` / `setTheme` | Read or change light/dark |
| `getPreferences` / `savePreferences` | User preferences |

### `watchlistService` (Person 2)
| Function | Purpose |
|---|---|
| `addToWatchlist` | Add a movie by id (requires login) |
| `removeFromWatchlist` | Remove a movie by id |
| `isInWatchlist` | True or false, used to render the right button state on cards and details |
| `toggleWatched` / `toggleFavorite` | Flip those flags |
| `getWatchlist` | All items for the current user |
| `getWatchlistCount` | Number shown in the navbar |

### `ratingService` (Person 5)
| Function | Purpose |
|---|---|
| `rateMovie` | Save the current user's rating |
| `getUserRating` | Current user's rating for a movie, if any |

### `movieCard` component (Person 4)
| Input | Meaning |
|---|---|
| A movie record | What to display |
| Display options | e.g. compact or full size, whether to show the watchlist button, whether to show the hover preview |

**Event handling convention:** buttons inside cards use `data-action` attributes (such as `add-watchlist`) and `data-movie-id`. Pages attach **one** listener to the container (event delegation) instead of one per card.

---

## 7. Error and empty-state conventions

- Services return a clear "nothing" result for not-found cases and **never crash the page**.
- Anything that reads storage or fetches data is wrapped in error handling and falls back to a safe default (empty list, light theme).
- Every list view has three visible states: **loading**, **empty** (friendly message), and **error** (message plus retry). Styles live in `css/components/states.css`.
