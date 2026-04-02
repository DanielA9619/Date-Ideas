# Date Ideas

A GitHub Pages site to discover and track date ideas together.

**Live site:** [daniela9619.github.io/Date-Ideas](https://daniela9619.github.io/Date-Ideas/)

---

## Architecture

The site is a single HTML page (`index.html`) that reads from two JSON data files. There is no build step, no frameworks — just vanilla HTML/CSS/JS.

```
index.html              ← Rendering shell (HTML + CSS + JS)
recommendations.json    ← Date ideas data (AI updates this)
done-dates.json         ← Completed dates log (user + AI write here)
CLAUDE.md               ← Instructions the AI reads automatically
README.md               ← This file
```

### How data flows

```
recommendations.json ──fetch──→ index.html renders Recommendations tab
done-dates.json ─────fetch──→ index.html renders Done List tab
                     ←──GitHub API PUT── form submit (with token)
localStorage ────────────────→ checkbox states + theme preference
```

---

## index.html — Code Structure

The file has three sections: **CSS** (styles), **HTML** (structure), and **JS** (logic).

### CSS (lines ~9–460)

| Section | What it styles |
|---------|---------------|
| `:root` / `[data-theme="dark"]` | CSS custom properties for light (cream `#FFF9F2`) and dark (`#1a1a1a`) themes |
| Base / layout | body, container, header, tabs, sub-tabs |
| `.theme-toggle` | Sun/moon toggle button in header |
| `.date-card` | Recommendation cards (checkbox, name, price, desc, detail pills) |
| `.feedback-btns` / `.fb-btn` | Thumbs up/down buttons on recommendation cards |
| `.fb-comment-btn` / `.fb-comment-row` | Comment button and expandable input row on cards |
| `.detail-free` / `.detail-ticket` | Green "no reservation" and orange "tickets needed" tags |
| `.add-form` | The "Log a Date" form (inputs, star rating, buttons) |
| `.done-entry` | Done list entries (activity, meta row, edit/delete buttons) |
| `.stars` | Star rating picker (uses CSS reverse-order trick for hover) |
| `.setup-box` | GitHub token setup collapsible |
| `.stats` | Stats dashboard (total, avg rating, categories) |
| `.toast` | Bottom notification popup |

### HTML (lines ~462–570)

| Element | Purpose |
|---------|---------|
| `<header>` | Title + subtitle (date range, set dynamically) + theme toggle button |
| `.tabs` | Two tab buttons: Recommendations / Done List |
| `#recs` | Recommendations panel with sub-tabs: |
| → `.sub-tabs` | "This Period" / "Whenever" toggle |
| → `#scheduled` | Date-specific ideas (filled by JS) |
| → `#whenever` | Anytime ideas by category (filled by JS) |
| `#done` | Done List panel containing: |
| → `#setup-box` | Collapsible GitHub token setup |
| → `.add-form` | Form: date, activity, stars, category, location, price, notes |
| → `#done-list` | List container — filled by JS from `done-dates.json` |
| → `#stats` | Stats grid (hidden until entries exist) |

### JavaScript (lines ~572–end)

#### Constants & state
- `REPO_OWNER`, `REPO_NAME`, `FILE_PATH`, `BRANCH` — GitHub API target
- `allEntries` — array of done date objects (source of truth at runtime)
- `fileSha` — current SHA of `done-dates.json` (needed for GitHub API updates)
- `editingIndex` — which entry is being edited (`-1` = adding new)
- `recsData` — full recommendations.json object (kept in memory for feedback saves)
- `recsSha` — current SHA of `recommendations.json`

#### Key functions

| Function | What it does |
|----------|-------------|
| **Theme** | |
| `getPreferredTheme()` | Returns saved theme or detects system preference (`prefers-color-scheme`) |
| `applyTheme(theme)` | Sets `data-theme` attribute, updates toggle button icon (sun/moon) |
| `toggleTheme()` | Switches theme and saves to localStorage |
| `showTab(id, btn)` | Switches between Recommendations and Done List tabs |
| `showSubTab(id, btn)` | Switches between "This Period" and "Whenever" sub-tabs within Recommendations |
| `showToast(msg, isError)` | Shows a notification at the bottom of the screen |
| **GitHub connection** | |
| `getToken()` | Reads GitHub PAT from localStorage |
| `saveToken()` | Saves PAT to localStorage, updates UI |
| `removeToken()` | Removes PAT, updates UI |
| `updateSetupUI()` | Toggles setup box between "connected" and "setup" states |
| **GitHub API helpers** | |
| `ghGet(path)` | Shared GET helper — fetches file from GitHub Contents API, returns JSON with `sha` and `content` |
| `ghPut(path, content, sha, msg)` | Shared PUT helper — commits file update via GitHub Contents API using stored token |
| `fetchDoneFromGitHub()` | Uses `ghGet` to load `done-dates.json`, stores `fileSha` |
| `saveDoneToGitHub(entries)` | Uses `ghPut` to commit updated `done-dates.json` |
| **Recommendations** | |
| `toggleRec(cb)` | Toggles checkbox, saves state to localStorage |
| `reservationTag(type)` | Returns HTML for "none"/"tickets"/"required" tag |
| `escHtml(str)` | Escapes HTML special characters for safe rendering |
| `getComment(id)` | Returns the comment string for a recommendation (handles both string and object feedback formats) |
| `toggleCommentRow(id)` | Shows/hides the comment input row on a card |
| `saveComment(id)` | Saves comment to feedback object, commits via `ghPut` |
| `toggleFeedback(id, type)` | Thumbs up/down on a recommendation, saves via `ghPut` to `recommendations.json` |
| `updateFeedbackUI(id)` | Updates button active states for a card |
| `renderCard(d, saved, getVote)` | Renders a single date card (shared by scheduled and whenever views) |
| `formatDay(dateStr)` | Formats a date string as "Friday — Apr 4" for the scheduled view |
| `renderRecs(data)` | Renders scheduled (by date) and whenever (by category) sub-panels |
| `loadRecs()` | Fetches recs (via `ghGet` if token exists, else direct fetch) |
| **Done List** | |
| `starsHtml(rating)` | Returns filled/empty star HTML for a 1-5 rating |
| `renderDone()` | Renders the full done list + stats from `allEntries` |
| `getFormEntry()` | Reads all form fields into an entry object |
| `addEntry()` | Adds new or updates existing entry (checks `editingIndex`), saves to GitHub |
| `editEntry(index)` | Populates form with entry data, sets edit mode |
| `deleteEntry(index)` | Confirms and removes entry, saves to GitHub |
| `cancelEdit()` | Exits edit mode, clears form |
| `resetForm()` | Clears all form fields, resets to "add" mode |

#### Init sequence (bottom of script)
1. `applyTheme(getPreferredTheme())` — set light/dark theme + listen for system changes
2. `updateSetupUI()` — show connection status
3. `loadRecs()` — fetch recommendations (via API with token, or direct), store SHA, `renderRecs()`
4. `fetchDoneFromGitHub()` → populate `allEntries` → `renderDone()`

---

## recommendations.json

Two main arrays: `scheduled` (date-specific ideas) and `whenever` (anytime ideas by category).

```json
{
    "dateRange": "March 31 - April 13, 2026",
    "updated": "March 31, 2026",
    "feedback": {"sunset-picnic": "up", "game-night": {"vote": "down", "comment": "Not into board games"}},
    "previousIds": ["sunset-picnic", "..."],
    "scheduled": [
        {
            "date": "2026-04-04",
            "dates": [{ "id": "...", "name": "...", "price": "...", "desc": "...", "start": "7:00 PM", "duration": "2 hrs", "where": "...", "reservation": "none", "link": "https://..." }]
        }
    ],
    "whenever": [
        {
            "title": "Category Name",
            "dates": [{ "id": "...", "name": "...", "price": "...", "desc": "...", "duration": "2 hrs", "where": "...", "reservation": "none", "link": "https://..." }]
        }
    ]
}
```

- `scheduled` — ideas tied to specific dates, shown under "This Period" tab. Each entry has a `date` (YYYY-MM-DD) and array of date ideas for that day.
- `whenever` — category-grouped ideas for anytime, shown under "Whenever" tab
- `feedback` — user thumbs up/down + optional comments. Values can be a simple string (`"up"`/`"down"`) or an object (`{ "vote": "down", "comment": "too expensive" }`). AI reads this, then clears it on refresh.
- `previousIds` — tracks all ever-used IDs to prevent repeats
- `reservation` — controls tag color: `"none"` = green, `"tickets"`/`"required"` = orange
- The AI replaces `scheduled` and `whenever`, clears `feedback`, and appends new IDs to `previousIds` on each refresh

## done-dates.json

Array of objects, newest first:

```json
[
    {
        "date": "2026-03-28",
        "activity": "Sunset Picnic",
        "rating": 5,
        "category": "outdoor",
        "location": "Riverside Park",
        "price": "$25",
        "notes": "Beautiful weather"
    }
]
```

- Only `activity` is required, everything else is optional
- `rating` is 1-5 (the AI uses this to tune recommendations)
- Written to by the site form (via GitHub API) and by the AI (via direct file edit)

---

## Setup

### GitHub Pages
1. **Settings > Pages** → Deploy from branch → **master** / root → Save

### Direct saving from the site
1. Go to **github.com/settings/tokens** → Fine-grained tokens → Generate
2. Scope to **only this repository**, Contents: **Read and write**
3. Paste token on the Done List tab under "GitHub connection setup"

---

## Updating Recommendations

Open this repo in [Claude Code](https://claude.ai/code) and ask it to refresh. It reads `CLAUDE.md` for detailed instructions, reads `done-dates.json` for preferences, and updates `recommendations.json`.
