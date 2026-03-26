# Claude Code Instructions — Date Ideas

This file tells the AI agent how to update the date recommendation site.

## Project Overview

- Single-page site: `index.html`
- Two tabs: **Recommendations** (date ideas) and **Done List** (user-logged dates)
- Hosted via GitHub Pages from the `master` branch
- User data (checkboxes, done entries) is stored in `localStorage`

## Updating Recommendations

When asked to refresh or update recommendations:

1. **Read `index.html`** to see current recommendations and which `data-id` values exist
2. **Avoid repeating** any date ideas already in the recommendations or marked as done
3. **Use the done list entries** (if any exist in the HTML or are described by the user) as inspiration — lean into categories and types they rated positively, avoid ones they didn't enjoy
4. **Generate 10-14 new ideas** spread across the categories below

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

## Update Checklist

When refreshing recommendations:

- [ ] Update the date range in `<p class="subtitle">` (header)
- [ ] Update the date in `<p class="updated">` (footer)
- [ ] Replace the recommendation cards in each category section
- [ ] Ensure every `data-id` is unique and not reused from previous cycles
- [ ] Keep the Done List panel, stats, and all JavaScript unchanged
- [ ] Commit and push to `master`
