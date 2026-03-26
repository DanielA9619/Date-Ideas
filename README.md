# Date Ideas

A GitHub Pages site to discover and track date ideas together.

**Live site:** [daniela9619.github.io/date-ideas](https://daniela9619.github.io/date-ideas/)

## What It Does

**Recommendations tab** — Date ideas organized by category, each with:
- Price range, suggested day/time, duration, location, and reservation info
- Clickable checkboxes to mark completed — done items fade out
- Refreshed every two weeks with new ideas tailored to your ratings

**Done List tab** — Log completed dates directly on the site:
- Date, activity, 1-5 star rating, category (optional), notes (optional)
- Entries are saved locally and can be synced to GitHub via the copy button
- Stats dashboard shows total dates, average rating, and category count

## How to Log a Date

Two ways:

1. **On the site** — Fill in the form on the Done List tab. Hit "Copy Done List for GitHub" and paste it into `done-dates.json` on GitHub to save permanently.
2. **Tell Claude Code** — Say *"we did [activity] and it was a 4/5"* and it adds it to `done-dates.json` automatically.

## Setup

1. Go to **Settings > Pages** in this repo
2. Set source to **Deploy from a branch**
3. Select **master** branch, **/ (root)**
4. Save — site goes live in about a minute

## Data

| File | Purpose |
|------|---------|
| `recommendations.json` | Current date ideas (AI updates this) |
| `done-dates.json` | Completed dates log with ratings |
| `index.html` | Site shell — renders the JSON files |
| `CLAUDE.md` | Instructions for the AI agent |

## Updating Recommendations

Open this repo in [Claude Code](https://claude.ai/code) and ask it to refresh the recommendations. It reads `done-dates.json` for your star ratings and preferences, then generates new ideas that lean into what you loved and avoid what you didn't.
