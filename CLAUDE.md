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
| `category` | optional | `outdoor`, `food`, `arts`, `cozy`, `active`, `unique` |
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
    "hike-lunch": { "comment": "Maybe if it's not too hot" }
}
```

Feedback values can be:
- A simple string `"up"` or `"down"` (vote only, no comment)
- An object with `vote` (`"up"` or `"down"`) and/or `comment` (string)

### How to use feedback

- `"up"` vote = "This idea appeals to me" — suggest more like it
- `"down"` vote = "Not interested" — avoid similar ideas
- `comment` = **Read carefully** — the user is explaining *why* they like or dislike an idea. Use this to understand their preferences more deeply than just a thumbs up/down. For example, "too expensive" means suggest cheaper alternatives; "love the creativity" means lean into unusual/creative ideas.

## Updating Recommendations

When asked to refresh or update recommendations:

1. **Read `done-dates.json`** to see all past dates, what they liked, and what they didn't
2. **Read `recommendations.json`** — check `previousIds`, and read the `feedback` object
3. **Generate 10-14 new ideas** that:
   - Heavily favor categories/styles with done-date ratings of 4-5
   - Lean into ideas similar to feedback `"up"` votes
   - Avoid ideas similar to feedback `"down"` votes
   - **Pay close attention to feedback comments** — they explain the *why* behind preferences
   - Include some variety from categories rated 3
   - Avoid anything similar to done dates rated 1-2
   - Never reuse any ID from `previousIds`
4. **Replace the `categories` array** in `recommendations.json` with new ideas
5. **Clear the `feedback` object** (reset to `{}`) since it was for the old set
6. **Add all new IDs** to the `previousIds` array (keep old ones too)
7. **Update `dateRange`** and **`updated`** fields

## recommendations.json Structure

```json
{
    "dateRange": "March 26 - April 8, 2026",
    "updated": "March 26, 2026",
    "feedback": {},
    "previousIds": ["sunset-picnic", "stargazing-drive", "..."],
    "categories": [
        {
            "title": "Adventures & Outdoors",
            "dates": [
                {
                    "id": "unique-kebab-case-id",
                    "name": "Date Name Here",
                    "price": "$XX - $XX",
                    "desc": "One to two sentence description.",
                    "when": "Any evening",
                    "start": "7:00 PM",
                    "duration": "2 - 3 hrs",
                    "where": "Location description",
                    "reservation": "none"
                }
            ]
        }
    ]
}
```

### Date fields

| Field | Description | Example |
|-------|-------------|---------|
| `id` | Unique kebab-case identifier | `sunset-picnic` |
| `name` | Short, catchy title | `Sunset Picnic` |
| `price` | Cost range for two | `$15 - $30` or `Free` |
| `desc` | 1-2 sentence description | |
| `when` | Best day/time | `Any evening`, `Weekend morning` |
| `start` | Suggested start time | `7:00 PM`, `Flexible` |
| `duration` | Estimated total time | `2 - 3 hrs` |
| `where` | General location | `Local park`, `Home` |
| `reservation` | `"none"`, `"tickets"`, or `"required"` | |

### Categories

Use these 6 category titles. Aim for 2-3 ideas per category minimum:

- **Adventures & Outdoors** — parks, drives, exploring, nature
- **Food & Dining** — cooking, restaurants, food crawls, markets
- **Arts & Culture** — bookstores, museums, music, classes
- **Cozy / Stay-In** — movie nights, games, spa, home activities
- **Active & Sporty** — hiking, biking, sports, physical activities
- **Unique / Novelty** — challenges, themed dates, unusual experiences

## Update Checklist

- [ ] Read `done-dates.json` for preference context
- [ ] Read `recommendations.json` for `previousIds`
- [ ] Update `dateRange` and `updated` in `recommendations.json`
- [ ] Replace `categories` with new date ideas
- [ ] Add all new IDs to `previousIds`
- [ ] Do NOT edit `index.html` (unless changing layout/style)
- [ ] Commit and push to `master`
