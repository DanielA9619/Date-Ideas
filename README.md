# Date Ideas

A GitHub Pages site to discover and track date ideas together.

**Live site:** [daniela9619.github.io/Date-Ideas](https://daniela9619.github.io/Date-Ideas/)

## What It Does

**Recommendations tab** — Date ideas organized by category, each with:
- Price range, suggested day/time, duration, location, and reservation info
- Clickable checkboxes to mark completed — done items fade out
- Refreshed every two weeks with new ideas tailored to your ratings

**Done List tab** — Log completed dates directly on the site:
- Date, activity, 1-5 star rating, category, location, price, notes (all optional except activity)
- Saves directly to `done-dates.json` via GitHub API
- Stats dashboard shows total dates, average rating, and category count

## First-Time Setup

### GitHub Pages
1. Go to **Settings > Pages** in this repo
2. Set source to **Deploy from a branch**
3. Select **master** branch, **/ (root)**, and save

### Direct Saving (one-time)
To save dates from the site directly to the repo:
1. Go to **github.com/settings/tokens** → Fine-grained tokens → Generate
2. Name it "Date Ideas", select **Only this repository**
3. Under Permissions → Contents → **Read and write**
4. Paste the token on the Done List tab under "GitHub connection setup"

Without the token, you can still log dates through Claude Code.

## Data

| File | Purpose |
|------|---------|
| `recommendations.json` | Current date ideas (AI updates this) |
| `done-dates.json` | Completed dates log with ratings |
| `index.html` | Site shell — renders the JSON files |
| `CLAUDE.md` | Instructions for the AI agent |

## Updating Recommendations

Open this repo in [Claude Code](https://claude.ai/code) and ask it to refresh the recommendations. It reads `done-dates.json` for your star ratings and preferences, then generates new ideas that lean into what you loved and avoid what you didn't.
