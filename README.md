# Walk on Bengaluru — Trivia Edition

An interactive, team-based trivia game about Bengaluru's history, culture, urban development, ecology, and governance. Runs entirely in a browser — no server or install required.

---

## How to Run

Open the link in a browser: https://github.com/blrdesigncentre/blrdesigncentre.github.io
> No build step, no dependencies to install. The only internet dependency is the Shoelace UI component library loaded via CDN — the game works offline once the page has loaded once.

---

## How to Play

### Starting a Game

- **New Game** — clears any saved state and starts fresh.
- **Resume Game** — appears only when a saved game exists in the browser. Restores all scores, answered questions, and round progress.

The game autosaves to the browser's `localStorage` after every action. Refreshing the page will not lose progress.

### Game Board

| Element | Description |
|---|---|
| Timeline bar | 5 rounds: T1–T4 are the main rounds, T5 is Rapid Fire. Click a round to filter the question grid. |
| Team scoreboard | 5 teams listed vertically. Each team has ▲ and ▼ buttons on the left — click them to move that team up or down one position. The number between the arrows shows the current order. |
| Question grid | 4 categories × 4 timelines = 16 tiles. Each tile holds 2 questions. |
| Timer | Default 25 seconds. Click the number to type a custom value, then press Start. |

### Taking a Turn

1. Select the active team in the scoreboard (it highlights).
2. Click a question tile on the grid.
3. The question overlay opens. Use **Show Clue** to reveal the clue image.
4. Use **Reveal Answer** when ready.
5. Mark the result:
   - **Correct** → +5 points.
   - **Wrong** → 0 points (or −5 if *Auto Surge* is active).
   - **Floor** → opens the question to all other teams to buzz in.

### Location Bonus

Some questions are marked with a location pin. After answering the question, the active team may bid points to guess which of five Bengaluru locations the answer relates to. A correct location guess awards **+40 points**.

### City Shuffle (Power-Up Cards)

Certain question tiles trigger a City Shuffle card. A random card is drawn and its effect applies immediately for that turn. The 10 cards are:

| Card | Effect |
|---|---|
| Auto Surge | Lose 5 points if the answer is wrong. |
| Gridlock | Choose a team — they cannot buzz in to steal this turn. |
| Signal Jump | Pick a team — they lose 5 points instantly. |
| Tender Coconut Break | The active team gets one free retry if their first answer is wrong. |
| Kempegowda Stamp | Active team gets +5 points regardless of outcome. |
| Gossip Katte | Facilitators give a hint — but they might mislead you! |
| Shortcut | Swap the chosen question for any other on the board. |
| Friendly Passenger | Pick another team — they also score bonus points if you answer correctly. |
| Sudden Shower | No floor steal allowed. The question is lost if the active team misses. |
| Pothole | The team to your right chooses who on your team must answer. |

### Rapid Fire (Round 5)

Select a team and they answer 5 rapid-fire questions in a row. Each correct answer scores **10 points** (maximum 50). Questions are pre-assigned per bucket to each team position.

---

## Updating Content

All game content lives in three CSV files inside [`collateral/questions/`](collateral/questions/). Edit them in Excel or any spreadsheet app and save as CSV (UTF-8). The Excel source files (`.xlsx`) are included as authoring backups.

---

### `question-bank.csv` — Main Questions

32 questions: 4 categories × 4 timelines × 2 questions each.

| Column | Values | Notes |
|---|---|---|
| `category` | `culture`, `urban`, `ecology`, `governance` | Must be lowercase, no spaces. |
| `round` | `t1`, `t2`, `t3`, `t4` | Corresponds to the 4 timeline periods. |
| `question number` | `1` or `2` | Two questions per tile. |
| `code` | e.g. `c_t1_1` | Must be unique. Format: first letter of category + `_` + round + `_` + number. |
| `question` | Question text | Plain text. |
| `clue` | e.g. `c_t1_1.png` | Filename of the clue image in [`collateral/clues/`](collateral/clues/). Must match exactly. |
| `answer` | Answer text | Plain text. |
| `location bonus` | `true` or `false` | Set `true` to trigger the location bidding mechanic after this question. |

**To add or replace a question:**
1. Edit the row in `question-bank.csv`.
2. If the clue image changes, replace the PNG in `collateral/clues/` using the same filename as the `clue` column.
3. Save the CSV. Reload the browser.

---

### `rapid-fire-bank.csv` — Rapid Fire Questions

25 questions across 5 buckets, one bucket per team position.

| Column | Values | Notes |
|---|---|---|
| `id` | e.g. `rb1_1` | Unique ID. Format: bucket + `_` + question number. |
| `no` | `1`–`5` | Question number within the bucket. |
| `bucket` | `rb1`–`rb5` | One bucket per team. rb1 goes to the team in position 1, rb2 to position 2, etc. |
| `question` | Question text | Plain text. |
| `answer` | Answer text | Plain text. |

**To update rapid fire questions:** edit the relevant row, keep the bucket and number consistent, save as CSV.

---

### `city-shuffle.csv` — Power-Up Cards

10 power-up cards drawn at random during gameplay.

| Column | Values | Notes |
|---|---|---|
| `id` | `1`–`10` | Numeric ID. Must be unique. |
| `effect` | Card name | Displayed as the card title. |
| `detail` | Description text | Shown to players when the card is drawn. |
| `icon` | e.g. `auto-surge.png` | Filename in [`collateral/shuffle-icons/`](collateral/shuffle-icons/). |

The game logic for each card is hard-coded in `main.js`. Changing the `detail` text only updates what players read — it does not change the actual mechanic. To change a mechanic, the corresponding code in `main.js` must also be updated.

---

## File Reference

```
walk_on_bengaluru_game/
│
├── index.html                  — Game UI (all screens and overlays)
├── main.js                     — All game logic, state, and CSV loading
├── style.css                   — All styling and theming
│
└── collateral/
    ├── questions/
    │   ├── question-bank.csv       — 32 main trivia questions
    │   ├── question-bank.xlsx      — Excel source for question-bank.csv
    │   ├── rapid-fire-bank.csv     — 25 rapid fire questions
    │   ├── rapid-fire-bank.xlsx    — Excel source for rapid-fire-bank.csv
    │   ├── city-shuffle.csv        — 10 power-up card definitions
    │   └── city-shuffle.xlsx       — Excel source for city-shuffle.csv
    │
    ├── clues/
    │   └── [code].png              — Clue images, one per question (e.g. c_t1_1.png)
    │
    ├── shuffle-icons/
    │   └── [card-name].png         — Icons for each City Shuffle card
    │
    ├── audio/
    │   ├── intro.mp3               — Plays on game start
    │   ├── timer.mp3               — Looping countdown sound
    │   ├── round.mp3               — Round transition
    │   ├── cheer.mp3               — Correct answer
    │   └── wrong.mp3               — Wrong answer
    │
    ├── png/1x/
    │   ├── background.png          — Main background image
    │   ├── blr_play.png            — Game logo
    │   ├── bdc_logo.png            — Bengaluru Design Centre logo
    │   ├── mod_logo.png            — MOD logo
    │   ├── culture-card.png / culture-icon.png
    │   ├── urban-card.png / urban-icon.png
    │   ├── ecology-card.png / ecology-icon.png
    │   ├── governance-card.png / governance-icon.png
    │   ├── locate-card.png         — Location bonus indicator
    │   └── shuffle-card.png / shuffle-card-back.png
    │
    └── svg/
        ├── maja_maadi_default.svg  — "Let's Play" button (default state)
        └── maja_maadi_hover.svg    — "Let's Play" button (hover state)
```

---

## Updating the Codebase (Git)

The repository is hosted on GitHub. To push updates:

```bash
# Check what has changed
git status

# Stage your changes
git add collateral/questions/question-bank.csv
# (or: git add . to stage everything)

# Commit with a message
git commit -m "Update question bank: replace t2 culture questions"

# Push to GitHub
git push
```

To pull the latest version from GitHub onto another machine:

```bash
git pull
```

Always reload the browser after pulling or editing CSV/asset files to pick up the changes.

---

## Teams

The five default teams are named after Bengaluru neighbourhoods:

1. Chickpete
2. Malleshwaram
3. Electronic City
4. Shivajinagar
5. Indiranagar

Team names are defined in `main.js` and can be changed there if needed.

---

## Browser Compatibility

Works in any modern browser. Tested in Chrome and Edge. Requires JavaScript enabled. No internet connection needed after the first page load (Shoelace loads from CDN on first visit only).
