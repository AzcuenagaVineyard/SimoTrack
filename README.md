# 2026 Track Meet Tracker

A simple, family-facing static webpage that tracks FAT timing opportunities for **800m and 1500m races, June through October 2026**, focused on the Northeast and Mid-Atlantic.

The page reads from `meets.csv` so the meet list can be updated weekly without touching any HTML.

## Files

| File | Purpose |
|------|---------|
| `index.html` | The full webpage — HTML, CSS, and JavaScript in one file |
| `meets.csv` | The meet list. Edit this weekly. |
| `README.md` | This file |

## Features

- Sortable columns (click any header)
- Filters for month, state, meet type, ranking, and status
- Full-text search across meet name, city, venue, and notes
- A/B/C ranking color codes (green / amber / tan)
- Entry deadlines highlighted when within 21 days (orange) or 7 days (red)
- Mobile-friendly — hides less critical columns on small screens
- All static — no backend, no database, no paid services

## Publishing to GitHub Pages

### One-time setup

1. Create a new repository on GitHub (public works fine; private requires a paid plan for Pages).
2. Upload these three files to the repository root: `index.html`, `meets.csv`, `README.md`.
3. In the repository, go to **Settings → Pages**.
4. Under **Build and deployment → Source**, select **Deploy from a branch**.
5. Set the branch to `main` (or `master`) and folder to `/ (root)`. Click **Save**.
6. Wait ~1–2 minutes. GitHub Pages will publish the site at:
   `https://<your-username>.github.io/<repo-name>/`

### Local preview

The page works in two modes:

- **Double-click `index.html`** — opens directly in your browser using an embedded copy of the CSV that lives inside `index.html`. Good for a quick look without any setup.
- **Served via a local web server or GitHub Pages** — the page fetches `meets.csv` from disk, so any edit to the CSV shows up on refresh.

The footer shows which mode is active (`Source: meets.csv` vs. `Source: embedded data (local preview)`).

For a real local server (recommended when actively editing the CSV):

```bash
cd path/to/track-tracker
python3 -m http.server 8000
```

Then visit http://localhost:8000 in your browser.

> **Heads up:** when you double-click the file, you're seeing the embedded copy, not the live `meets.csv`. To preview CSV edits without pushing to GitHub, use the local server above — or update both `meets.csv` AND the `<script type="text/csv" id="csv-fallback">` block inside `index.html`. On GitHub Pages, only `meets.csv` matters.

## Weekly update process

Each week, do the following:

1. **Open the Athletic.net calendar:**
   https://www.athletic.net/TrackAndField/Calendar

2. **Search for meets in the target states:**
   NY, PA, NJ, MA, CT, OH, WV, VA, SC

3. **For each promising meet, check:**
   - Are 800m and/or 1500m offered?
   - Is timing FAT (Fully Automatic Timing)?
   - Are unattached or club athletes allowed to enter?
   - Is she age-eligible?
   - What is the registration deadline?
   - Is there a direct registration link?

4. **Update `meets.csv`:**
   - Change `Estimated` to `Confirmed` when details are locked in
   - Replace the generic `https://www.athletic.net/TrackAndField/Calendar` link with the specific meet registration link
   - Add the real entry deadline (use `YYYY-MM-DD` format so deadline highlighting works)
   - Update the ranking (`A`, `B`, or `C`)
   - Add notes about eligibility, timing quality, or expected field strength
   - Move past meets to `Status = Completed` or remove them

5. **Commit the updated CSV to GitHub.** From the GitHub web UI: open `meets.csv`, click the pencil icon, paste/edit, and commit. Or from the command line:
   ```bash
   git add meets.csv
   git commit -m "Update meets for week of <date>"
   git push
   ```

6. **GitHub Pages automatically rebuilds** within a minute or two. Refresh the page to see the new data.

## CSV format reference

Columns must stay in this exact order with these exact headers:

```
Date,Meet Name,City,State,Venue,Type,FAT,Events,Registration Link,Distance from Buffalo,Entry Deadline,Ranking,Status,Notes
```

### Field rules

| Column | Format / accepted values |
|--------|--------------------------|
| **Date** | `YYYY-MM-DD` (required for sorting and month filter) |
| **Meet Name** | Free text. If a comma is needed, wrap the whole field in `"double quotes"`. |
| **City** | Free text |
| **State** | 2-letter code (`NY`, `PA`, etc.) |
| **Venue** | Free text |
| **Type** | `USATF`, `College Open`, `Club`, `AAU`, `Twilight HS`, `Open / All-Comers` |
| **FAT** | `Yes` or `No` |
| **Events** | e.g. `800/1500` |
| **Registration Link** | Full URL starting with `https://` |
| **Distance from Buffalo** | e.g. `390 miles` |
| **Entry Deadline** | `YYYY-MM-DD` for live deadline highlighting, or `TBD` |
| **Ranking** | `A` (best), `B` (useful), `C` (backup) |
| **Status** | `Confirmed`, `Estimated`, `Watchlist`, `Closed`, `Completed` |
| **Notes** | Free text. Wrap in `"quotes"` if commas are needed. |

### Editing tips

- **Spreadsheets work great.** Open `meets.csv` in Excel, Google Sheets, or Numbers. When saving, choose `CSV (Comma delimited)` or `Comma-separated values`.
- **Keep the header row exactly as is** — column names are matched verbatim.
- **Empty cells are fine** — they'll just render blank.
- **Test before pushing:** preview locally with `python3 -m http.server` to make sure the CSV parses cleanly.

## Customization

- **Change the page title or intro:** edit the `<header>` section near the top of `index.html`.
- **Adjust deadline thresholds:** in `index.html`, search for `days <= 7` and `days <= 21` to change the urgent/soon windows.
- **Add columns:** add the column to both the CSV header and to the `<thead>` and the `render()` function in the script. Easier: just add a note into the existing `Notes` column.
