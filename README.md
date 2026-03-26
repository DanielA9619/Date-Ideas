# Date Ideas

A GitHub Pages site to discover and track date ideas together.

**Live site:** [daniela9619.github.io/date-ideas](https://daniela9619.github.io/date-ideas/)

## What It Does

**Recommendations tab** — Date ideas organized by category, each with:
- Price range, suggested day/time, duration, location, and reservation info
- Clickable checkboxes to mark completed — done items fade out
- Refreshed every two weeks with new ideas

**Done List tab** — Log completed dates with a simple form:
- Date, activity, liked it (yes/no), category (optional), notes (optional)
- Stats dashboard at the bottom

## Setup

1. Go to **Settings > Pages** in this repo
2. Set source to **Deploy from a branch**
3. Select **master** branch, **/ (root)**
4. Save — site goes live in about a minute

## Data Storage

Checkbox state and done list entries are saved in your browser's **localStorage**. Data persists between visits on the same device/browser.

## Updating Recommendations

Open this repo in [Claude Code](https://claude.ai/code) and ask it to refresh the recommendations. It reads `CLAUDE.md` for instructions on how to structure updates and uses the done list to avoid repeats.
