# CLAUDE.md — Mi Biblioteca (`web/library`)

Personal library app: books + loans. **One self-contained HTML file** (`library.html`) that the user opens by double-click on a PC or phone. No build, no dependencies, no network, no server. UI is in **Spanish**. The root `CLAUDE.md` applies (Android/iOS rules there do not; this is a web-only project outside `android/` and `ios/`).

## Product requirements (from the user)
- List of books, add book, loans with status **Prestado / Devuelto / Retrasado**, loan date, expected return date and actual return date.
- Portable and local: all data stays on the user's machine. They currently use Excel and dislike it, so CSV import/export is a first-class feature.
- Must work well on a **phone** (user tests from the phone's file viewer) and on a **PC**.
- Library may hold **hundreds of books**: never use a `<select>` or a long list for choosing a book.

## Architecture (all in `library.html`)
- Plain JS, no framework. State is one object `db = { books: [], loans: [] }`; `save()` persists it, `render()` redraws everything.
  - Book: `{ id, title, author, note, demo? }`
  - Loan: `{ id, bookId, who, date, due, returned|null, demo? }` (dates are `YYYY-MM-DD` strings)
- **Status is computed, never stored** (`status(l)`): `returned` if `l.returned`, else `late` if `l.due < today()`, else `loaned`. Labels in `LABEL`.
- A book with an open loan (`activeLoan`) cannot be lent again or deleted.
- Date helpers use **local** dates (`ymd`, `today`, `addDays`, `daysBetween` at noon) — do not use `toISOString()` for "today" (timezone bug).

## Storage
1. `localStorage` key `library.v1` (always; per browser + file location).
2. Optional **File System Access API** (Chrome/Edge desktop only): "Nuevo/Abrir archivo de datos" links a JSON file, handle kept in IndexedDB (`library-fs`); every `save()` writes to it (debounced 250 ms). After a restart the browser may need a click on "Reconectar archivo" (permission rule). Not available in Firefox/Safari/phone viewers.
3. Manual JSON backup/restore and CSV export/import in the **⚙ Datos** sheet.

CSV: export has a `sep=,` first line + BOM so Excel opens it in any locale. Import (`importCSV`) auto-detects `,` `;` or tab, matches headers through `ALIASES` (English + Spanish, accent-insensitive via `norm`), accepts `YYYY-MM-DD` or day-first `DD/MM/YYYY`, creates missing books by title, and treats a file with a "who/Prestado a" column as loans. Add aliases there if the user's real Excel headers differ (we have not seen their sheet yet).

## UI decisions worth keeping
- **No `alert/confirm/prompt`.** The phone's file viewer silently blocks them (this broke "Marcar devuelto" once). Use `dlgAlert`, `dlgConfirm` (Promise) and the bottom-sheet modal `openSheet/closeSheet`.
- Mobile = cards, floating **+** button (adds book or loan by active tab), bottom-sheet forms, 16px inputs (avoid iOS zoom), 44px touch targets.
- Desktop = **sortable tables** (`SORT`, `th`, `sortBy`) in `body.desk`. Header button **🖥 PC / 📱 Móvil** toggles (`toggleView`, stored in `localStorage` `library.view`); on a phone, PC mode sets the viewport to `width=1100` so the user can preview the desktop layout. Default = desktop when `screen.width >= 1000`.
- Loan form picks the book with a **search box** (`#lSearchBook`): shows nothing until typing, max 6 results, accent-insensitive over title/author/notes. Borrower input has a `<datalist>` of past borrowers.
- Light/dark follows `prefers-color-scheme` through CSS variables on `:root`.
- Sample data: **⚙ Datos → Cargar libros y préstamos de ejemplo** (72 books, 20 loans, relative to today, all statuses) flagged `demo: true`; "Quitar los datos de ejemplo" removes only those.

## Testing
No test suite. Verify with Playwright (preinstalled; `require('playwright')`) against `file:///home/user/apps/web/library/library.html`:
- Mobile: `devices['Pixel 7']`; desktop: viewport 1440×900, `deviceScaleFactor: 2` for screenshots.
- Listen to `pageerror` and fail on any native `dialog` event.
- Seed quickly with `page.evaluate(d => { db = d; save(); render(); }, data)` or the demo button.
- Check: no horizontal overflow on mobile (`scrollWidth > innerWidth`), return flow, CSV round-trip (semicolon + Spanish headers), reload persistence.

## Conventions
- Keep it a single file with no external assets or CDN links (must work offline from `file://`).
- All user-visible text in Spanish. Escape every interpolated string with `esc()`.
- Commit on the session branch and push; do not open PRs or subscribe to PR activity unless asked.

## Ideas not done yet
- Edit an existing book/loan (only add/delete today).
- Spreadsheet-like inline editing; choose visible columns; extra fields (category, ISBN as its own column).
- "Due soon" highlight, loan history per book/person, printable list.
- Table view is desktop only; phone always shows cards.
