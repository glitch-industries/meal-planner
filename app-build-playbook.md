# App Build Playbook
### Lessons from "Lower the Bar" → applied to your next project

---

## 1. Start with a Spec, Not Code

Before any code, write a plain-English `.md` spec file covering:

- **What the app does** (one sentence)
- **Who uses it and when** (e.g. "on my phone, after dinner")
- **Core data entities** (e.g. meals, days, goals, phases)
- **Key screens** (e.g. today view, history, settings)
- **What persists** (localStorage? which keys?)
- **What's out of scope** (saves arguments later)

Put it in the repo. Claude reads it before every session to get context fast.

**Prompt to start:** *"Here's my spec: [paste]. What's missing before we write code?"*

---

## 2. Architecture: Keep It Boring

The stack that worked here scales to almost any personal app:

| Layer | Choice | Why |
|---|---|---|
| Hosting | GitHub Pages | Free, fast, deploys on push |
| Language | Vanilla JS | No build step, no dependencies |
| Styling | Inline styles in JS | Co-located with logic, easy to tweak |
| Storage | `localStorage` | No backend, works offline |
| Offline | Service Worker | PWA = installs on home screen |
| Data | JSON files in `/data/` | Editable without touching code |

**The golden rule:** if you don't need a framework, don't add one. Every dependency is a future problem.

---

## 3. Separate Data from Code

Store all content in `/data/*.json` — never hardcode plan content in JS.

For a meal planning app:
```
/data/meals.json        ← meal library (name, calories, macros, tags)
/data/plans.json        ← weekly templates
/data/phases.json       ← calorie phases / progression rules
/data/schedule.json     ← anchor date + weekly pattern
```

**Why it matters:** you can update meal data, calorie targets, or plans without touching app logic. Claude can edit a JSON file in seconds; refactoring JS logic takes much longer.

---

## 4. The Render Pattern

Use a single `render()` function that rebuilds the whole UI from state. Simple, predictable, easy to debug.

```js
var state = {
  view: "today",        // which tab is active
  selectedDay: 3,       // rolling window index
  // ... other UI state
};

function render() {
  recomputeInfo();       // refresh derived values
  // save scroll position before rebuild
  var savedScroll = (root._lastView === state.view && root.children[1])
    ? root.children[1].scrollTop : 0;
  root._lastView = state.view;
  root.innerHTML = "";
  // build UI...
  scrollArea.scrollTop = savedScroll;  // restore scroll
}
```

**Lessons learned the hard way:**
- Always save/restore scroll position or toggles jump to top
- Reset scroll to 0 when switching tabs, preserve it within a tab
- `root.innerHTML = ""` is fine — don't fear full rebuilds

---

## 5. Service Worker: The One Rule

**Bump the cache version every single deploy.** If you forget, users get stale files.

```js
// sw.js
var CACHE = "myapp-v1";  // increment this every deploy
```

Also add this to your HTML so updates apply immediately without the user clearing history:

```js
navigator.serviceWorker.register("/myapp/sw.js")
  .then(function(reg){ reg.update(); });

navigator.serviceWorker.addEventListener("controllerchange", function(){
  window.location.reload();
});
```

**Commit rule:** icon + SW bump always go in the same commit. Never split them.

---

## 6. Mobile-First Layout (What Actually Works)

### Sticky header, scrollable content
```js
// Fixed zone — never scrolls away
var stickyTop = el("div", {style: "flex-shrink:0;"});
stickyTop.appendChild(renderHeader());
stickyTop.appendChild(renderTabs());

// Scroll zone — only this moves
var scrollArea = el("div", {style:
  "flex:1; overflow-y:auto; -webkit-overflow-scrolling:touch;" +
  "padding-bottom:env(safe-area-inset-bottom,16px);"});
```

### Safe area insets (iPhone notch + home indicator)
```css
html, body { height: 100%; overflow: hidden; }
#app {
  height: 100%;
  display: flex;
  flex-direction: column;
  padding-top: env(safe-area-inset-top);
  padding-left: env(safe-area-inset-left);
  padding-right: env(safe-area-inset-right);
}
```

### Viewport meta tag (required)
```html
<meta name="viewport"
  content="width=device-width, initial-scale=1.0,
           maximum-scale=1.0, user-scalable=no, viewport-fit=cover" />
```

---

## 7. Color Palette — Do This First

Pick your palette before writing a single line of CSS. Changing colors later is tedious.

**What worked for Lower the Bar:**
- 1 dark background (`#2d3a2e`)
- 1 cream base (`#f5f0ea`)
- 2–3 accent colors tied to content categories
- 1 muted tone for "soft" states (blush, sage)
- Never use default browser blue — replace it immediately

**For a food app, consider:**
- Warm terra cottas and creams for food/meals
- A strong brand color for "logged/complete" states
- Avoid red for errors — use amber or muted tones instead

---

## 8. Design Principles That Paid Off

| Principle | How to apply |
|---|---|
| **Collapse, don't hide** | Once a check-in is logged, collapse it to a summary banner. Don't leave a giant form on screen. |
| **Progress feedback** | A thin progress bar above a checklist is more satisfying than a count alone. |
| **Two-state filters only** | Active = filled color + white text. Inactive = white + subtle border. Never three styles at once. |
| **Dim locked content** | `opacity: 0.5` + no interaction tells users "this exists but not yet" without shouting about it. |
| **Timeline > checkbox list** | History views shouldn't look like interactive to-do lists. Connected dots read as "log" not "todo." |
| **Shake on rejection** | When a user taps something they can't do, a quick shake is better than nothing. |
| **Semantic badges > border swaps** | Status = a small pill inside the card. Don't recolor the entire card border per state. |

---

## 9. GitHub as a Backlog

Before building anything, create GitHub issues for every feature. Then work through them systematically.

**Setup:**
```bash
gh issue create --title "Feature name" --body "Description"
gh issue list
gh issue close 3
```

This gives you:
- A clear record of what's been done
- Easy conversation with Claude ("let's do issues 4 and 5")
- A history you can reference months later

---

## 10. How to Prompt Claude Code Effectively

### Starting a session
> *"We're working on [app name]. The spec is in `spec.md` and all files are in `/path/to/project`. Read the spec and the main JS file before we do anything."*

### For features
> *"Let's implement [feature]. The relevant code is in [function name] around line [N]. Don't touch anything else."*

### For design changes
> *"Review the current colors in app.js and suggest changes. Show me your reasoning before applying anything."*

### For deploys
> *"Apply the changes, bump the SW cache version, and push. Confirm the cache version before pushing."*

### For debugging
> *"[describe what's wrong]. Here's what I see: [screenshot/description]. Don't guess — read the relevant function first."*

### Preview before pushing
> *"Save a preview to /tmp/ and open it before deploying."* (Critical for icon/image work)

---

## 11. Common Mistakes to Avoid

| Mistake | Fix |
|---|---|
| Forgetting to bump SW cache | Make it part of every deploy command |
| Changing icon without bumping SW | Same commit, always |
| Inline colors everywhere | Define a `COLORS` or `TAG_COLORS` object at the top |
| Day/date logic bugs | Always derive day name from the actual date, never from array index |
| Scroll jumping on toggles | Save/restore `scrollTop` around every `render()` call |
| Tabs resetting scroll | Track `root._lastView` — only restore scroll if view hasn't changed |
| Future days acting like today | Gate all interactions behind `!selectedIsFuture()` |

---

## 12. For Your Meal Planning App — Starter Thoughts

Based on what worked here, I'd suggest:

**Data model to think about early:**
- `meals.json` — the food library (calories, macros, tags like "breakfast", "quick", "high-protein")
- `logs.json` or localStorage by date — what was actually eaten
- `goals.json` — calorie targets, macro splits, by phase if progressive
- `schedule.json` — anchor date, weekly meal templates if you use them

**Features that will feel familiar:**
- Rolling 7-day window for the day selector
- Phase-gated progression (deficit phase → maintenance phase)
- Check-in card (hunger level instead of energy level)
- History timeline (calories logged per day)
- Streak counter for days on target

**New challenges to plan for:**
- Search/filter in a large meal library
- Calorie math (running daily total)
- Partial servings (0.5× a meal)
- Quick-add for repeated meals

**The one thing to nail first:** the "log a meal" interaction. Make it as fast as possible — 2 taps maximum. Everything else is secondary.

---

*Built during "Week Zero" of Lower the Bar, June 2026.*
*Week One starts Monday. 🍑*
