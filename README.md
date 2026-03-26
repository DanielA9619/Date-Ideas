# Date Ideas

A simple GitHub Pages site to track and discover date ideas together.

**Live site:** [daniela9619.github.io/date-ideas](https://daniela9619.github.io/date-ideas/)

## How It Works

The site has two tabs:

### Recommendations
- Date ideas organized by category (Outdoors, Food, Arts, Cozy, Active, Unique)
- Check off ideas as you do them — completed ones fade out
- Updated every two weeks with fresh ideas based on what you've already done

### Done List
- Log completed dates with a simple form
- Fields: date, activity, liked it (yes/no), category (optional), notes (optional)
- Stats dashboard tracks totals, favorites, and category variety

## Setup

1. Go to **Settings > Pages** in this repo
2. Set source to **Deploy from a branch**
3. Select **master** branch, **/ (root)**
4. Save — site goes live in about a minute

## Data Storage

Everything is saved in your browser's **localStorage** — no backend, no accounts. Data persists between visits on the same device/browser.

## Updating Recommendations

Run this repo with [Claude Code](https://claude.ai/code) and ask it to refresh the recommendations. It will read your done list history and generate new, non-repetitive ideas tailored to your preferences.
