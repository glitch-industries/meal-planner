# ADHD Design Guide — Roughly Chopped

Design and feature decisions for this app are informed by ADHD psychology and executive dysfunction research. This file serves as a reference for future development.

## User Context

Robyn has ADHD. Key patterns that affect eating and food habits:
- **Forgets to eat** — hunger signals are missed or ignored, especially when hyperfocused
- **Decision fatigue** — too many choices leads to no choice; paralysis over "what should I eat"
- **Time blindness** — loses track of time, skips meals without realizing
- **Perfectionism** — all-or-nothing thinking; missing a habit feels like failing entirely
- **Reward sensitivity** — dopamine-driven; immediate feedback works, delayed rewards don't
- **Working memory gaps** — forgets what food is in the fridge, what meals are possible

---

## Core Design Principles

### 1. Reduce decisions, don't add them
The app should narrow choices, not expand them. Show one meal suggestion, not a list. The pool system (plan once a week, eat from the pool) front-loads decisions so day-to-day is frictionless.

### 2. External cues over internal motivation
ADHD brains can't reliably generate internal motivation. The app uses:
- Time-aware suggestions ("How about for lunch?")
- "Haven't eaten yet" nudge after 1pm with nothing logged
- Habit streaks as external accountability

### 3. Forgive one missed day
Streaks break on two consecutive misses, not one. Research shows this dramatically improves completion rates for ADHD brains — the all-or-nothing failure mode is avoided.

### 4. Immediate feedback loop
Tapping "I ate this" immediately updates the UI. Habit checkboxes respond instantly. Streaks are visible at a glance. Never require the user to go looking for feedback.

### 5. Reduce friction at the point of decision
- "Ready to grab" (no-cook) meals are always surfaced first in This Week
- Repeat buys auto-populate the shopping list
- Quick-start themes mean planning can take 10 seconds
- Shopping mode collapses planning UI down to just the list

### 6. Make the invisible visible
ADHD working memory is unreliable. The app makes available food visible (This Week tab), makes what's been eaten visible (day dots, tracking table), and makes habit streaks visible as a proxy for how the week is going.

---

## Nutrition Principles (ADHD-specific)

Research links ADHD symptom severity to nutrition. Key facts to inform meal design:

- **Protein at breakfast is high-priority** — tyrosine and tryptophan are precursors to dopamine and norepinephrine, which ADHD brains are deficient in. High-protein breakfasts improve focus 20-40% longer than carb-heavy ones.
- **Blood sugar stability matters** — crashes worsen ADHD symptoms significantly. Meals combining protein + fat + complex carb are ideal.
- **Meal skipping is very common** — and makes everything worse. The app nudges eating before it becomes a problem.
- **Omega-3s** have the strongest dietary evidence for ADHD support (fish, walnuts, flaxseed).
- Nutrition is complementary to medication/therapy, not a replacement.

### Meal tags used in the app
- `protein-rich` — used to bias morning suggestions toward these meals (supports focus)
- `no-cook` — shown first in "Ready to grab" (reduces friction)
- `freezer` — available without shopping, reliable fallback
- `quick` — low time/effort threshold

---

## Habits Rationale

| Habit | Why it matters for ADHD |
|---|---|
| 💊 Took my meds | Foundation — everything else is harder without it |
| 🌅 Ate breakfast | ADHD + skipping breakfast is documented and common; protein-rich breakfast supports focus |
| 🥗 Packed lunch (weekdays) | Prevents decision paralysis at lunchtime; removes "what can I eat at work" anxiety |
| 💧 Drank water | Dehydration worsens focus and mood; ADHD brains often forget |
| 🥦 Ate a vegetable | Gut-brain axis; microbiome diversity supports neurotransmitter production |
| 🍎 Ate fruit | Blood sugar + micronutrient support; easy external reminder to eat fruit |
| 🚶 Got outside | Light and movement both support dopamine regulation; commonly recommended for ADHD |
| 😴 In bed on time | Sleep deprivation dramatically worsens all ADHD symptoms; often the highest-leverage habit |

### Why streaks forgive one missed day
Hard streaks (break on any miss) trigger the ADHD all-or-nothing failure pattern. Research on habit completion in neurodivergent populations shows that forgiving a single missed day maintains momentum far better than resetting to zero. Two consecutive misses end the streak.

---

## Features Implemented from ADHD Research

- **Feed Me tab** — one suggestion at a time, time-aware, reduces decision to a single tap
- **"Not feeling it" shuffle** — low-friction way to get a different suggestion without choice paralysis
- **"Ready to grab" / "Could cook" split** — surfaces the lowest-friction options first
- **Cooking nudge mid-week** — external cue for a task that gets procrastinated
- **Skipped-meal nudge** — "Hey, you haven't logged anything today" after 1pm
- **Protein-first morning suggestions** — biases breakfast toward focus-supporting meals
- **Forgive-one-day streaks** — reduces perfectionism-driven abandonment
- **Shopping mode** — collapses plan to just the list; removes cognitive load at the store

---

## Future Feature Ideas

- **Meal-time notification** — push notification at noon if nothing logged (needs PWA push permission)
- **Weekly win summary** — end-of-week dopamine reward: "You hit your meds 6/7 days 🎉"
- **Habit stacking tip** — suggest anchoring new habits to existing ones (e.g. "take meds with breakfast")
- **"Forgive one week" for weekly planning** — if the week resets and there's no plan, offer last week's pool as a starting point
- **Low-effort mode** — single-tap to say "I had something" without specifying, for bad executive function days
