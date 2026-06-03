# ðŸŽ° Lucky Lease

**A rent-survival slot rogue-lite with a secret three-floor casino.**

You're broke, rent is due in a handful of spins, and the only thing standing
between you and eviction is a flickering slot machine. Build a deck of symbols,
stack risky perks, dodge bad-weather modifiers, and â€” if you're feeling
desperate â€” slip behind the marquee to gamble your last credits across ten
side-games of the secret casino.

It's a single-file browser game. No build step, no installs. Just open
`index.html` and play.

---

## Table of contents

- [Getting started](#getting-started)
- [Core loop](#core-loop)
- [The slot machine](#the-slot-machine)
- [Inventory, perks & the draft](#inventory-perks--the-draft)
- [Rent, loans & game-over](#rent-loans--game-over)
- [Daily modifiers](#daily-modifiers)
- [Double-or-nothing](#double-or-nothing)
- [The Secret Casino](#the-secret-casino)
  - [Wager system](#wager-system)
  - [Floor 1 â€” Classic table games](#floor-1--classic-table-games)
  - [Floor 2 â€” Arcade side games](#floor-2--arcade-side-games)
  - [Floor 3 â€” High roller](#floor-3--high-roller)
- [Settings & accessibility](#settings--accessibility)
- [Saving & data](#saving--data)
- [Tech notes](#tech-notes)
- [Controls reference](#controls-reference)
- [License](#license)

---

## Getting started

Lucky Lease ships as a **single HTML file** (`index.html`). Everything â€” UI,
CSS animations, audio synth, particle engine, ten casino games â€” lives in one
~5,000-line document with zero external dependencies at runtime (Tailwind is
loaded via CDN for utility classes, but a hand-written fallback keeps the game
fully playable if the CDN is blocked).

To play locally:

```bash
# clone or download, then either:
open index.html              # macOS
xdg-open index.html          # Linux
start index.html             # Windows

# or serve it over HTTP (any static server works):
python3 -m http.server 8080  # then visit http://localhost:8080
```

No Node, no bundler, no package manager required.

---

## Core loop

Each **day** lasts a fixed number of spins (default: 5). The flow is:

1. **Spin the slot machine** â€” each spin costs credits and pulls three symbols
   from your inventory.
2. **Collect winnings** â€” matching lines, near-misses, perks and item synergies
   all stack into your payout.
3. **Optionally gamble** â€” winning a round lets you risk it in
   double-or-nothing for an instant 2Ã— or zero.
4. **Pay rent** â€” when the day's spins run out, your **rent target** is deducted
   from your balance. Miss it and you can take a high-interest loan to stay
   alive â€” or it's game over.
5. **Draft new gear** â€” surviving rent grants a draft pick. Choose new symbols
   for your reel pool or stack a perk to compound your power.
6. **Day advances** â€” rent rises, modifiers reroll, the loop begins again.

Survive as many days as you can before the wheels stop turning.

---

## The slot machine

Three reels spin emoji symbols pulled from **your personal inventory** â€” not a
fixed paytable. Common symbols like ðŸ’ and ðŸ‹ give modest payouts; rarer
finds like ðŸ’Ž, 7ï¸âƒ£ or ðŸƒ (wild) hit much harder. Some symbols **evolve over
time** with use: ðŸ¥š â†’ ðŸ¦…, ðŸ› â†’ ðŸ¦‹, ðŸŒ± â†’ ðŸŒ³.

Spin mechanics include:

- **Three-of-a-kind payouts** scale with the symbol's base value and rarity.
- **Wilds and synergies** â€” ðŸƒ substitutes for any symbol; certain combos
  (e.g. two same-suit fruits) trigger bonus multipliers via the `SYNERGY` perk.
- **Anticipation** â€” when the first two reels match, the third reel slows and
  glows yellow, drawing out the suspense.
- **Near-miss effects** â€” when a row is one symbol off a jackpot, a ghostly
  copy of the missing symbol floats away and refunds a small consolation.
- **Pity counter** â€” strings of bad luck slowly bias the RNG toward better
  results so dry spells don't bankrupt you instantly.
- **Critical hits** â€” the `CRIT` perk gives every spin a chance to double
  the payout outright.
- **Big-win celebration** â€” payouts above a threshold trigger a screen shake,
  rainbow strobe text, a coin burst across the canvas, and a jackpot sound.

---

## Inventory, perks & the draft

### Inventory (your symbol pool)

Your **inventory** is the literal multiset of symbols the reels can roll.
Starting deck: `ðŸ’ ðŸ’ ðŸ’ ðŸ‹ ðŸ‹ ðŸŠ`. Every symbol has a **value, rarity, and
description**, viewable by clicking or long-pressing the tile.

Rarities tier from **Common â†’ Uncommon â†’ Rare â†’ Epic â†’ Legendary**. Epic
acquisitions trigger a fullscreen pop animation.

### Perks

Perks are passive modifiers tracked as stack counts. Each rank you add makes
the perk stronger. A representative slice:

| Perk | Effect |
|---|---|
| `LIFE` | Extra rent grace â€” first miss is forgiven |
| `MULT` | Increases every payout multiplier |
| `VALUE` | Raises base value of all symbols |
| `COST` | Reduces spin cost |
| `LUCK` | Improves odds of high-value rolls |
| `JACKPOT` | Boosts jackpot frequency and size |
| `EXTRA` | Adds bonus symbols to the spin pool |
| `REBATE` | Refunds a fraction of every loss |
| `INTEREST` | Grows your balance between days |
| `CRIT` | Chance to double a payout |
| `SYNERGY` | Bonus for matching suit/category symbols |
| `TAX_BREAK` | Lowers rent target |
| `INSURANCE` | Reduces game-over risk |
| `BOUNTY` | Periodic free spins |
| `CLEANSE` | Removes a curse on demand |
| `SALVAGE` | Recover credits when symbols are destroyed |
| `DIVIDEND` | Per-day interest scaling with balance |
| `RISK` / `BLOOD_PACT` | High-risk, high-reward modifiers |

### The Draft

Between rent days you get one or more **draft picks**. Each pick opens a
randomized choice panel offering:

- New symbols (sometimes with a cursed downside)
- Perk stack-ups
- Trades (lose a perk, gain a rare symbol â€” or vice versa)

Picks are permanent. Choose carefully â€” your deck and perk stack define how
deep into the run you survive.

---

## Rent, loans & game-over

Every day ends with a **rent collection**. Rent rises over time, so a deck
that pays out on day 3 may not be enough by day 12.

If you can't cover rent:

- **Take a loan** â€” you receive cash immediately but the **total due** grows
  every day (compounding interest). If the loan isn't repaid by its deadline,
  it's game over.
- **Insurance / Life perks** can grant a one-time bailout.

The **game-over overlay** shows total days survived, biggest win, and offers a
restart.

---

## Daily modifiers

Each new day rolls a **weather modifier** that lasts until the next day:

- â˜€ï¸ **Clear Skies** â€” neutral baseline.
- ðŸŒ§ï¸ **Storm Front** â€” adds wildcards to the reel pool but spin cost goes up.
- ðŸŒªï¸ **Tornado** â€” high-volatility â€” bigger swings both ways.
- ðŸŒ«ï¸ **Fog** â€” preview symbols hidden until reels stop.
- ðŸ”¥ **Heatwave** â€” payouts increased but symbols can be "destroyed" on use.
- â„ï¸ **Cold Snap** â€” payouts reduced but spin cost free.

Modifiers can chain with your perks to make brutal or brilliant runs.

---

## Double-or-nothing

After certain winning spins, the **gamble panel** appears. You can stake the
winnings on a coin-flip-style guess (red/black or heads/tails depending on the
mode). Correct â†’ doubled. Wrong â†’ zero. Chain it as many times as you dare,
or **collect** at any point to bank the current pile.

---

## The Secret Casino

Behind the Lucky Lease neon sign is a hidden three-floor casino with **ten
side-games**. Open it from the title bar; close it any time to return to the
slot machine.

The casino has its own **map view**, a floor switcher (FLOOR 1 / 2 / 3), and
an animated player marker that walks between rooms when you select one.

### Wager system

Every casino game shares a single **wager** value (`casinoBet`). Five preset
chips and one custom chip live in three locations (sidebar, room HUD, mobile
bar):

- **10 / 25 / 50 / 100** â€” preset chips. Won't be accepted if you can't
  afford the chip's value.
- **CUSTOM** â€” gold chip. Opens a prompt that accepts any whole integer from
  **1 to your current credits**. Anything else (decimal, zero, overdraft,
  non-numeric) is rejected with a red error message.

Whatever you set applies to **every casino game** automatically.

### Floor 1 â€” Classic table games

A symmetric pentagon layout: corner tables, central roulette, slot exit at
the bottom.

| Room | Bets | Payout |
|---|---|---|
| ðŸª™ **Coin Vault** | Heads / Tails | 2Ã— on correct call |
| ðŸŽ² **Dice Alley** | Low (1â€“3) / High (4â€“6) | 2Ã— on correct call |
| ðŸƒ **High Card** | You draw vs dealer | 2Ã— higher card Â· tie refunds |
| ðŸŽ¡ **Roulette** | Red / Black / Odd / Even / Green | 2Ã— colors Â· 14Ã— green |
| ðŸŽ° **Lucky Lease Exit** | â€” | Returns to the main slot machine |

### Floor 2 â€” Arcade side games

Six games in a clean 3Ã—2 grid.

| Room | Bet flow | Payout |
|---|---|---|
| ðŸ’ **Fruit King** | Multi-bet across 8 symbols; running-light cursor lands on one or more | Per-symbol multipliers up to BAR 50Ã— Â· train and scatter bonuses |
| ðŸ **Snake** | Pay wager, steer the snake to eat apples | Score Ã— wager paid out when you crash |
| ðŸ’£ **Mines** | Reveal safe tiles, bank score before hitting a bomb | Growing multiplier per safe reveal Â· auto-cash at 20 |
| ðŸ§± **Stack Blocks** | Drop moving blocks onto a stack; each layer raises the prize | Take prize anytime Â· miss a layer and lose the wager |
| ðŸš€ **Crash** | Multiplier climbs from 1.00Ã— â€” cash out before the rocket crashes | Variable up to crash; lose wager if you wait too long |
| ðŸ‡ **Horse Race** | Pick horse 1â€“4; horses bob along an animated track | Winning horse pays 4Ã— |

### Floor 3 â€” High roller

A 3 + 3 grid mixing wheel-based games, chance cards, and the upcoming VIP
door.

| Room | Bet flow | Payout |
|---|---|---|
| ðŸŽ¨ **Color** | Bet on a color; 3 dice roll | Match 1/2/3 dice â†’ 1Ã—/2Ã—/3Ã— Â· triple unlocks 10Ã—â€“1000Ã— Boost Wheel |
| ðŸŽ¡ **Wheel of Fortune** | One spin Â· 8 weighted segments | x0, x1, x2, x3, x4, x5, x7, x10 â€” payout = bet Ã— multiplier |
| ðŸŽ¯ **Plinko** | 8 peg rows, 11 multiplier slots, DPR-aware canvas physics | `[5, 2, 1.2, 0.6, 0.3, 0.2, 0.3, 0.6, 1.2, 2, 5]` â€” payout = bet Ã— slot, AUTO 5Ã— available |
| ðŸŽ« **Scratch Card** | Buy a 3Ã—3 card, scratch cells | Match 3/4/5+ identical symbols â†’ 3Ã—/5Ã—/10Ã— Â· diagonal scratch reveal + dust particles |
| ðŸ“ **Cockfighting Arena** | Bet RED ðŸ“ or BLUE ðŸ“ | Live HP-bar match Â· winner pays 2Ã— |
| ðŸ”‘ **VIP Door** | Locked teaser | Reserved for a future high-stakes room |

Each room has its own theme background and a per-game sound profile. Most
games have **fast-feedback animations** â€” peg glow on hit, feather bursts on
clashes, conic-gradient wheels, scratch dust trails, coin showers on big
wins.

---

## Settings & accessibility

A settings panel (gear icon) controls:

- **Volume** â€” 0â€“100% slider scaling the synthesized audio.
- **UI Zoom** â€” 80%â€“140% CSS scale for the whole interface (helpful on small
  laptops or large monitors).
- **Particles** â€” toggle the canvas particle layer (coin showers, sparks,
  floating text) for lower-end devices.
- **Screen shake** â€” toggle the screen-shake feedback for users sensitive to
  motion.

Settings persist between sessions in `localStorage`.

---

## Saving & data

Lucky Lease keeps a small amount of state in your browser's `localStorage`
under the key `luckyLeaseSettings` (settings only). The game itself is a
**fresh-run rogue-lite** â€” closing the tab ends the run, the way the genre
intends. Nothing leaves your machine; there are no analytics, accounts, or
network calls beyond loading the CDN fonts and Tailwind (and those will be
gracefully missing if blocked).

---

## Tech notes

- **Single-file architecture** â€” `index.html` contains the head, all CSS
  inside one `<style>` block, all markup, and the entire game inside one
  `<script>` block. Easy to fork, embed, archive, or self-host.
- **Audio engine** â€” every sound (clicks, jackpots, spins, losses, the Crash
  rocket) is **synthesized at runtime** with `AudioContext` oscillators. No
  audio files to download.
- **Particle engine** â€” a fullscreen `<canvas>` overlay running on
  `requestAnimationFrame` draws coin showers, sparks, and floating
  `+250`-style text on every win.
- **Animations** â€” pure CSS keyframes for reels, anticipation glow, near-miss
  ghosts, big-win rainbow strobe, casino game pieces (coin flip, dice roll,
  card draw, roulette spin, horse bob, crash shake, scratch reveal, fortune
  wheel, plinko peg glow, cockfighter lunges).
- **DPR-aware canvas** â€” the Plinko board renders at the device's native
  pixel ratio so pegs and balls stay crisp on retina screens.
- **No build step** â€” write code, save, refresh. The same workflow as 1996,
  but with modern CSS and ES2020 JavaScript.

---

## Controls reference

| Where | Input | Action |
|---|---|---|
| Slot machine | Click SPIN | Costs `spinCost` credits, rolls reels |
| Slot machine | Click GAMBLE | Opens double-or-nothing on the last win |
| Inventory tile | Click / long-press | Show symbol info, value, rarity |
| Perk tile | Click / long-press | Show perk effect |
| Title bar | Click "Secret Casino" | Opens the casino overlay |
| Casino sidebar | FLOOR 1 / 2 / 3 | Switch floors |
| Casino map | Click any room | Walks the marker, opens the table |
| Casino wager | Preset chip / CUSTOM | Set bet for every casino game |
| Casino room | Action buttons | Play the round |
| Snake (mobile) | On-screen D-pad | Steer the snake |
| Settings (âš™ï¸) | Sliders & toggles | Volume, zoom, particles, shake |

---

## License

This repository's source is released under the **MIT License** unless a
`LICENSE` file in the repo says otherwise. Emoji glyphs are rendered by the
operating system / browser and remain the property of their respective
designers.

---

> _Pay your rent. Survive the lease. The wheels keep turning._
