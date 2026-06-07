# Meal Planner — App Spec
*v2 — updated after design conversation*

## What the app does
A personal weekly meal planning app that generates a Target-friendly shopping list, gives a flexible pool of meals to pick from throughout the week, and tracks gentle healthy habits — with no calorie counting, no strict schedules, and no shame.

## Who uses it and when
- Just me, on my phone
- **Sunday (or any shopping day):** plan the week — pick meals for the week's pool, generate shopping list, go to Target
- **Throughout the week:** each day, tap which meals from the pool I actually had
- **Once a week:** optional weight check-in (research supports weekly over daily — daily fluctuations mask real trends and can feel discouraging)
- **End of day:** quick habit check-in (did I eat a veggie? drink water?)

## Design philosophy
- **A pool, not a schedule.** I pick ~5-7 meal ideas for the week. Each day I check off what I actually had. No "you were supposed to eat salmon on Wednesday."
- **Flexibility is the feature.** The store might not have a specific item — keep everything generic enough to swap.
- **Low bar, high consistency.** Habits start at the floor: ate a vegetable today? drank a glass of water? Build up slowly. Never reset with shame.
- **Perfectionism-safe.** Partial weeks count. One logged meal is better than zero. The UI never implies you "failed" a day.
- **Minimize dishes.** Meals are tagged by dish count. A "no dishes" meal (a salad kit) and a "1 pan" meal are very different decisions.
- **ADHD-friendly:** big tap targets, 2-tap max to log anything, no long forms, no overwhelming lists.

---

## Core data entities

### Meal Library
My personal rotation of meals. Not a food database — a curated shortlist of things I actually make and eat.

Each meal:
```json
{
  "id": "kevins-salad",
  "name": "Bag Salad Kit + Kevin's Chicken",
  "meal_type": ["lunch", "dinner"],
  "tags": ["no-cook", "no-dishes", "veggie-friendly", "quick"],
  "dishes": 0,
  "recipe": null,
  "notes": "Add extra spinach, shredded cheese, whatever veggies are around.",
  "shopping_item": "bag salad kit + shredded cheese",
  "favorite": true
}
```

**Tags that matter:**
- `no-cook` — nothing needs heat
- `no-dishes` / `1-pan` / `2-pan` — how much cleanup
- `quick` — ready in under 10 min
- `freezer` — frozen item, can stock up
- `veggie-friendly` — no meat prep involved
- `recipe` — has steps to follow (shown when selected)

**Starting meal library (pre-seeded):**

| Meal | Type | Dishes | Tags |
|---|---|---|---|
| Eggos + peanut butter (+ choc chips) | breakfast | 0 | no-cook, quick |
| Bag salad kit + Kevin's chicken + extras | lunch/dinner | 0 | no-cook, quick, veggie-friendly |
| Frozen veggie meal (Annie's, etc.) | lunch/dinner | 1 | freezer, quick, veggie-friendly |
| Scrambled eggs + toast | breakfast | 1 | quick, veggie-friendly |
| *(add more over time)* | | | |

### Weekly Plan
A pool of selected meals for the week — not assigned to specific days.

```json
{
  "week": "2026-W24",
  "meal_pool": ["kevins-salad", "frozen-veggie", "eggo-pb", "scrambled-eggs"],
  "shopping_list": [
    { "item": "bag salad kits", "qty": "3–4", "store_section": "produce" },
    { "item": "Kevin's pre-cooked chicken (Target)", "qty": "2 packs", "store_section": "refrigerated" },
    { "item": "frozen veggie meals", "qty": "5", "store_section": "frozen" },
    { "item": "Eggos", "qty": "1 box", "store_section": "frozen" },
    { "item": "shredded cheese", "qty": "1 bag", "store_section": "dairy" }
  ]
}
```

Shopping list items are derived from the meal pool — generic enough to grab whatever's available at the store.

### Day Log
What I actually had each day — checked off from the week's pool.

```json
{
  "date": "2026-06-06",
  "logged_meals": ["eggo-pb", "kevins-salad"],
  "habits": {
    "ate_veggie": true,
    "drank_water": false
  },
  "weight_lbs": null
}
```

### Weight Log
Weekly check-in. Stored separately from day logs.

```json
[
  { "date": "2026-06-01", "weight_lbs": 170.0 },
  { "date": "2026-06-08", "weight_lbs": 169.5 }
]
```

---

## Key screens

### 1. Today (default tab)
- "What did you eat today?" — shows the week's meal pool as tappable chips
- Tap a meal → it checks off with a warm filled color. Tap again to undo.
- No breakfast/lunch/dinner slots — just "did I have this today"
- Bottom of screen: two habit toggles
  - 🥦 Ate a vegetable today
  - 💧 Had a glass of water
- Weight button appears once per week (Sunday, or whenever — no nagging)

### 2. This Week (tab)
- Horizontal strip of days (Mon–Sun), today highlighted
- Below: the week's meal pool — each meal shows a dot for each day it was logged
- Tap any past day to view/edit what you had
- Progress shown as a warm fill on each meal chip (0 of 7 days → full of 7 days)
- No "you missed Tuesday" language — just shows what happened

### 3. Plan Week (tab or modal, triggered Sunday or on-demand)
- Step 1: Pick meals for the week (from library, multi-select)
- Step 2: Review auto-generated shopping list
- Step 3: (optional) mark items as "already have"
- Produces a shareable/printable shopping list (or just a screen to reference at Target)

### 4. Meal Library (tab)
- My meals, grouped by tag: Quick & Easy / Freezer / Has a Recipe
- Tap any meal to see details + recipe steps (if it has them)
- "+ Add meal" — name, tags, dish count, optional recipe steps, shopping note
- Edit / favorite / delete

### 5. Patterns (tab — simple, added later)
- Weight line chart (weekly dots)
- Habit calendar: each day is a soft-colored square — green if both habits, amber if one, empty if neither
- Framing: "Here's what your week looked like" — no streaks, no scores, no shame

---

## Recipes (simple format)
For meals with a recipe, steps are plain English — no ingredient lists with exact measurements. Just what to do.

Example — "Veggie Fried Rice":
1. Cook rice (or use a microwave packet)
2. Scramble 2 eggs in a pan, push to the side
3. Add frozen peas and corn, stir a minute
4. Add the rice, splash of soy sauce, sesame oil if you have it
5. Season to taste

Max 6 steps. No "¼ tsp of X." Trust the cook's instincts.

---

## What persists (localStorage keys)

| Key | Contents |
|---|---|
| `meals_library` | Array of meal objects |
| `log_YYYY-MM-DD` | Day log for that date |
| `plan_week_YYYY-WW` | Weekly plan + shopping list |
| `weight_log` | `[{date, weight_lbs}]` |
| `app_settings` | Onboarding done, preferred shopping store |

---

## Color palette — "dreamy warm sunset over adobe roofs"

| Role | Color | Usage |
|---|---|---|
| Background | `#f5ede6` | Warm cream base |
| Surface | `#fdf8f4` | Cards, modals |
| Brand / primary | `#c1694f` | Terracotta — buttons, active states, checkmarks |
| Accent | `#e8a598` | Soft pink — habit toggles, secondary highlights |
| Accent 2 | `#d4826a` | Deeper rose-terracotta — progress fills |
| Text primary | `#3d2b23` | Dark warm brown |
| Text muted | `#9c7b72` | Secondary labels |
| Logged / complete | `#c1694f` | Filled terracotta chip |
| Not logged | `#f0e0d8` | Pale blush chip (inactive) |
| Habit on | `#e8a598` | Soft pink toggle |

---

## Out of scope (won't build)
- Calorie counts, macros, nutrition data — ever
- Exact ingredient quantities in recipes
- Strict day-by-day meal scheduling
- Grocery delivery integration
- Social features or accounts
- Large food database search
- Meal ratings, food photos
- Streaks with reset/failure states

---

## Stack
Per the playbook:
- Vanilla JS, no framework, no build step
- Inline styles via JS, `el()` helper pattern
- Single `render()` function from state
- GitHub Pages hosting (`/meal-planner/` path)
- Service worker for PWA / home screen install
- `/data/meals.json` — pre-seeded meal library (editable without touching code)
