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
| Base / layout | body, container, header, tabs |
| `.theme-toggle` | Sun/moon toggle button in header |
| `.date-card` | Recommendation cards (checkbox, name, price, desc, detail pills) |
| `.feedback-btns` / `.fb-btn` | Thumbs up/down buttons on recommendation cards |
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
| `#recs` | Empty div — filled by JS from `recommendations.json` |
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
| `toggleFeedback(id, type)` | Thumbs up/down on a recommendation, saves via `ghPut` to `recommendations.json` |
| `updateFeedbackUI(id)` | Updates button active states for a card |
| `renderRecs(data)` | Renders all recommendation cards with feedback buttons |
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

```json
{
    "dateRange": "March 26 - April 8, 2026",
    "updated": "March 26, 2026",
    "feedback": {"sunset-picnic": "up", "game-night": "down"},
    "previousIds": ["sunset-picnic", "..."],
    "categories": [
        {
            "title": "Category Name",
            "dates": [
                {
                    "id": "kebab-case-id",
                    "name": "Date Name",
                    "price": "$XX - $XX",
                    "desc": "Description.",
                    "when": "Any evening",
                    "start": "7:00 PM",
                    "duration": "2 - 3 hrs",
                    "where": "Location",
                    "reservation": "none|tickets|required"
                }
            ]
        }
    ]
}
```

- `feedback` — user thumbs up/down on ideas: `"up"` = interested, `"down"` = not for us. AI reads this, then clears it on refresh.
- `previousIds` — tracks all ever-used IDs to prevent repeats
- `reservation` — controls tag color: `"none"` = green, `"tickets"`/`"required"` = orange
- The AI replaces `categories`, clears `feedback`, and appends new IDs to `previousIds` on each refresh

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
