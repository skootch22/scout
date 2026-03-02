# ⚾ Baseball Scouting Report

A mobile-first, single-file web application for generating team scouting reports from Google Sheets data. No server, no build tools, no dependencies — just one HTML file deployed to GitHub Pages.

---

## Features

- **Instant reports** — select a team from the dropdown and a full scouting report generates automatically
- **Google Sheets integration** — data lives in your spreadsheet; the app fetches it directly via CSV export
- **Pitching analysis** — per-pitcher rollup across all outings with K/9, BB/9, Strike%, BB/K, P/IP, IP/App, and color-coded grade dots
- **Batting analysis** — team AVG, OBP, and SO/BB for the full season
- **Stolen base tracking** — SB allowed and CS% tracked at the game level and surfaced in the pitching strip
- **Common opponent comparison** — flag specific games to compare performance against a shared opponent across pitching and batting
- **Team record** — W-L-T record, run differential, and run diff per game pulled automatically from scores in your sheet
- **Key Findings** — auto-generated strengths and concerns labeled by Pitching or Batting
- **Mobile-first** — designed for sideline use on a phone; fully responsive up to desktop

---

## Setup

### 1. Deploy the app

Upload `baseball-scout.html` to GitHub Pages (or any static host):

1. Create a GitHub repository
2. Add `baseball-scout.html` to the repo root
3. Go to **Settings → Pages → Source** and select your main branch
4. Your app will be live at `https://yourusername.github.io/your-repo/baseball-scout.html`

### 2. Set up your Google Sheet

Create a Google Sheet with the following tab structure for each team. Tab names must follow the exact pattern `TeamName - TabName`.

#### Teams tab (named exactly `Teams`)

Lists all teams. Only teams marked Active will appear in the dropdown.

| Team | Active |
|---|---|
| Riverdale Fall Ball | 1 |
| Lake County Captains | 1 |
| Toledo Mud Hens | 0 |

- **Active**: `1`, `yes`, `y`, or `true` = shown in dropdown. Anything else = hidden.
- If the Active column is omitted, all teams are shown.

#### TeamName - Games

Master game log. Drives opponent names, common opponent flags, team record, and stolen base tracking.

| Game | Opponent | Common | Score | Opp Score | SB | CS |
|---|---|---|---|---|---|---|
| 1 | Toledo | 0 | 5 | 3 | 2 | 1 |
| 2 | Lake County | 1 | 2 | 7 | 0 | 0 |
| 3 | Lake County | 1 | 4 | 4 | 1 | 2 |

- **Common**: `1` = flag this game as a common opponent game for comparison purposes
- **Score / Opp Score**: used to calculate W-L-T record and run differential. Leave blank if unknown — those games are excluded from the record.
- **SB**: stolen bases allowed by the pitching staff that game
- **CS**: runners caught stealing that game. CS% = CS / (SB + CS). Leave blank if not tracked — the stat will show `—` rather than an incorrect zero.

#### TeamName - Pitching

One row per pitcher per outing. The app automatically rolls up multiple outings per jersey number.

| Game | Number | IP | Pitches | Strikes | BB | K |
|---|---|---|---|---|---|---|
| 1 | 12 | 4.2 | 68 | 44 | 2 | 5 |
| 1 | 23 | 2.1 | 34 | 22 | 1 | 3 |
| 2 | 12 | 3.0 | 51 | 33 | 3 | 4 |

- **IP**: use baseball notation — `4.2` means 4 innings and 2 outs, not 4.2 decimal innings
- **Game**: must match the Game values in your Games tab for common opponent filtering to work

#### TeamName - Batting

One row per game with team totals.

| Game | AB | R | H | RBI | BB | SO |
|---|---|---|---|---|---|---|
| 1 | 28 | 5 | 9 | 4 | 3 | 6 |
| 2 | 27 | 2 | 5 | 2 | 1 | 8 |

- **Game**: must match the Game values in your Games tab

### 3. Publish your Google Sheet

The app fetches data via Google's CSV export endpoint, which requires the sheet to be published:

1. In Google Sheets, go to **File → Share → Publish to web**
2. Select **Entire Document** and **Web page**
3. Click **Publish**

> Note: "Published to web" is separate from "Anyone with the link can view." You need both.

### 4. Hardcode your sheet ID

Open `baseball-scout.html` in a text editor and find this line near the top of the `<script>` block:

```javascript
let sheetId = 'YOUR_SHEET_ID_HERE'; // hardcoded
```

Replace `YOUR_SHEET_ID_HERE` with the ID from your Google Sheet URL:

```
https://docs.google.com/spreadsheets/d/SHEET_ID_IS_HERE/edit
```

---

## Column Name Flexibility

Column header names are matched loosely — order doesn't matter and capitalization is ignored. Accepted aliases:

| Field | Accepted names |
|---|---|
| Game | `Game`, `Gm`, `G` |
| Opponent | `Opponent`, `Opp` |
| Common | `Common`, `Co`, `Flag` |
| Score | `Score`, `RS`, `Runs Scored`, `Our Score` |
| Opp Score | `Opp Score`, `RA`, `Runs Allowed`, `Opponent Score`, `Opp Sc` |
| SB | `SB`, `Stolen Base`, `Stolen Bases Allowed`, `SBA` |
| CS | `CS`, `Caught Stealing` |
| Number | `Number`, `Num`, `Jersey`, `No` |
| IP | `IP`, `Innings` |
| Pitches | `Pitches`, `Pitch` |
| Strikes | `Strikes`, `Strike` |
| BB | `BB`, `Walks`, `Walk` |
| K (pitching) | `K`, `Strikeout`, `SO` |
| AB | `AB`, `At Bat` |
| R | `R`, `Runs`, `Run` |
| H | `H`, `Hits`, `Hit` |
| SO (batting) | `SO`, `Strikeout`, `K` |

---

## Report Sections

The generated report includes:

1. **Masthead** — team name, W-L-T record, run differential, and per-game run differential
2. **Season Batting strip** — team AVG, OBP, SO/BB
3. **Common Opponent Batting strip** — same stats filtered to flagged games *(if applicable)*
4. **Pitching strip** — staff Strike Rate, BB/K, K/9, CS%
5. **Common Opponent Pitching strip** — same stats filtered to flagged games *(if applicable)*
6. **Pitching Staff cards** — per-pitcher breakdown with IP, K, BB, Str%, BB/K, K/9, P/IP, IP/App, and grade dot
7. **Key Findings** — auto-generated strengths and concerns labeled as Pitching or Batting

---

## Grading Scale

Each pitcher receives a color-coded dot based on a composite score across K/9, BB/9, BB/K, and Strike%:

| Grade | Color | Score |
|---|---|---|
| A | Dark green | 7–8 |
| B | Medium green | 5–6 |
| C | Golden yellow | 3–4 |
| D | Brown | 1–2 |
| F | Red | 0 |

---

## Tech Stack

- **Vanilla HTML/CSS/JavaScript** — no frameworks, no build step
- **Google Sheets CSV export** — data source via `/gviz/tq?tqx=out:csv` endpoint
- No external dependencies at runtime (Google Fonts loaded for typography)

---

## Notes

- The sheet ID is hardcoded in the file. To use a different sheet, update the `sheetId` variable.
- Google Sheets has a limit of 200 tabs per spreadsheet (~66 teams with 3 tabs each).
- Ties in the W-L-T record are supported and only shown when at least one tie exists.
- All score, SB, and CS fields are optional — missing data shows `—` rather than skewing calculations.
