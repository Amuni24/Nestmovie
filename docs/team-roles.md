# Team Roles and Ownership

Fill in the **Assigned to** column in your first meeting. Each person owns the files listed for their role. If you need a change in someone else's file, ask the owner or open a pull request they review.

| Person | Area | Assigned to |
|---|---|---|
| **Person 1** | Accounts, profile, theme, preferences | @Amuni24 |
| **Person 2** | Watchlist | @Tensaye-Daniel |
| **Person 3** | Search, filters, sorting | @worku404 |
| **Person 4** | Home / Discover, navbar, movie card, movie data | @wubettamrat525-prog |
| **Person 5** | Movie details | @tesnimabdi16-netizen |

---

## Person 1: Accounts, Profile, Theme

**Branch:** `feature/auth`

### Files owned
- Pages: `pages/login.html`, `pages/register.html`, `pages/profile.html`
- CSS: `css/pages/auth.css`, `css/pages/profile.css`, `css/components/forms.css`
- Services: `js/services/authService.js`, `js/services/preferencesService.js`
- Features: everything in `js/features/auth/`
- Page scripts: `js/pages/login.js`, `register.js`, `profile.js`
- Maintains the light/dark palettes in `css/base/variables.css`

### Features
- Registration, login, logout
- Form validation
- User profile with profile picture
- Favorite genres
- Dark/light mode
- User preferences

### JavaScript / ES6 requirements
- Form validation
- Regular expressions
- Error handling
- localStorage
- DOM manipulation
- Password visibility toggle
- Theme switching
- ES6 modules
- Event handling

### Provides to the team
`authService`, `preferencesService`, `authGuard`, theme applied on every page.

### Depends on
Navbar (P4) to display login state and a theme switch button.

---

## Person 2: Watchlist

**Branch:** `feature/watchlist`

### Files owned
- Page: `pages/watchlist.html`
- CSS: `css/pages/watchlist.css`
- Service: `js/services/watchlistService.js`
- Features: everything in `js/features/watchlist/`
- Page script: `js/pages/watchlist.js`

### Features
- Add movie to watchlist
- Remove movie
- Mark as watched
- Favorites
- Watchlist counter
- Filter watchlist
- Sort watchlist

### JavaScript / ES6 requirements
- localStorage
- JSON stringify() / parse()
- Arrays and objects
- map(), filter(), find()
- Event delegation
- DOM manipulation
- ES6 modules

### Provides to the team
`watchlistService` (used by movie cards, details page, navbar counter).

### Depends on
`authService` and `authGuard` (P1), `movieService` and `movieCard` (P4).

---

## Person 3: Search and Filters

**Branch:** `feature/search`

### Files owned
- Page: `pages/search.html`
- CSS: `css/pages/search.css`, `css/components/filter-panel.css`, `css/components/states.css`
- Features: everything in `js/features/search/`
- Page script: `js/pages/search.js`

### Features
- Search by movie title
- Multiple filters: genre, rating, release year, language
- Sort by: rating, newest, oldest, A–Z
- Search results update without reloading
- Reads URL parameters so other pages can link into a pre-filtered search

### HTML
- Search form
- Filter controls
- Results container
- Sorting controls

### CSS
- Advanced filter panel
- Responsive results grid
- Loading and empty-result states
- Animated cards

### JavaScript / ES6 requirements
- filter(), sort(), map()
- Event listeners
- Dynamic DOM rendering
- Search algorithm
- Multiple filters working together
- ES6 modules
- Optional debounce for search (helper lives in `core/utils.js`)

### Provides to the team
Loading, empty, and error state styles used by every list page.

### Depends on
`movieService` and `movieCard` (P4).

---

## Person 4: Home / Discover, Navbar, Movie Card, Movie Data

**Branch:** `feature/home`

**Goal:** users discover movies.

### Files owned
- Page: `index.html`
- CSS: `css/pages/home.css`, `css/components/navbar.css`, `css/components/footer.css`, `css/components/movie-card.css`
- Service: `js/services/movieService.js`
- Components: `js/components/navbar.js`, `footer.js`, `movieCard.js`
- Features: everything in `js/features/home/`
- Page script: `js/pages/home.js`
- Structure of `data/movies.json` (everyone adds movies)

### HTML
- Hero section with movie image or video
- Trending movie carousel
- Featured movies
- Genre sections

### CSS
- Responsive Grid/Flexbox
- Animations and transitions
- Hover movie previews
- Responsive navigation

### JavaScript / ES6 requirements
- Movie data using arrays of objects
- map(), filter(), find()
- Dynamic movie-card generation
- Carousel controls
- Genre switching
- DOM manipulation
- ES6 arrow functions, template literals, destructuring

### Provides to the team
`movieService`, `movieCard`, navbar and footer. **This is the most depended-on role, so deliver the movie data, `movieService`, and a first version of `movieCard` in the first days.**

### Depends on
`authService` (P1) and `watchlistService` (P2) for the navbar's login state and counter. Use agreed function names so work can begin before they are finished.

---

## Person 5: Movie Details

**Branch:** `feature/details`

### Files owned
- Page: `pages/movie.html`
- CSS: `css/pages/movie-details.css`, `css/components/modal.css`
- Service: `js/services/ratingService.js`
- Component: `js/components/modal.js`
- Features: everything in `js/features/details/`
- Page script: `js/pages/movie-details.js`

### Features
- Movie poster
- Trailer/video
- Synopsis
- Cast
- Rating
- Release date
- Genre
- Movie images/gallery
- Similar movies

### JavaScript requirements
- Dynamic movie selection (from the `id` URL parameter)
- Trailer modal
- Image gallery
- Rating interaction
- "Add to Watchlist"
- Similar-movie recommendation using JavaScript

### Provides to the team
Reusable `modal` component, `ratingService`.

### Depends on
`movieService` and `movieCard` (P4), `watchlistService` (P2).

---

## Dependency order (who unblocks whom)

1. **First:** `movies.json`, `movieService`, `config.js`, `storage.js`, `variables.css` (agreed in Phase 0).
2. **Early:** P4's `movieCard` + navbar shell, P1's `authService`, P2's `watchlistService`. Others can code against the agreed function names in [data-contract.md](data-contract.md) while these are being built.
3. **Last:** the connections between features (Phase 2 integration).
