# Claude Code Instructions — Date Ideas

This file tells the AI agent how to update the date recommendation site.

## Project Overview

- Single-page site: `index.html`
- Two tabs: **Recommendations** (date ideas) and **Done List** (user-logged dates)
- Hosted via GitHub Pages from the `master` branch
- User data (checkboxes, done entries) is stored in `localStorage`
- A **hidden HTML comment** at the bottom of `index.html` stores preference summaries and history for the AI

## How the Done List Works

The done list lives in the user's browser (`localStorage`), so the AI **cannot read it directly** from the code. Instead:

1. **Ask the user** what dates they've done recently, what they liked, and what they didn't
2. **Read the hidden preference summary** at the bottom of `index.html` (inside `<!-- PREFERENCE SUMMARY -->`) for past context
3. **Update the preference summary** with the new info the user gives you

This way, preference data persists across sessions in the source code.

## Updating Recommendations

When asked to refresh or update recommendations:

1. **Read `index.html`** — check the hidden preference summary at the bottom and the current recommendation `data-id` values
2. **Ask the user** about any new dates they've done since last update — what they did, liked/disliked
3. **Update the preference summary** comment block with:
   - New done dates added to `DONE DATES`
   - Updated `PREFERENCES` (what they enjoy)
   - Updated `AVOID / DIDN'T ENJOY` (what to skip)
   - All old + new `data-id` values added to `PREVIOUSLY RECOMMENDED IDS`
4. **Generate 10-14 new ideas** that:
   - Lean into categories and types they rated positively
   - Avoid anything in `AVOID / DIDN'T ENJOY`
   - Never reuse any ID from `PREVIOUSLY RECOMMENDED IDS`
5. **Replace** the recommendation cards in each category section

## Categories

Use these category groupings. Aim for 2-3 ideas per category minimum:

- **Adventures & Outdoors** — parks, drives, exploring, nature
- **Food & Dining** — cooking, restaurants, food crawls, markets
- **Arts & Culture** — bookstores, museums, music, classes
- **Cozy / Stay-In** — movie nights, games, spa, home activities
- **Active & Sporty** — hiking, biking, sports, physical activities
- **Unique / Novelty** — challenges, themed dates, unusual experiences

## Date Card HTML Structure

Every recommendation must follow this exact HTML structure inside its category `<div class="category">`:

```html
<div class="date-card" data-id="unique-kebab-case-id">
    <div class="card-header">
        <input type="checkbox" onchange="toggleRec(this)">
        <div class="name">Date Name Here</div>
        <div class="price">$XX - $XX</div>
    </div>
    <div class="desc">One to two sentence description of the date idea.</div>
    <div class="details">
        <div class="detail"><span class="label">When</span> Suggested day/timing</div>
        <div class="detail"><span class="label">Start</span> Suggested start time</div>
        <div class="detail"><span class="label">Duration</span> X - X hrs</div>
        <div class="detail"><span class="label">Where</span> Location description</div>
        <!-- Use ONE of the following: -->
        <div class="detail detail-free">No reservation needed</div>
        <!-- OR -->
        <div class="detail detail-ticket">Tickets may be needed</div>
        <!-- OR -->
        <div class="detail detail-ticket">Reservation required</div>
    </div>
</div>
```

### Required fields for each card

| Field | Description | Example |
|-------|-------------|---------|
| `data-id` | Unique kebab-case identifier | `sunset-picnic` |
| Name | Short, catchy title | `Sunset Picnic` |
| Price | Estimated cost range for two people | `$15 - $30` or `Free` |
| When | Best day/time type | `Any evening`, `Weekend morning`, `Fri or Sat night` |
| Start | Suggested start time | `7:00 PM`, `Flexible`, `~1 hr before sunset` |
| Duration | Estimated total time | `2 - 3 hrs` |
| Where | General location type | `Local park`, `Home`, `Downtown area` |
| Reservation | One of the three tag options above | Use `detail-free` or `detail-ticket` class |

## Preference Summary Format

The hidden comment block at the bottom of `index.html` follows this format:

```html
<!--
===========================================================
PREFERENCE SUMMARY (hidden — for AI agent use only)
===========================================================

DONE DATES:
- 2026-03-28 | Cooking Challenge Night | Loved it | "we had so much fun with Korean food"
- 2026-04-02 | Hike + Lunch | Meh | "trail was too crowded"

PREFERENCES:
- Love cooking together and food-related dates
- Enjoy low-key evenings at home
- Like exploring new neighborhoods

AVOID / DIDN'T ENJOY:
- Crowded trails on weekends
- Expensive sit-down restaurants

PREVIOUSLY RECOMMENDED IDS:
sunset-picnic, stargazing-drive, cooking-challenge, ...

LAST UPDATED: 2026-04-09
===========================================================
-->
```

## Update Checklist

When refreshing recommendations:

- [ ] Ask the user about recent dates and update the preference summary
- [ ] Update the date range in `<p class="subtitle">` (header)
- [ ] Update the date in `<p class="updated">` (footer)
- [ ] Replace the recommendation cards in each category section
- [ ] Ensure every `data-id` is unique and not in `PREVIOUSLY RECOMMENDED IDS`
- [ ] Add all new `data-id` values to `PREVIOUSLY RECOMMENDED IDS`
- [ ] Keep the Done List panel, stats, and all JavaScript unchanged
- [ ] Commit and push to `master`
