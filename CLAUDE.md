# Claude Code Instructions — Date Ideas

This file tells the AI agent how to update the date recommendation site.

## Project Overview

- Single-page site: `index.html` renders data from two JSON files
- **`recommendations.json`** — current date recommendations (AI updates this)
- **`done-dates.json`** — completed dates log (AI adds entries here)
- Hosted via GitHub Pages from the `master` branch
- Checkbox state and theme preference stored in `localStorage`

## File Structure

```
index.html              ← Site shell (don't edit unless changing layout/style)
recommendations.json    ← Current recommendations (AI edits this)
done-dates.json         ← Done date log (AI adds entries here)
CLAUDE.md               ← These instructions
README.md               ← Human-facing docs
```

## Adding a Done Date

When the user says they did a date (e.g. *"we did a sunset picnic and loved it"*), add an entry to the **beginning** of the array in `done-dates.json`:

```json
{
    "date": "2026-03-28",
    "activity": "Sunset Picnic",
    "rating": 5,
    "category": "outdoor",
    "location": "Riverside Park overlook",
    "price": "$25",
    "notes": "Beautiful weather, great spot at the overlook"
}
```

| Field | Required | Values |
|-------|----------|--------|
| `date` | optional | `YYYY-MM-DD` |
| `activity` | **yes** | What they did |
| `rating` | optional | `1` to `5` (star rating — 5 is best) |
| `category` | optional | `outdoor`, `food`, `arts`, `cozy`, `active`, `sports`, `exploration`, `unique` |
| `location` | optional | Where they went |
| `price` | optional | What they spent (e.g. `"$25"`, `"Free"`) |
| `notes` | optional | Any extra details |

### Rating guide for AI

- **5** = "Loved it, want more like this" — heavily favor this category/style
- **4** = "Really enjoyed it" — lean into similar ideas
- **3** = "It was fine" — neutral, okay to suggest similar but don't prioritize
- **2** = "Meh, not great" — reduce similar ideas
- **1** = "Didn't enjoy" — avoid this type

## Recommendation Feedback

Users can thumbs-up or thumbs-down individual recommendations without doing them, and optionally add a comment explaining why. This is stored in the `feedback` object in `recommendations.json`:

```json
"feedback": {
    "sunset-picnic": "up",
    "game-night": { "vote": "down", "comment": "We don't really like board games" },
    "cooking-challenge": { "vote": "up", "comment": "Love the idea of trying new cuisines together" },
    "hike-lunch": { "vote": "maybe", "comment": "Maybe if it's not too hot" }
}
```

Feedback values can be:
- A simple string `"up"`, `"maybe"`, or `"down"` (vote only, no comment)
- An object with `vote` (`"up"` / `"maybe"` / `"down"`) and/or `comment` (string)

### How to use feedback

- `"up"` vote = "This idea appeals to me" — suggest more like it
- `"maybe"` vote = "On the fence — could go either way" — okay to suggest similar but don't prioritize. Pay extra attention to any comment, since it usually explains the hesitation.
- `"down"` vote = "Not interested" — avoid similar ideas
- `comment` = **Read carefully** — the user is explaining *why* they like or dislike (or are unsure about) an idea. Use this to understand their preferences more deeply than just a thumbs up/down. For example, "too expensive" means suggest cheaper alternatives; "love the creativity" means lean into unusual/creative ideas; "maybe if it's not too hot" means suggest indoor or shaded alternatives.

## Updating Recommendations

When asked to refresh or update recommendations:

1. **Read `done-dates.json`** to see all past dates, what they liked, and what they didn't
2. **Read `recommendations.json`** — check `previousIds`, `feedback`, and the `wishlist` array
3. **Process the wishlist** — if there are items in the `wishlist` array, search for each one (it might be a venue name, event, activity, or something they saw on a billboard). Look up the real details (dates, prices, location, website) and include them as full recommendation entries in `scheduled` (if date-specific) or `whenever` (if anytime). After processing, **clear the `wishlist` array** (set to `[]`).
4. **Note today's date** from the `currentDate` context. The 2-week window starts **today** (not a future date). Skip any days that have already passed. So if today is April 16, the window is April 16 – April 29 and no scheduled date should be before April 16.
5. **Generate ideas** split into two groups:
   - **`scheduled`** — 4-7 date-specific ideas tied to particular days within the 2-week window starting today. Pick good days (Fridays, Saturdays, some weeknights). Consider events, weather, and day of week.
   - **`whenever`** — 5-8 anytime ideas grouped by category. These are ideas that work any day.
   - Both groups should:
     - Heavily favor categories/styles with done-date ratings of 4-5
     - Lean into ideas similar to feedback `"up"` votes
     - Avoid ideas similar to feedback `"down"` votes
     - For `"maybe"` votes, read the comment carefully — it usually says what would tip them toward "yes." Adjust similar ideas to address the hesitation.
     - **Pay close attention to feedback comments** — they explain the *why* behind preferences
     - Include some variety from categories rated 3
     - Avoid anything similar to done dates rated 1-2
     - Never reuse any ID from `previousIds`
6. **Replace `scheduled` and `whenever`** arrays in `recommendations.json` with new ideas
7. **Clear the `feedback` object** (reset to `{}`) since it was for the old set
8. **Clear the `wishlist` array** (reset to `[]`) since the items were processed
9. **Add all new IDs** to the `previousIds` array (keep old ones too)
10. **Update `dateRange`** (today → today + 13 days) and **`updated`** (today's date) fields

## recommendations.json Structure

```json
{
    "dateRange": "March 31 - April 13, 2026",
    "updated": "March 31, 2026",
    "feedback": {},
    "wishlist": ["Phantom of the Opera", "that new ramen place in Lehi"],
    "previousIds": ["sunset-picnic", "stargazing-drive", "..."],
    "scheduled": [
        {
            "date": "2026-04-04",
            "dates": [
                {
                    "id": "unique-kebab-case-id",
                    "name": "Date Name Here",
                    "price": "$XX - $XX",
                    "desc": "One to two sentence description.",
                    "start": "7:00 PM",
                    "duration": "2 - 3 hrs",
                    "where": "Location description",
                    "reservation": "none",
                    "link": "https://example.com/venue-or-event"
                }
            ]
        }
    ],
    "whenever": [
        {
            "title": "Category Name",
            "dates": [
                {
                    "id": "unique-kebab-case-id",
                    "name": "Date Name Here",
                    "price": "$XX - $XX",
                    "desc": "One to two sentence description.",
                    "duration": "2 - 3 hrs",
                    "where": "Location description",
                    "reservation": "none",
                    "link": "https://example.com/venue-or-event"
                }
            ]
        }
    ]
}
```

### Scheduled date fields

| Field | Description | Example |
|-------|-------------|---------|
| `id` | Unique kebab-case identifier | `friday-picnic` |
| `name` | Short, catchy title | `Sunset Picnic` |
| `price` | Cost range for two | `$15 - $30` or `Free` |
| `desc` | 1-2 sentence description | |
| `start` | Suggested start time | `7:00 PM`, `10:00 AM` |
| `duration` | Estimated total time | `2 - 3 hrs` |
| `where` | General location | `Local park`, `Home` |
| `reservation` | `"none"`, `"tickets"`, or `"required"` | |
| `link` | URL to venue, event page, or tickets (optional) | `https://thanksgivingpoint.org/tulip-festival` |

### Whenever date fields

Same as scheduled, but `start` is optional (omit or use `"Flexible"`). Include `link` when there's a relevant website.

### Whenever categories

Use these category titles for the `whenever` array. Aim for 1-2 ideas per category:

- **Adventures & Outdoors** — parks, drives, exploring, nature
- **Food & Dining** — cooking, restaurants, food crawls, markets
- **Arts & Culture** — bookstores, museums, music, classes
- **Cozy / Stay-In** — movie nights, games, spa, home activities
- **Active & Sporty** — hiking, biking, sports, physical activities
- **Unique / Novelty** — challenges, themed dates, unusual experiences

## Git Workflow

**Always work directly on the `master` branch.** Do NOT create feature branches or pull requests. The site deploys from `master` via GitHub Pages, so changes must be committed and pushed directly to `master` to go live.

```
git add recommendations.json done-dates.json
git commit -m "Refresh recommendations for <dateRange>"
git push -u origin master
```

If push is rejected (remote has newer commits), pull with rebase first:
```
git pull origin master --rebase
git push -u origin master
```

## Update Checklist

- [ ] Read `done-dates.json` for preference context
- [ ] Read `recommendations.json` for `previousIds`
- [ ] Update `dateRange` and `updated` in `recommendations.json`
- [ ] Replace `scheduled` and `whenever` arrays with new date ideas
- [ ] Add all new IDs to `previousIds`
- [ ] Do NOT edit `index.html` (unless changing layout/style)
- [ ] Commit and push directly to `master` (no branches, no PRs)
