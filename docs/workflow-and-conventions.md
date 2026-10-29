# Workflow and Conventions

How we work together so that five people's code fits into one project.

---

## 1. Git workflow

### Branches
| Branch | Purpose |
|---|---|
| `main` | Stable, demo-ready only. Updated at milestones |
| `dev` | Integration branch. All finished work lands here |
| `feature/auth` | Person 1 |
| `feature/watchlist` | Person 2 |
| `feature/search` | Person 3 |
| `feature/home` | Person 4 |
| `feature/details` | Person 5 |

### Daily routine
1. Update your branch from `dev` **before you start working**.
2. Work only in files you own (see [team-roles.md](team-roles.md)).
3. Commit small and often.
4. Open a **pull request into `dev`** when a feature part works.
5. Someone else reviews and merges. **Nobody merges their own pull request.**

### Commit messages
Start with a type, then a short description:
- `feat: add password visibility toggle`
- `fix: watchlist counter not updating after remove`
- `style: responsive grid for search results`
- `docs: update data contract`
- `refactor: move debounce into utils`

### Pull request checklist
- [ ] Only touches files I own (or the change to a shared file was agreed)
- [ ] Works when run through Live Server, with no errors in the browser console
- [ ] Tested on desktop and mobile width
- [ ] Tested logged in and logged out (where relevant)
- [ ] Docs updated if I changed a contract

### Merge conflicts
Conflicts should be rare if everyone stays in their own files. When they happen in a shared file, **the two people involved resolve it together**, not alone.

### What not to commit
- Huge video files. Keep the hero video short and compressed.
- Editor settings and OS junk files (add them to `.gitignore`).

---

## 2. Naming conventions

| Thing | Convention | Example |
|---|---|---|
| HTML / CSS files | kebab-case | `movie-details.css` |
| JavaScript files | camelCase | `watchlistService.js` |
| Image files | lowercase kebab-case, named after the movie | `the-dark-knight-poster.jpg` |
| CSS classes | lowercase kebab-case with a component or page prefix | `movie-card__title`, `filter-panel__group` |
| HTML ids | kebab-case, used for JavaScript hooks only | `search-form` |
| Data attributes | kebab-case | `data-action`, `data-movie-id` |
| JS functions and variables | camelCase | `getWatchlistCount` |
| JS constants | UPPER_SNAKE_CASE | `STORAGE_KEYS` |

**CSS classes must be prefixed with the component or page name** so one person's `.title` never breaks another's page.

---

## 3. Coding conventions

### JavaScript
- **ES6 modules only:** `import` / `export`. No global variables, no scripts that depend on load order.
- **One `<script type="module">` per HTML page**, pointing at that page's controller in `js/pages/`.
- Prefer **named exports**.
- Use `const` by default, `let` when needed, never `var`.
- Use arrow functions, template literals, destructuring, and array methods (`map`, `filter`, `find`, `sort`) where they fit.
- **No inline JavaScript** in HTML (`onclick="..."`). Attach listeners in JS.
- Use **event delegation** for lists of cards and buttons.
- Wrap storage access and `fetch` in **try/catch**. Never let a failure blank the page.
- Services never touch the DOM. Features and components never touch localStorage.
- Always **escape or safely insert** user-provided text (names, etc.) into the page. Don't build HTML from raw user input.

### HTML
- Semantic elements (`header`, `nav`, `main`, `section`, `footer`, `form`, `button`).
- Every image has an `alt` attribute. Every form input has a `label`.
- Same `<head>` setup on every page: viewport meta tag, base CSS files in the same order, then component CSS, then the page CSS.
- No inline styles.

### CSS
- Use the tokens in `css/base/variables.css` for colors, spacing, and fonts. **No hard-coded colors** inside component or page CSS. That is what makes dark mode work.
- Mobile-first: write the small-screen layout first, then use `min-width` media queries.
- Use Flexbox and Grid for layout.
- Respect the shared breakpoints (agree on them in Phase 0 and note them at the top of `variables.css`).

### Accessibility basics
- Everything clickable works with the keyboard.
- Modals can be closed with `Esc` and return focus when closed.
- Text has enough contrast in **both** light and dark themes.

---

## 4. Definition of done

A feature is done only when:

1. It does everything in your checklist in [team-roles.md](team-roles.md).
2. It handles **loading, empty, and error** states.
3. It works at mobile, tablet, and desktop widths.
4. It works in light and dark themes.
5. It works logged in and logged out, where relevant.
6. There are no errors or warnings in the browser console.
7. It follows the layer rules in [architecture.md](architecture.md).
8. It is reviewed and merged into `dev`.

---

## 5. Communication

- **Phase 0 meeting (everyone, required):** agree the data contract, genre/language lists, breakpoints, and color tokens.
- **Short sync every few days:** what's done, what's blocked, what you need from someone else.
- **Need something from another person's file or service?** Ask early, naming the function or change you need. Don't edit their file.
- **Changing a contract** (data shape, function name, storage key, URL parameter)? Update [data-contract.md](data-contract.md) or [architecture.md](architecture.md) in the same pull request and announce it to the team.
