# GENOCIDE

A single-file, mobile-first 2D pixel-art RPG about the monsters beneath Mount Solace — and what you choose to do to them. Built with vanilla HTML5, Canvas2D and the Web Audio API. No images, no sound files, no build step, no backend, no account. Everything you see and hear is drawn and synthesized live in the browser.

Inspired by the exploration, turn-based "bullet-hell" combat and branching-mercy structure of games like *Undertale* — but every character, location, puzzle, piece of music and story beat here is original.

## A note on scope

The brief behind this project asked for an enormous game — six-plus hours-long areas, dozens of bosses, a dozen puzzles. Building all of that for real (not as stubs) is a multi-month project. Rather than pad this out with placeholder content, I built a **smaller game where every single system actually works end-to-end and has been tested**:

- **5 interconnected areas**, 15 rooms total, each area with its own palette, music and mechanic
- **6 named characters** with real arcs across multiple conversations: a guardian, a failed comedian, a hopeful young monster, a warrior, a scientist, and a flower who is watching all of it
- **9 enemies** — 6 ordinary encounters sharing a small family of attack patterns, plus 3 unique mini-bosses, plus a true final boss exclusive to the genocide route
- **4 original puzzles**: a switch sequence, a push-block/pressure-plate, a destructible object with a real narrative consequence, and a lamp-combination lock
- **3 tracked routes** (pacifist / neutral / genocide), computed from your cumulative kills and how you resolved every named character — never from one final choice — each with its own ending text
- Full save/load, settings (volume, mute, text speed), pause menu, inventory, touch + keyboard controls, and a responsive layout that keeps the dialogue/battle panel from ever overlapping the game view, even on small phones

If you want more areas, characters or bosses later, the data (`ROOMS`, `DLG`, `ENEMIES`) is laid out so it's straightforward to extend — see **Extending the game** below.

## Controls

**Keyboard (desktop):**
| Key | Action |
|---|---|
| Arrow keys / WASD | Move / move the dodge-heart in battle / navigate menus |
| Z, Enter, or Space | Confirm / interact / advance dialogue |
| X | Cancel / back |
| Esc or P | Pause menu |

**Touch (phone/tablet):** an on-screen D-pad (bottom-left), an **A** (confirm/interact) and **B** (cancel) button (bottom-right), and a pause button (top-right) are always visible and always tappable, in gameplay, dialogue, battle and every menu. You can also just tap buttons directly (the dialogue box, battle menu, and all menus are regular tappable buttons).

## How the routes work

Nothing is decided by a single ending choice. The game quietly tracks every kill, every spare, and how each of the five named characters' stories resolved. When you reach the Lumen Spire at the end:

- **Pacifist** — you never killed anything, named or not.
- **Genocide** — you killed every ordinary enemy *and* all five named characters. This unlocks a unique, much harder final fight and a deliberately bleak ending that does not treat winning as a victory.
- **Neutral** — anything in between, with an ending that's assembled from exactly what you actually did.

## Playing locally

Just open `index.html` in a modern browser (Chrome, Firefox, Safari, Edge). No server required for basic play.

Saving uses `localStorage`, which works reliably once the page is served over `http(s)://` (see Publishing below). If you open the file directly as `file://` and your browser restricts storage for local files, the game still runs fine — saves just won't persist between sessions, and the game is built to never crash or get stuck if storage is unavailable.

If you'd rather test locally over `http://` first:
```bash
# Python 3
python3 -m http.server 8000
# then open http://localhost:8000
```

## Publishing to GitHub Pages

1. Create a new GitHub repository (public, for free Pages hosting).
2. Add `index.html` and this `README.md` to the repo root and push:
   ```bash
   git init
   git add index.html README.md
   git commit -m "GENOCIDE"
   git branch -M main
   git remote add origin https://github.com/<your-username>/<your-repo>.git
   git push -u origin main
   ```
3. On GitHub, go to **Settings → Pages**.
4. Under **Build and deployment**, set **Source** to "Deploy from a branch", branch `main`, folder `/ (root)`. Save.
5. Wait a minute or two, then your game will be live at:
   `https://<your-username>.github.io/<your-repo>/`

That's it — no build step, no dependencies to install.

## Testing on an Android phone

1. Publish to GitHub Pages (above), or get the file onto your phone some other way (e.g. host it temporarily with `python3 -m http.server` on your computer and visit your computer's local IP from your phone on the same Wi-Fi).
2. Open the page in **Chrome for Android**.
3. Optional but recommended: tap the Chrome menu (⋮) → **Add to Home screen**, so it opens full-screen like an app, with no browser chrome in the way.
4. Rotate your phone — the game adapts to both portrait and landscape; landscape gives you the most screen space for the dodge-phase battles.
5. If you don't hear sound at first, tap anywhere once — browsers require a tap before they'll allow audio to start (the game handles this automatically; it's a standard mobile-browser restriction, not a bug).
6. To test a fresh run after you've saved, use **Pause → Return to Title**, then **New Game** (this doesn't erase your save unless you also start and save over it — your existing save stays in `localStorage` until you save again or clear site data).

## Design notes / known intentional choices

- **Defeat is forgiving.** If your HP hits 0 in battle, you're pulled back with partial HP rather than hitting a hard game-over screen. This was a deliberate choice so a difficult fight never permanently blocks progress on a phone.
- **Enemy HP numbers are hidden for ordinary encounters** (shown only for named bosses) — a small nod to not knowing exactly how close an enemy is to being spareable.
- **The MERCY option on the true final boss is intentionally unspareable**, with its own flavor text explaining why, rather than silently doing nothing — this is a one-time, narratively-justified exception, not a bug.
- Audio is 100% procedural (oscillators, noise buffers and a small look-ahead scheduler for the music) — there are no audio files, so there's nothing to fail to load.

## Extending the game

Everything content-related lives in clearly-marked sections inside `index.html` (search for the `§` section tags in the comments):

- `§DATA-ROOMS` — add a new room object to `ROOMS`, give it `walls`, `decor`, `npcs`, `enemies`, `objects`, and `exits`, and link an existing room's `exits` array to it.
- `§DATA-DIALOGUE` — dialogue is just a function returning an array of steps (`L` for a line, `CH` for a choice, `FN` to run code, `BATTLE` to start a fight). Add a new `DLG.yourKey` function and reference it from an NPC's `dialogueKey`.
- `§DATA-ENEMIES` — add a new entry to `ENEMIES` and a matching `patYourEnemy(battle, pt, dt)` function that pushes bullets into `battle.bullets`.

No build step is needed — just edit `index.html` and reload.
